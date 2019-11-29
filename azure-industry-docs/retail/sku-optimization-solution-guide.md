---
title: 使用 Azure ML 和分析为消费者品牌进行 SKU 优化
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 零售行业分类优化。 通过 AI 和 ML 见解优化 SKU。
ms.openlocfilehash: 22411776e830bb3c71f8c1277b30ec4331a3ef17
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308501"
---
# <a name="sku-optimization-for-consumer-brands-solution-guide"></a>消费者品牌 SKU 优化解决方案指南

## <a name="introduction"></a>介绍

零售商和消费者品牌关注的是确保他们拥有消费者希望在市场上购买的正确产品和服务。 也可以这样说，在寻求最大程度提高销售额时，产品（或产品组合）是购物体验的主要部分。 产品/服务的可用性（即库存）是消费者品牌持续关注的问题。

产品库存（又称 SKU 分类）是一个跨供应链和物流价值链的复杂话题。 在本文档中，我们侧重于从消费品角度优化 SKU 分类以最大化收益的问题。 可以通过开发算法回答以下问题来解决 SKU 分类优化的难题：

- 哪些 SKU 在给定市场或商店中表现最好？ 
- 哪些 SKU 应根据其性能分配给给定市场或商店？
- 哪些 SKU 性能较低，应替换为性能较高的 SKU？
- 关于我们的消费者和市场细分，我们还能获得什么其他见解？

## <a name="automate-decision-making"></a>自动执行决策制定

传统上，消费者品牌通过增加 SKU 组合中的 SKU 数量来满足消费者需求。 随着 SKU 数量激增和竞争加剧，据估计，90% 的收益归因于组合中仅 10% 的产品 SKU。 通常情况下，80% 的收益源自 20% 的 SKU。 并且该比率是提高盈利能力的候选要素。 

传统静态报告方法利用历史数据，这限制了见解。 最佳情况下，决策仍然通过手动制定和实现。 这意味着人为干预和处理时间。 随着人工智能 (AI) 和云计算的进步，我们可以使用高级分析来提供一系列的选择和预测。 这种自动化可改进结果和客户接收速度。

## <a name="sku-assortment-optimization"></a>SKU 分类优化

SKU 分类解决方案必须通过将销售数据分割成有意义和详细的比较，来处理数以百万计的 SKU。 该解决方案的目标是通过使用高级分析来调整产品分类，从而在每个销售点或商店最大程度地提高销售额。 第二个目标是消除缺货和改进分类。 财政目标是销售额增加 5% 到 10%。 为此，认知见解使人能够：

- 了解 SKU 项目组合性能并管理表现较差的 SKU。
- 优化 SKU 的分布以减少缺货。
- 了解新的 SKU 如何支持短期和长期战略。
- 从现有数据创建可重复、可缩放和可操作的见解。

## <a name="descriptive-analytics"></a>描述性分析

描述性模型汇总数据点，并了解可能影响产品销售的因素之间的关系。 这些信息可能会增加一些外部数据点，例如位置、天气、人口普查数据等。可视化效果通过解释数据来帮助用户获得见解。 然而，在执行此操作时，该人员仅限于了解在上一个销售周期中发生的情况，也可能是当前阶段发生的情况（具体取决于数据刷新的频率）。

在这种情况下，传统数据仓库和报告方法就足以了解，例如，在一段时间内表现最好和最差的 SKU 是什么。

下图显示了历史数据的典型报告，即销售数据。 它有几个带有复选框的块来选择筛选结果的条件。 该中心显示了两个条形图，显示了销售额随时间推移的变化。 第一个图表显示每周的平均销售额；第二个图表显示每周的销售量。

 ![显示历史销售数据的仪表板示例。](assets/sku-optimization-solution-guide/sku-max-model.png)
  
## <a name="predictive-analytics"></a>预测分析

历史记录报告对于了解所发生的情况很有用。 最终，我们希望对可能发生的情况有一个预测。 过去的信息可能对于实现此目的很有用。 例如，我们可以确定季节性趋势。 但不能涵盖“假设”应用场景，例如，对新产品的引入进行建模。 为此，我们必须把注意力转移到为客户行为建模上，因为这是决定销售的最终因素。

### <a name="an-in-depth-look-at-the-problem-choice-models"></a>此问题的深入探讨：选择模型

让我们从定义我们要查找的内容和拥有的数据开始：

分类优化指的是找到一个投入销售的产品子集，以最大化预期收益。 这正是我们所寻求的。

出于财务目的，我们会定期收集交易数据  。 

**分类数据**潜在地包含与 SKU 相关的所有信息：下面是我们所需信息的示例： 

- SKU 的数量
- SKU 描述
- 分配的数量
- SKU 和购买数量
- 事件（例如，购买）时间戳
- SKU 价格
- POS 的 SKU 价格
- 在任何时间点的每个 SKU 的存货水平

遗憾的是，收集的此类数据不如交易数据可靠。 

在本文中，为简单起见，我们将只考虑交易数据和 SKU 数据，而不考虑外部因素。

即便如此，也请注意，给定一组 n 个产品，有 2n 个可能的分类。 这使得优化问题成为一个计算密集型过程。 对于大量产品而言，评估所有可能的组合是不切实际的。 因此，分类通常按类别（例如谷类）、位置和其他条件进行划分，以减少变量数量。 优化模型试图将排列数“修剪”到一个可行的子集。

问题的关键在于对消费者的行为进行有效建模  。 理想情况下，呈现给他们的产品会和他们想要购买的产品相匹配。

预测消费者选择的数学模型已经开发了几十年。 所选的模型将最终确定最适合的实现技术；因此，我们将进行汇总并提供几个注意事项。

## <a name="parametric-models"></a>参数模型

参数模型通过使用具有有限参数集的函数来粗略估计客户行为。 我们估计了一组最适合我们所处理数据的参数。 其中最早且众所周知的一个参数是[多项逻辑回归](https://en.wikipedia.org/wiki/Multinomial_logistic_regression)（又名 MNL、多类 logit 或 softmax 回归）。 它用于计算分类问题中几种可能结果的概率。 在这种情况下，可以使用 MNL 来计算：

- 消费者 (c) 在特定时间 (t) 选择一个产品 (i) 的概率，指定分类 (a) 中该类别的一组产品，并向客户 (v) 提供一个已知实用程序。 

我们还假设产品的实用程序可以是其功能函数。 外部信息也可以包括在实用程序的度量值中（例如，伞在下雨时更有用）。

由于 MNL 在参数估计和结果评估方面的易处理性，我们通常将其作为其他模型的基准。 换而言之，如果你做的比 MNL 差，你的算法就没有用处。
已有几个模型从 MNL 中派生出来，但不在本文讨论范围内。 

提供了 R 和 Python 编程语言库。 对于 R，你可能会使用 glm（和派生词）。  对于 Python，可以使用 [scikit-learn](http://scikit-learn.org/stable/)、[biogeme](http://biogeme.epfl.ch/) 和 [larch](https://pypi.org/project/larch/)。 这些库提供了指定 MNL 问题的工具，以及在各类平台上寻找解决方案的并行求解程序。

最近，我们提议通过在 GPU 上实现 MNL 模型来计算具有多个参数的复杂模型，否则这些模型将难以处理。

采用 softmax 输出层的神经网络在大型多类问题上得到了有效的应用。 这些网络产生一个输出向量，表示一系列不同结果的概率分布。 与其他实现相比，它们的定型速度较慢，但可以处理大量的类和参数。 

## <a name="non-parametric-models"></a>非参数模型

虽然广受欢迎，但 MNL 对人类行为的一些重要假设可能会限制其有效性。 具体而言，它假设某人在两个选项之间选择的相对概率独立于后面集合中引入的其他备选方案。 这在大多数情况下是不切实际的。

例如，如果你同时喜欢产品 A 和产品 B，那么有 50% 的概率你会选择其中一个。 让我们向组合引入产品“C”。 你仍然有 50% 的概率可以选择“A”，但现在你将偏好拆分成了 25% 的“B”和 25% 的“C”。 相对概率发生了变化。

此外，由于缺货或形形色色的产品分类（也就是说，当你没有明确的想法，并在货架上随便挑选一件商品），MNL 和派生模型将没有简单的方法来应对替代商品。
设计非参数模型的目的是应对替代商品，并减少对客户行为的约束。 

他们引入了“排名”的概念，其中消费者在分类中表达对产品的严格偏好。 因此，他们的购买行为可以通过按照偏好的降序顺序对产品进行排序来建模。 

分类优化问题可以表示为收益最大化：

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/assortment-optimization-problem.png" width="150"/></center>
 
- r_i$ 表示产品 i 的收益 
- 如果产品 i 被选在第 k 位，$y_i ^ k$ 为 1，否则为 0  
- $\lambda_k$ 是客户根据排名 k 做出选择的概率
- 如果产品包含在分类中，$x_i$ 为 1，否则为 0
- K 是排名数 
- n 是产品数量

受约束限制：

- 每个排名只能有 1 个选择
- 根据排名 k，只有在产品 i 是分类的一部分的情况下才可以选择它
- 如果产品 i 包括在分类中，则无法选择排名 K 中不太受欢迎的选项 
- 不购买是一个可选项，因此无法选择排名中不太受欢迎的选项

在此类公式中，问题可以被视为[混合整数优化](https://en.wikipedia.org/wiki/Integer_programming)。

设想如果有 n 个产品，可能的排名的最大数量（包括不选择选项）为阶乘：(n+1)!

虽然公式中的约束允许对可取选项进行相对有效的修剪（例如仅选择最可取的选项并设置为 1，其余选项都设置为 0），但你可以想象到，考虑到可取的备选选项的数量，实现的可伸缩性将至关重要。

### <a name="the-importance-of-data"></a>数据的重要性

我们前面提到过销售数据随时可用。 我们想要使用这些数据来指导我们的分类优化模型。 具体而言，我们想要查找我们的概率分布 λ。

从销售点系统获得的销售数据将包括带有时间戳的交易，以及在相应的时间和地点向客户展示的一组产品。 借此，我们可以构造实际销售额向量，其元素 vi,m 表示在给定分类 $S_m$ 下将商品 i 销售给客户的概率

我们还可以构造一个矩阵：

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/matrix-construction.png" width="300"/></center>

鉴于我们的销售数据变成了另一个优化问题，查找我们的概率分布 λ。 我们希望查找一个向量 λ 来最大程度减少我们的销售估计错误：

$$min_\lambda|\Lambda\lambda - v|$$

请注意，计算也可以表示为回归，为此，可以使用诸如多变量决策树这样的模型。 

## <a name="implementation-details"></a>实现详细信息

从上面的公式可以看出，优化模型是数据驱动和计算密集型模型。

Microsoft 合作伙伴（如 Neal Analytics）已经开发了可靠的体系结构来满足这些条件。 请参阅 [SKU 最大化](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview?WT.mc_id=invopt-article-gmarchet)。 我们将使用这些体系结构作为示例，并提供几点注意事项。

- 首先，它们依赖 (1) 可靠、可伸缩的数据管道来提供模型，并依赖 (2) 可靠、可伸缩的执行基础结构来运行模型。
- 其次，规划人员可以通过仪表板轻松使用这些结果。

图 2 显示一个示例体系结构。 它包括四个主要块：捕获、处理、建模和操作化。 每个块均包含主要流程。 捕获包括“数据预处理”，处理包括“存储数据”功能；建模包括“定型机器学习模型”功能；操作化包括“存储数据”和报告选项（如仪表板）。

![体系结构分为四个部分：捕获、处理、建模和操作化。](assets/sku-optimization-solution-guide/architecture-sku-optimization.png)<center><font size="1">_图 2：SKU 优化体系结构 — 由 Neal Analytics 提供_</font></center>

## <a name="the-data-pipeline"></a>数据管道

该体系结构强调了为模型定型和操作建立数据管道的重要性。 我们使用 [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet)编排管道中的活动，它是一项托管的提取-转换-加载 (ETL) 服务，允许你设计和运行集成工作流。

Azure 数据工厂是一项托管服务，其组件称为“活动”，用于使用和/或生成数据集。

活动可以拆分为：

- 数据移动（例如，从源复制到目标）
- 数据转换（例如，使用 SQL 查询聚合或运行存储过程）

数据工厂服务可以计划、监视和管理将一组活动链接在一起的工作流。 此完整工作流被称为“管道”。

在捕获阶段，我们可以利用复制活动（内置到数据工厂）将来自各种源（本地和云中）的数据传输到 Azure SQL 数据仓库。 如何执行此操作的示例在文档中提供：

- [向/从 Azure SQL DW 复制数据](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)
- [将数据载入 Azure SQL DW](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)

下图显示了管道定义。 它由三个成排的大小相等的块组成。 前两个块是用箭头连接的数据集和活动，指示数据流。 第三个块标记为“管道”，并简单地指向前两个块以指示封装。 

 ![Azure 数据工厂概念：活动管道使用的数据集。](assets/sku-optimization-solution-guide/azure-data-factory.png)<center><font size="1">_图 3：Azure 数据工厂的基本概念_</font></center>

Neal Analytics 解决方案使用的数据格式的示例可以在 Microsoft Appsource 页面找到。 该解决方案包含以下数据集：

- 每个商店和 SKU 组合的销售历史数据
- 商店和消费者记录
- SKU 代码和说明
- 捕获产品特性的 SKU 属性（例如，大小、材质）。 这些通常用于在参数化模型中区分产品变体。

如果数据源没有以特定格式表示，数据工厂将提供一系列转换活动。 

在处理阶段，SQL 数据仓库是主要存储引擎。 因此，你可能希望将此类转换活动表示为 SQL 存储过程，后者可以作为管道的一部分自动调用。 本文档提供了以下详细说明：

- [使用 SQL 存储过程转换数据](https://docs.microsoft.com/azure/data-factory/transform-data-using-stored-procedure?WT.mc_id=invopt-article-gmarchet)

请注意，数据工厂不会限制你使用 SQL 数据仓库和 SQL 存储过程。 事实上，它与多个平台集成。 例如，可以选择使用 Databricks 和运行 Python 脚本来取代转换。 这是一个优势，因为你可以在下面的“建模”阶段使用一个平台来存储、转换和定型机器学习算法。

## <a name="training-the-ml-algorithm"></a>定型 ML 算法

有几种工具可以帮助你实现参数和非参数模型。 你的选择取决于你的可伸缩性和性能要求。

[Azure 机器学习工作室](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet)是原型制作的一个极佳工具。 它提供了一种在图形环境中使用代码模块（在 R 或 Python 中）或使用预定义的 ML 组件（例如多类分类器、提升的决策树回归）构建和运行定型工作流的简单方法。 它还允许你将一个定型模型作为 Web 服务发布，单击几下便可进一步使用，从而生成一个 REST 接口。 

但是，它可以处理的数据大小限制在 10 GB（目前），每个组件可用的内核数量也限制在 2 个（目前）。

下图显示了正在使用的机器学习工作室示例。 它是机器学习实验的图形表示形式。 图中显示了几组块。 每组块代表实验的某个阶段，每个块均连接到一个或多个块，表示数据输入和输出。

![正在使用的机器学习工作室示例。](assets/sku-optimization-solution-guide/ml-training-pipeline-example.png)<center><font size="1">_图 4：使用 R 和预构建组件的 ML 定型管道示例_</font></center>

如果需要进一步扩展，但仍然希望使用 Microsoft 的一些通用机器学习算法的快速并行实现（如多项逻辑回归），你可能想要查看 Microsoft ML Server 在 Azure Data Science VM 上的运行情况。

对于非常大的数据量 (TB)，最好选择一个存储和计算元素可在其中进行以下操作的平台：

- 独立缩放，以便在不定型模型时限制成本。
- 将计算分布到多个内核。
- 在“靠近”存储的位置运行计算，以限制数据移动。

Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet) 和 [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet) 都满足这些要求。 此外，它们都是 Azure 数据工厂编辑器中支持的执行平台。 在工作流中集成任何一个平台都非常简单。

ML Server 及其库可以部署在 HDInsight 之上，但要充分利用平台功能，可能需要使用 SparkML、Python 中的 Microsoft ML Spark 库或其他专业线性编程求解程序（如 TFoCS、Spark-LP 或 SolveDF）来实现所选的 ML 算法。 

然后，启动定型过程将变成从数据工厂工作流调用相应的 pySpark 脚本或笔记本的问题。 在图形编辑器中完全支持此功能。 有关详细信息，请参阅[使用 Azure 数据工厂中的 Databricks Notebook 活动运行 Databricks Notebook](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=invopt-article-gmarchet)。

下图显示了通过 Azure 门户访问的数据工厂用户界面。 它包括工作流中各种流程的块。 

![显示 databricks 笔记本活动的数据工厂接口。](assets/sku-optimization-solution-guide/data-factory-pipeline-databricks.png)<center><font size="1">_图5：使用 Databricks 笔记本活动的数据工厂管道示例_</font></center>

此外请注意，在我们的[库存优化解决方案](https://gallery.azure.ai/Solution/Inventory-Optimization-3?WT.mc_id=invopt-article-gmarchet)中，我们提出了一个基于容器的求解程序实现，它通过 [Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet) 进行缩放。 专业优化库（如 [pyomo](http://www.pyomo.org/about/)）允许你使用 Python 编程语言来表达优化问题，然后调用独立求解程序（如 [bonmin](https://projects.coin-or.org/Bonmin)（开源）或 [gurobi](http://www.gurobi.com/)（商业版））来查找解决方案。

库存优化文档处理的是与分类优化不同的问题（订单数量），而 Azure 中求解程序的实现同样适用。

虽然比目前所建议的操作要复杂，但该技术允许最大的可伸缩性，这主要受你所能提供的内核数量的限制。

## <a name="running-the-model-operationalize"></a>运行模型（操作化）

一旦定型模型，运行它通常需要与用于部署的基础结构所不同的基础结构。 为了使其易于使用，可以选择将其部署为带有 REST 接口的 Web 服务。 Azure 机器学习工作室和 ML Server 都将创建此类服务的过程自动化。 对于 ML Server，我们提供了用于部署支持基础结构的模板。 请参阅相关[文档](https://docs.microsoft.com/machine-learning-server/what-is-operationalization?WT.mc_id=invopt-article-gmarchet)。

下图显示了此部署的体系结构。 它包括运行 R 语言和 Python 的服务器表示形式。 两个服务器都与执行计算的 Web 节点的子部分通信。 一个大数据存储连接到计算块。

![ML Server 部署关系图。 多个节点执行之前的负载均衡器。](assets/sku-optimization-solution-guide/ml-server-deployment-example.png)<center><font size="1">_图6：ML Server 部署示例_</font></center>


对于在 HDInsight 或 Databricks 上创建并因此依赖于 Spark 环境（库、并行功能等）的模型，可能需要考虑在群集上运行它们。 [此处](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/spark-model-consumption?WT.mc_id=invopt-article-gmarchet)提供了相关指导。

这样做的优点是，操作模型本身可以通过数据工厂管道活动调用，以便进行计分。

要使用容器，可以打包模型并将其部署到 Azure Kubernetes 服务。 原型将需要使用 [Azure Data Science VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet)；还必须在 VM 上安装 Azure ML [命令行](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-service-deploy?WT.mc_id=invopt-article-gmarchet)工具。

## <a name="data-output-and-reporting"></a>数据输出和报告

一旦部署完毕，该模型将能够处理财务事务工作流和存货读数，以生成最佳分类预测。 以此生成的数据可以存储回 Azure SQL 数据仓库，以供进一步分析。 具体而言，将有可能研究各种 SKU 的历史性能，找出最佳创收者和亏损者。 然后，你可以将这些数据与模型建议的分类进行比较，并评估性能以及重新定型的必要性。

[PowerBI](https://powerbi.microsoft.com/get-started/?&OCID=AID719832_SEM_uhlWLg3x&lnkd=Google_PowerBI_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvkyOLMJCrhqH8iac84aLX7EcUQIirSSqUCostzGi8y_XntJTCD73ZixoCQ4sQAvD_BwE?WT.mc_id=invopt-article-gmarchet) 提供了一种方法来分析和显示过程中生成的数据。 

下图显示了典型的 Power BI 仪表板。 它包括两个显示 SKU 存货信息的图表。 

![显示超过 12 个月的结果的仪表板示例。](assets/sku-optimization-solution-guide/sku-max-model.png)<center><font size="1">_图7：模型结果报告示例 — 由 Neal Analytics 提供_</font></center>

## <a name="security-considerations"></a>安全注意事项

处理敏感数据的解决方案包括财务记录、存货水平和价格信息。 此类敏感数据必须受到保护。 为了缓解对数据安全和私隐的顾虑，请注意：

- 可以使用 Azure Integration Runtime 在本地运行一些 Azure 数据工厂管道。 在运行时向/从本地源执行数据移动活动。 它还将调度在本地执行的活动。
  - 具体而言，你可能希望开发一个自定义活动以匿名化要传输到 Azure 的数据（并在本地运行）。
- 所有提到的服务都支持传输和静态加密。 如果选择将数据存储在 Azure Data Lake 中，则默认启用加密。 如果使用 Azure SQL 数据仓库，则可以启用透明数据加密 (TDE)。
- 所有提到的服务，除了机器学习工作室之外，都支持与 Azure Active Directory 集成以进行身份验证和授权。 如果编写自己的代码，则必须将该集成构建到你的应用程序中。

有关 GDPR 的详细信息，请参阅我们的[合规性](https://www.microsoft.com/trustcenter?WT.mc_id=invopt-article-gmarchet)页面。

## <a name="technologies-mentioned"></a>上述技术

- [Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet)
- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/?&OCID=AID719825_SEM_w1MNAVjn&lnkd=Google_Azure_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvk4bGtyQo11KBY-u2skor1SydsSl1vrYUmhyGhhwyJhDlAYpnMmIcRRoCTfsQAvD_BwE&dclid=CMn6lvfRkd0CFRwBrQYdtIoJOA?WT.mc_id=invopt-article-gmarchet)
- [Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet)
- [Azure Integration Runtime](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime?WT.mc_id=invopt-article-gmarchet)
- [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet)
- [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet)
- [Azure SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=invopt-article-gmarchet)
- [Azure 机器学习工作室](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server?WT.mc_id=invopt-article-gmarchet)
- [Azure Data Science VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet)
- [Azure Kubernetes 服务](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft PowerBI](https://powerbi.microsoft.com/?WT.mc_id=invopt-article-gmarchet)
- [Pyomo 优化建模语言](http://www.pyomo.org/)
- [Bonmin 求解程序](https://projects.coin-or.org/Bonmin)
- [适用于 Spark 的 TFoCS 求解程序](https://github.com/databricks/spark-tfocs)
