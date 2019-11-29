---
title: 将精算风险分析迁移到 Azure 的指南
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 精算开发人员如何将现有解决方案和支持基础设施迁移到 Azure。
ms.openlocfilehash: 456c054cf3a6165f160005ba8ea2c155637faa07
ms.sourcegitcommit: f030566b177715794d2ad857b150317e72d04d64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234537"
---
# <a name="actuarial-risk-analysis-and-financial-modeling-solution-guide"></a>精算风险分析和金融建模解决方案指南

在过去几年中，保险公司和提供保险类产品的公司看到一些新法规的出台。 这些新法规要求保险公司进行更广泛的金融建模。 欧盟颁布了[偿付能力 II](https://eur-lex.europa.eu/legal-content/EN/ALL/?uri=celex%3A32009L0138)，要求保险公司证明他们已经进行了适当的分析，以证实保险公司在年底将具备偿付能力。 提供可变年金的保险公司必须遵循[精算准则 XLIII](https://eur-lex.europa.eu/legal-content/EN/ALL/?uri=celex%3A32009L0138)，并对资产和负债现金流进行广泛分析。 所有类型的保险公司，包括分销保险类产品的保险公司，都必须在 2021 年之前实施[国际财务报告准则第 17 号](https://www.ifrs.org/supporting-implementation/supporting-materials-by-ifrs-standard/ifrs-17/) (IFRS 17)。 根据保险公司运营所在的司法管辖区不同，会存在其他法规。 这些标准和法规要求精算师在对资产和负债进行建模时使用计算密集型技术。 许多分析将利用随机生成的方案数据，而不是资产和负债等内容的连续输入。 除了监管需求外，精算师还会进行相当多的财务建模和计算，以针对生成监管报告的模型生成输入表。 内部网格不能满足计算需求，因此精算师正在稳步迁移到云。

精算师迁移到云，以便有更多时间来审查、评估和验证结果。 当监管机构对保险公司进行审计时，精算师需要能够解释其结果。 迁移到云可以访问计算资源，通过强大的并行化能力，在 24-120 小时的时钟时间内运行 20000 小时的分析。 为了满足这种规模需求，许多创建精算软件的公司提供了允许在 Azure 中运行计算的解决方案。 其中一些解决方案基于本地运行的技术和 Azure（如 [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview?view=hpc16-ps&WT.mc_id=riskmodel-docs-scseely)）构建。 其他是 Azure 本机的，使用 [Azure Batch](https://docs.microsoft.com/azure/batch?WT.mc_id=riskmodel-docs-scseely)、[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets?WT.mc_id=riskmodel-docs-scseely)，或自定义缩放解决方案。

在本文中，我们将介绍精算开发人员如何使用 Azure 并搭配建模包来分析风险。 本文介绍建模包用于在 Azure 上大规模运行的一些 Azure 技术。 可以使用相同的技术对数据进行进一步分析。 我们来看看以下各项：

- 在 Azure 中以更短的时间运行更大的模型。
- 报告结果。
- 管理数据保留。

无论是为寿险、财产和意外、健康还是其他保险提供服务，都需要创建资产和负债的财务和风险模型，以调整投资和保费，从而作为保险公司能保持偿付能力。 IFRS 17 报告为精算师创建的模型增加了变化，例如计算合同服务边际 (CSM)，从而改变了保险公司随时间管理其利润的方式。

## <a name="running-more-in-less-time-in-azure"></a>在 Azure 中以更短的时间运行更多内容

相信云的承诺：它可以更快、更轻松地运行财务和风险模型。 对于许多保险公司而言，封底计算有一个问题：他们需要数年甚至数十年的连续时间来从头到尾运行这些计算。 需要技术来解决运行时问题。 策略包括：

- 数据准备：某些数据会缓慢更改。 一旦政策或服务协定生效，声明就会以可预测的速度迁移。 可以准备在模型到达时运行所需的数据，从而无需为数据清理和准备规划大量时间。 还可以使用群集通过加权的表示形式为连续数据创建备用项。 较少的记录通常会使计算时间缩短。
- 并行化：如果需要对两个或更多项进行相同的分析，可以同时执行分析。

让我们分别来看看这些项。

### <a name="data-preparation"></a>数据准备

数据从多个不同的来源流入。 你的业务簿中包含半结构化的政策数据。 有关被保险人、公司和各种申请表中的条目的信息。 经济方案生成器 (ESG) 生成各种格式的数据，这些格式可能需要转换为模型可以使用的形式。 有关资产值的当前数据也需要规范化。 股票市场数据、租金现金流数据、抵押贷款支付信息以及其他资产数据在从源头转移到模型时都需要做一些准备。 最后，应该根据最近的经验数据更新任何假设。 要加快模型运行，请提前准备数据。 当运行时发生时，需要执行任何必要的更新以添加自上次计划内更新以来的更改。

那么，如何准备数据？ 让我们首先看看公共位，然后看看如何使用数据显示的不同方式。 首先，需要一种机制来获取自上次同步以来的所有更改。 该机制应包括一个可排序的值。 对于最近的更改，该值应大于以前的任何更改。 最常见的两种机制是不断增加的 ID 字段或时间戳。 如果记录具有不断增加的 ID 键，但记录的其余部分包含可更新的字段，则需要使用类似&quot;上次修改的&quot;时间戳的内容来查找更改。 记录处理完毕后，记下最后更新项的可排序值。 此值（可能是名为 _lastModified_ 的字段上的时间戳）将成为水印，用于数据存储上的后续查询。 可以通过多种方式处理数据更改。 以下是使用资源最少的两种常见机制：

1. 如果要处理数百或数千个更改：将数据上传到 blob 存储。 使用 [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory?WT.mc_id=riskmodel-docs-scseely)中的一个事件触发器来处理变更集。
2. 如果要处理少量更改，或希望在更改发生后立即更新数据，请将每个更改放入由[服务总线](https://docs.microsoft.com/azure/service-bus-messaging?WT.mc_id=riskmodel-docs-scseely)或[存储队列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction?WT.mc_id=riskmodel-docs-scseely)承载的队列消息中。 [本文](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted?WT.mc_id=riskmodel-docs-scseely)对这两种队列技术之间的权衡进行了很好的解释。 一旦消息进入队列后，可以使用 Azure Functions 或 Azure 数据工厂中的触发器来处理消息。

下图演示了一个典型方案。 首先，计划的作业收集一些数据集并将文件放入存储中。 计划的作业可以是本地运行的 CRON 作业、[计划程序任务](https://docs.microsoft.com/azure/scheduler?WT.mc_id=riskmodel-docs-scseely)、[逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview?WT.mc_id=riskmodel-docs-scseely)或在计时器上运行的任何内容。 上载文件后，可以触发 [Azure Function](https://docs.microsoft.com/azure/azure-functions?WT.mc_id=riskmodel-docs-scseely)或**数据工厂**实例来处理数据。 如果可以在短时间内处理该文件，请使用 Function  。 如果处理很复杂，需要 AI 或其他复杂的脚本，你可能会发现 [HDInsight](https://docs.microsoft.com/azure/hdinsight?WT.mc_id=riskmodel-docs-scseely)、[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks?WT.mc_id=riskmodel-docs-scseely) 或自定义内容更有效。 完成后，该文件最终以可用的形式成为新文件或数据库中的记录。

 ![](./assets/insurance-risk-assets/process-files.png)

数据进入 Azure 后，需要使其可供建模应用程序使用。 可以编写代码来执行自定义转换，通过 HDInsight  或 Azure Databricks  运行项目以引入较大的项，或将数据复制到正确的数据集中。 使用大数据工具还可以帮助完成将非结构化数据转换为结构化数据以及在接收到的数据上运行任何 AI 和 ML 等操作。 还可以托管虚拟机，从本地将数据直接上载到数据源，直接调用 Azure Functions 等等。

稍后，模型需要用到这些数据。 执行此操作的方式在很大程度上取决于计算访问数据所需的方式。 某些建模系统要求所有数据文件都位于运行计算的节点上。 其他可以使用 [Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/?WT.mc_id=riskmodel-docs-scseely)、[MySQL](https://docs.microsoft.com/azure/mysql/?WT.mc_id=riskmodel-docs-scseely) 或 [PostgreSQL](https://docs.microsoft.com/azure/postgresql/?WT.mc_id=riskmodel-docs-scseely) 等数据库。 可以使用上述项目中的任何一个低成本版本，然后在建模运行期间扩展性能。 这为你提供了日常工作所需的成本，以及在数千个内核请求数据时的额外速度。 通常，此数据在建模运行期间将是只读的。 如果计算发生在多个区域中，请考虑使用 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/distribute-data-globally?WT.mc_id=riskmodel-docs-scseely) 或 [Azure SQL 异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview?WT.mc_id=riskmodel-docs-scseely)。 两者都具有以低延迟跨区域自动复制数据的机制。 你的选择取决于开发人员所知道的工具、对数据进行建模的方式以及用于建模运行的区域数量。

一定要花些时间考虑存储数据的位置。 了解同时存在多少个对相同数据的请求。 考虑将如何分发信息：

- 每个计算节点是否都有自己的副本？
- 副本是否通过某个高带宽位置共享？

如果使用 Azure SQL 将数据集中化，则可能会在大多数情况下将数据库保持在价格较低的层。 如果数据仅在建模运行期间使用且不经常更新，则 Azure 客户将尽可能地备份数据并在两次运行之间关闭其数据库实例。 潜在的节约是巨大的。 客户还可以利用 [Azure SQL 弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool?WT.mc_id=riskmodel-docs-scseely)。 这些旨在控制数据库成本，尤其是不知道哪些数据库将在不同时间承受大量负载时。 弹性池允许数据库集合尽可能多地使用所需的功能，然后在需求转移到系统中的其他位置时缩减。

可能需要在建模运行期间禁用数据同步，以便稍后在该过程中的计算使用相同的数据。 如果使用队列，请禁用消息处理器，但允许队列接收数据。

还可以使用运行前的时间生成经济方案、更新精算假设以及通常更新其他静态数据。 让我们来看看经济方案生成 (ESG)。 [精算师协会](https://www.soa.org/)提供了[学院利率生成器](https://www.soa.org/tables-calcs-tools/research-scenario/) (AIRG)，这是一个模拟美国 国债收益率的 ESG。 AIRG 规定用于评估手册20 (VM-20) 计算等项。 其他 ESG 可能会对股票市场、抵押贷款、商品价格等进行建模。

由于你的环境正在预处理数据，因此你还可以提前运行其他部分。 例如，可能会有一些 yv1ou 模型使用记录来表示更大的总体的内容。 通常通过对记录进行群集处理来执行此操作。 如果偶尔更新数据集，例如每天更新一次，则可以将记录集减少到在引入过程中将在模型中使用的记录集。

让我们来看一个实例。 根据 IFRS-17，需要将合同组合在一起，以使任何两个合同的开始日期之间的最大距离不到一年。 让我们假设你以简单的方式执行此操作，并使用合同年作为分组机制。 通过读取文件并将记录移动到适当的年份分组，可以在将数据加载到 Azure 时完成此分段。

专注于数据准备可缩短运行模型组件所需的时间。 通过尽早获取数据，可以节省运行模型的时钟时间。

### <a name="parallelization"></a>并行化

适当的步骤并行化可以极大地缩短时钟执行时间。 通过简化实现的部分并了解如何以允许两个或多个活动同时运行的方式表达模型可实现此加速。 技巧就是在工作请求的大小和单个节点的工作效率之间找到平衡。 如果任务在设置和清理上花费的时间比在评估上花费的时间多，则表明任务太小。 如果任务太大，则执行时间不会缩短。 你希望活动足够小以分布在多个节点上，并对执行时间产生积极的影响。

要充分利用系统，需要了解模型的工作流以及计算与扩展能力的相互作用。软件可能具有作业、任务或类似内容的概念。 利用这一知识对工作进行拆分设计。 如果模型中有一些自定义步骤，请将其设计为允许将输入拆分为较小的组进行处理。 通常，这被称为分散-聚集模式。

- 分散：沿自然线拆分输入并允许单独的任务运行。
- 聚集：任务完成后，收集其输出。

拆分时，还要知道进程在前进之前需要在何处同步。 有一些常见的拆分位置。 对于嵌套的随机运行，可能有一千个外部循环，其中一组拐点运行一百个方案的内部循环。 每个外部循环可以同时运行。 在拐点处停止，然后同时运行内部循环，返回信息以调整外部循环的数据，然后再次前进。 下图演示了该工作流。 如果有足够的计算，可以在 100,000 个核心上运行 100,000 个内部循环，将处理时间缩短到以下时间之和：

![](./assets/insurance-risk-assets/timing.png)

分发会增加一点，具体取决于完成方式；它可能像使用正确的参数构建小型作业一样简单，也可能像将 100K 文件复制到正确的位置那样复杂。 如果可以使用 HD Insight 中的 Apache Spark、Azure Databricks 或你自己的部署来分发结果聚合，则甚至可以加快处理结果。 例如，计算平均值是一个简单的记住到目前为止看到的项数与总和的问题。 其他计算可能在具有数千个核心的单台计算机上效果更好。 对于这些，可以在 Azure 中使用支持 GPU 的计算机。

大多数精算团队通过将其模型迁移到 Azure 来开始这一旅程。 然后，他们收集流程中各个步骤的计时数据。 随后，他们将每个步骤的时钟时间按从最长到最短的运行时间进行排序。 他们不会查看总执行时间，因为某些内容可能会消耗数千个核心小时数，但只有 20 分钟的运行时间。 对于每个运行时间最长的作业步骤，精算开发人员都会寻找既能减少运行时间又能获得正确结果的方法。 此过程会定期重复。 一些精算团队将设定目标运行时间，假设隔夜对冲分析的目标是在 8 小时内运行完成。 一旦时间超过 8.25 小时，精算团队的某些部分将切换以改善分析中最长部分的时间。 一旦他们将时间重新缩短到 7.5 小时内，他们就会切换回开发。 精算师返回和优化的启发方式会有所不同。

要运行所有这些，有几个选项。 大多数精算软件都适用于计算网格。 在本地和 Azure 上工作的网格使用 [HPC Pack](https://docs.microsoft.com/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=riskmodel-docs-scseely)第三方包或自定义内容。 针对 Azure 优化的网格将使用[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/?WT.mc_id=riskmodel-docs-scseely)、[Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=riskmodel-docs-scseely) 或自定义内容。 如果选择使用规模集或 Batch，请确保查看其对低优先级 VM 的支持（[规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-low-priority?WT.mc_id=riskmodel-docs-scseely)低优先级文档、[Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms?WT.mc_id=riskmodel-docs-scseely) 低优先级文档）。 低优先级 VM 是在硬件上运行的 VM，可以正常价格的一小部分租用。 提供较低价格的原因是，当容量需要时，低优先级 VM 可能被占用。 如果时间预算有一些调整空间，则低优先级 VM 提供了降低建模运行价格的好方法。

如果需要协调多台计算机上的执行和部署，可能有一些在不同区域运行，你可以利用 CycleCloud。 CycleCloud 无需额外费用。 它可在必要时协调数据移动。 这包括计算机的分配、监控和关闭。 它甚至可以处理低优先级的计算机，确保费用得到控制。 你甚至可以描述需要的计算机组合。 例如，你可能需要一类计算机，但可以在任何具有 2 个或多个核心的版本上运行良好。 Cycle 可以跨这些计算机类型分配核心。

## <a name="reporting-on-the-results"></a>报告结果

一旦精算包运行并产生了结果，你将获得几个可提交给监管机构的报告。 你还会有大量新数据，你可能需要对其进行分析以生成监管机构或审计员不需要的其他见解。 你可能需要了解最佳客户的个人资料。 通过这些见解，可以告诉营销人员低成本客户的特点，以便营销和销售人员能够更快地找到他们。 同样，可以使用数据来发现哪些组从拥有保险中获益最多。 例如，你可能会发现进行年度体检的人更早发现了早期健康问题。 这可为保险公司节省时间和资金。 你可以使用此数据来推动客户群的行为。

要做到这一点，需要访问大量的数据科学工具以及可视化效果的某些部分。 根据要进行的调查量，可以从可通过 Azure 市场预配的 [ Data Science VM](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview?WT.mc_id=riskmodel-docs-scseely) 开始。 这些虚拟机具有 Windows 和 Linux 版本。 安装后，你会发现 Microsoft R Open、Microsoft ML Server、Anaconda、Jupyter 和其他工具已准备就绪。 投入一点 R 或 Python 以实现数据可视化并与同事共享见解。

如果需要进行更多分析，可以通过 HDInsight 或 Databricks 使用 Spark、Hadoop 等 Apache 数据科学工具。 当需要定期进行分析并且希望自动执行工作流时，需多使用这些。 这对于大型数据集的实时分析也很有用。

一旦找到一些有趣的内容，就需要显示结果。 许多精算师将首先获取示例结果并将其插入 Excel 中以创建图表、图形和其他可视化效果。 如果需要一个同样有良好界面来钻取数据的工具，不妨看看 [Power BI](https://docs.microsoft.com/power-bi/?WT.mc_id=riskmodel-docs-scseely)。 Power BI 可以提供一些很好的可视化效果，显示源数据，并允许通过添加[有序、带批注的书签](https://docs.microsoft.com/power-bi/desktop-bookmarks?WT.mc_id=riskmodel-docs-scseely)向读者解释数据。

## <a name="data-retention"></a>数据保留

引入系统的大部分数据都需要保留以供将来审核。 通常要求数据保留 7 到 10 年，具体要求各不相同。 保留内容最少包括：

- 模型原始输入的快照。 这包括资产、负债、假设、ESG 和其他输入。
- 最终输出的快照。 这包括用于创建向监管机构提交的报告的任何数据。
- 其他重要的中间结果。 审计员会问为什么你的模型会得出一些结果。 你需要保留有关模型为何做出某些选择或提供特定数字的证据。 许多保险公司将选择保留用于从原始输入生成最终输出的二进制文件。 之后，当被问及时，他们会重新运行模型以获得中间结果的新副本。 如果输出匹配，则中间文件还应包含所需的解释。

在模型运行期间，精算师使用数据传递机制来处理运行中的请求负载。 一旦运行完成并且不再需要数据，他们就会保留一些数据。 至少，保险公司应保留输入和运行时配置以满足任何可再现性要求。 数据库将保存到 Azure Blob 存储中的备份，并且服务器将关闭。 高速存储上的数据也将移动到成本较低的 Azure Blob 存储。 进入 Blob 存储后，可以选择用于每个 blob 的数据层：热、冷或存档。 热存储适用于频繁访问的文件。 冷存储针对不频繁的数据访问进行了优化。 存档存储最适合保存可审核的文件，但节省的价格需要付出延迟成本：存档层数据延迟以小时为单位。 阅读 [Azure Blob 存储：热、冷和存档存储层](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=riskmodel-docs-scseely)以全面了解不同的存储层。 可以使用生命周期管理来管理数据从创建到删除的整个过程。 blob 的 URI 保持静态，但 blob 存储的位置会随着时间的推移而变得更便宜。 此功能将为众多 Azure 存储用户节省大量资金并减少许多麻烦。 可以在[管理 Azure Blob 存储生命周期](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts?WT.mc_id=riskmodel-docs-scseely)中了解详细信息。 可以自动删除文件的功能非常棒：这意味着你不会因为引用超出范围的文件而意外扩展审核，因为文件本身可以自动删除。

## <a name="next-steps"></a>后续步骤

如果运行的精算系统具有本地网格实现，则该网格实现也可能在 Azure 上运行。 对于某些供应商，他们拥有以超大规模运行的专用 Azure 实现。 作为迁移到 Azure 的一部分，也可以将内部工具迁移过来。 各地的精算师都发现，他们的数据科学技能在其笔记本电脑或大环境中效果很好。 看看你的团队已经做了哪些：也许你有一些东西使用深度学习，但需要花费数小时或数天才能在一个 GPU 上运行。 尝试在具有 4 个高端 GPU 的计算机上运行相同的工作负荷，并查看运行时间；很有可能你会看到显著的加速效果。

随着情况的改进，请确保还构建出一些数据同步以提供建模数据。 在数据准备就绪之前，无法启动模型运行。 这可能需要添加一些工作，以便只发送已更改的数据。 实际的方法也取决于数据大小：更新几 MB 可能不是什么大问题，但减少千兆字节的上载量将大大加快速度。

### <a name="tutorials"></a>教程

- R 开发人员：[使用 Azure Batch 运行并行 R 模拟](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=riskmodel-docs-scseely)
- 介绍如何使用 Azure Functions 与存储交互的教程：[使用 Azure Functions 将图像上传到 Blob 存储](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database?tutorial-step=2&WT.mc_id=riskmodel-docs-scseely)
- 使用 Databricks 进行 ETL：[使用 Azure Databricks 提取、转换和加载数据](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?WT.mc_id=riskmodel-docs-scseely)
- 在 HDInsight 中进行 ETL：[使用 Apache Hive on Azure HDInsight 提取、转换和加载数据](https://docs.microsoft.com/azure/hdinsight/hdinsight-analyze-flight-delay-data-linux?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fhadoop%2FTOC.json&amp;bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&WT.mc_id=riskmodel-docs-scseely)
- Data Science VM 操作方法 (Linux)：[https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/linux-dsvm-walkthrough?WT.mc_id=riskmodel-docs-scseely)
- Data Science VM 操作方法 (Windows)：[https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/vm-do-ten-things](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/vm-do-ten-things?WT.mc_id=riskmodel-docs-scseely)
