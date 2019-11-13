---
title: 概述：用于银行的数据管理
author: dstarr
ms.author: dastarr
ms.date: 10/30/2019
ms.topic: article
ms.service: industry
description: 介绍了在受管制的银行环境中使用 Microsoft Azure 管理数据的技术。
ms.openlocfilehash: 1314054018c04e45b6450604febbf0142ead380d
ms.sourcegitcommit: f42a60539bec2a7769b42b6574f09eed4d1b6c79
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750534"
---
# <a name="data-management-in-banking-overview"></a>概述：用于银行的数据管理

## <a name="introduction"></a>介绍

如今，银行肩负着在防火墙内保护和存储海量有价值信息的责任，这些信息既包含客户信息，也包含不断改变的金融格局信息。 在许多情况下，此类信息一直处于未用状态，因为它们不易于访问或搜索，即使使用此类数据可以跨多个银行业务活动改进决策制定，也不例外。

使用此类数据，银行可以更快地发现面临拖欠贷款风险的人员，或决定需要进行什么市场投资组合评估调整。 银行还可以更清楚地了解如何存储和管理数据来满足监管要求，从而根据要求利用、保留、存档或删除数据。

由于每天为了满足银行职能要求都必须制定数千个大大小小的决策，因此数据变得越来越重要。 不仅如此，由于要遵守严格的监管要求和承担防止金融犯罪义务，因此银行需要能够审核任何数据分析流程的结果，并可追溯到在数据存储中存储的初始信息。 可跟踪性要求，从引入数据到生成可操作数据整个过程都十分透明。

为了管理银行服务的多个帐户或企业，银行需要快速高效地了解所有这些数据的意义。 随着银行数字化成熟，数据量和以新方式利用此类数据的机会激增，这使得银行能够努力实现新的业务模型和以客户为中心的机会领域。

制定适当的数据存储策略是高效运营、实现优异应用程序性能和监管合规性的关键所在。 数据存储策略也是将数据转换为可用于商业智能和可操作见解的格式的最初关键一环。

常见的数据管理模式如下所示：

![数据管理流](./assets/data-mgmt-in-banking-overview/data-mgmt-00.png)

在此模型中，“数据服务”描述任何转换、数据联接或除存档操作以外的其他任何数据操作。 这是利用数据制定更明智决策的关键活动。

所有银行和金融机构都引入、移动和存储数据。 本文重点介绍了将数据引入 Azure 并弃用传统本地数据存储，以及处理、存档和删除数据。 通过将数据迁移到 Azure 中，银行和金融机构可以利用基本优势，包括：

- 在不受限的全局范围内有效控制成本，仅在需要时才使用计算资源和数据容量。

- 通过停用本地物理服务器，降低资本支出和管理成本。

- 通过集成备份和灾难恢复，降低数据保护的成本和复杂性。

- 将冷数据自动存档到低成本存储，同时确保满足合规性需求。

- 使用高级集成数据服务处理数据，以便学习、预测、转换数据或满足其他需求。

本文推荐了可确保将数据高效流入 Azure 的技术，并介绍了在数据位于云中时使用的基本数据管理技术。

## <a name="data-ingest"></a>数据引入

金融机构将有已收集且正被当前应用程序使用的数据。 将此数据迁移到 Azure 中的方法有多种。 在许多情况下，现有应用程序可以连接到 Azure 中的数据，就像是连接本地数据一样，而且只需对现有应用程序进行极少更改。 在使用 Microsoft [Azure SQL 数据库](/azure/sql-database/?WT.mc_id=bankdm-docs-dastarr)时，尤其如此。不过，也可以通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/databases?WT.mc_id=bankdm-docs-dastarr)找到适用于 Oracle、TeraData MongoDB 和其他程序的解决方案。

可采用不同的数据迁移策略将数据从本地迁移到 Azure 中，这些策略的延迟程度也各异。 下面引用的所有技术都提供数据透明度和可靠的安全性。

### <a name="virtual-network-vnet-service-endpoints"></a>虚拟网络 (VNet) 服务终结点

处理客户财务信息时，安全性是主要考虑因素。
保护 Azure 内的资源（如数据库）通常依赖的是，先在 Azure 本身内设置网络基础设施，再通过特定终结点访问相应网络。

将数据传输到 Azure 前，有用的做法是考虑采用网络拓扑，以保护 Azure 资源及其与本地的连接。
[虚拟网络 (VNet?WT.mc_id=bankdm-docs-dastarr) 服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview?WT.mc_id=bankdm-docs-dastarr)支持安全直接连接到 Azure 已定义 VNet。

VNet 是在 Azure 中定义，以便在有界 VNet 内包含 Azure 资源。 然后，连接到相应 VNet 的终结点可启用对关键 Azure 服务资源的安全访问，并且仅支持访问在已定义 VNet 上的这些资源。

## <a name="database-lift-and-shift"></a>数据库直接迁移

数据库“直接迁移”模型是便于使用 Azure SQL 数据库的最常见方案之一。 直接迁移就是指，将现有本地数据库直接迁移到云中。 这样做的原因如下：

- 从价格更高的当前数据中心迁移或出于其他某运营原因
- 当前本地 SQL Server 数据库硬件即将到期或生命周期即将结束
- 为了支持公司的常规“迁移到云中”策略
- 利用 SQL Azure 的可用性和灾难恢复功能

如果数据库较小，数据引入的第一步通常是，通过 Azure 门户、Azure CLI 或 Azure SDK 创建所需的数据存储和结构（如表）。 对于这些较小的数据存储，后续步骤可以由为了将正确数据复制到相应 Azure 数据存储而编写的自定义应用程序执行。 对于较大的数据迁移，在 Azure 中还原备份通常是最快途径。

将数据安全快速地传输到 Azure 中的方法有很多种。 有关一些标准技术及各自的优缺点，[请参阅这篇文章](/azure/architecture/data-guide/scenarios/data-transfer?WT.mc_id=bankdm-docs-dastarr)。

### <a name="azure-database-migration-service"></a>Azure 数据库迁移服务

直接迁移 SQL Server 数据库时，可使用 [Microsoft Azure 数据库迁移服务](/azure/dms/dms-overview?WT.mc_id=bankdm-docs-dastarr)将数据库迁移到 Azure 中。 此服务使用[数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?WT.mc_id=bankdm-docs-dastarr)，以确保本地数据库与 Azure SQL 中的功能兼容。 迁移数据库前是否必须进行任何更改取决于你的选择。 此外，在本地网络和 Azure 之间还必须有站点到站点 Internet 连接，才能使用此服务。

### <a name="bulk-copy-program-for-sql-server"></a>适用于 SQL Server 的大容量复制程序

如果 SQL Server 目前位于本地，且目标是要迁移到 SQL Azure 中，可采用另一项很棒的技术，即使用 SQL Server Management Studio [和 BCP 实用工具将数据迁移到](https://azure.microsoft.com/blog/bcp-and-sql-azure/?WT.mc_id=bankdm-docs-dastarr) SQL Azure 中。 在原始本地服务器中创建 Azure SQL 数据库并编写数据库脚本后，便可使用 BCP 将数据快速传输到 SQL Azure。

### <a name="blob-and-file-storage"></a>Blob 存储和文件存储

各家银行支行通常在本地服务器上有自己的文件存储。 这可能会导致支行之间无法共享文件，并导致给定文件没有单一真实源。 更糟糕的是，金融机构可能有支行可访问的“官方”文件存储，但在访问文件共享时会出现间歇性的连接问题或其他问题。

Azure 提供了有助于降低这些问题影响的服务。 将此类数据迁移到 Azure 中，可以提供所有数据的单一真实源，以及支持集中权限和访问控制的全局可访问存储。

数据存储解决方案是否更适合因特定数据格式各异。
例如，在 SQL Server 中本地存储的数据可能最适合迁移到 Azure SQL 中。 在 .csv 或 Excel 文件中存储的数据可能最适合迁移到 [Azure Blob](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr) 存储或 [Azure 文件](/azure/storage/files/storage-files-introduction?WT.mc_id=bankdm-docs-dastarr)存储（这是在 Blob 服务的基础之上实现的存储）。

几乎所有流入和流出 Azure 的数据都会在数据移动过程中流经 Blob 存储。 Blob 存储的核心要素如下。

- 持久可用
- 安全合规
- 可管理且具有成本效益
- 可缩放且性能高
- 开放且可交互

将所有支行都连接到 Azure 中的同一文件共享，通常是通过银行的现有数据中心实现的，如图 1 所示。 企业数据中心通过 SMB（服务器消息块）连接连接到文件存储。
从逻辑和站点网络的角度来看，文件共享可以位于企业数据中心内，并能装载为其他任何网络文件共享。
使用此技术时，数据在静态时以及在数据中心与 Azure 之间传输时进行加密。

![逻辑文件共享](./assets/data-mgmt-in-banking-overview/data-mgmt-01.png)

图 1

企业通常使用文件存储来合并和保护海量文件。 这样一来，就可以停用旧文件服务器或再利用硬件。
迁移到文件存储的另一个优势是，可集中管理数据和使用恢复服务。

### <a name="azure-data-box"></a>Azure Data Box

通常来说，银行要迁移到 Azure 的信息达到 TB（甚至是 PB）级别。 幸运的是，Azure 中的数据存储非常有弹性且高度可缩放。

侧重于将海量数据迁移到 Azure 的服务是 [Azure Data Box](https://azure.microsoft.com/services/storage/databox/?WT.mc_id=bankdm-docs-dastarr)。 此服务旨在迁移数据，无需通过 Azure 连接传输数据或备份。 Azure Data Box 适用于迁移 TB 级别的数据，可以从 Azure 门户订购此设备。 交付到你的位置上后，它就可以在其中连接到网络，并通过标准 NAS 协议加载数据，以及通过 standard256-AES 加密提供保护。 当此设备上加载有数据后，数据就会发送回 Azure 数据中心，并在 Azure 中水化。
然后，此设备就会被安全清除。

## <a name="azure-information-protection"></a>Azure 信息保护

Azure 信息保护 (AIP) 是基于云的解决方案，有助于组织分类、标记和保护文档和电子邮件。 这可以由定义了规则和条件的管理员自动执行、由用户手动执行，或者以组合方式执行，在组合方式中，将向用户提供建议。

## <a name="data-services"></a>数据服务

银行执行主数据管理很费劲，因为元数据相互冲突，这是由于迥然不同的核心银行系统，以及数据来自源系统、载入系统、产品/服务管理系统、CRM 系统等所致。 Azure 提供了工具来帮助降低这些问题和其他常见数据问题的影响。

金融服务组织需要对数据执行的操作有许多。 将数据写入 Azure 数据存储后，可能需要转换此类数据，或将它与能扩充要引入数据的其他数据联接。

### <a name="azure-data-factory"></a>Azure 数据工厂

[Microsoft Azure 数据工厂](/azure/data-factory/introduction?WT.mc_id=bankdm-docs-dastarr)是一项完全托管服务，有助于流入、处理和监视数据工厂管道中的数据移动。 数据工厂活动构成了数据管理管道的结构。

使用数据工厂，可以在数据流入 Azure 和在其他 Azure 服务之间流动时转换或扩充数据。 数据工厂是一项托管云服务，旨在用于复杂的混合提取-转换-加载 (ETL)、提取-加载-转换 (ELT) 和数据集成项目。

例如，数据可能会被馈送到生成可操作见解的分析管道或工具。 数据可能会流入机器学习解决方案，也可能会被转换为另一种格式，以供日后下游处理。 例如，将 .csv 文件转换为 parquet 文件（更适合于机器学习系统），并将这些 parquet 文件存储在 Blog 存储中。

数据也可能会被提交到下游计算服务，如 [Azure HDInsight](/azure/hdinsight/hadoop/apache-hadoop-introduction?WT.mc_id=bankdm-docs-dastarr)、[Spark](/azure/hdinsight/spark/apache-spark-overview?WT.mc_id=bankdm-docs-dastarr)、[Azure Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview?WT.mc_id=bankdm-docs-dastarr) 和 [Azure 机器学习](/azure/machine-learning/?WT.mc_id=bankdm-docs-dastarr)。 这样一来，便能直接馈送系统，以生成分析和智能报表。 一个常见的数据入口模型如下面的图 2 所示。 数据保留在通用 [Data Lake](/azure/data-lake-store/data-lake-store-overview?WT.mc_id=bankdm-docs-dastarr) 中，以供下游分析服务使用。

![Data Lake 入口模型](./assets/data-mgmt-in-banking-overview/data-mgmt-02.png)

图 2

数据工厂管道由引入和输出数据集的活动组成。 可以将这些活动汇编到管道中，同时定义要从哪里获取数据、要如何处理数据以及要在哪里存储结果。 生成包含活动的管道是数据工厂的核心，而直接在 Azure 门户中撰写可视工作流则会让管道创建变得很简单。 [有关完整活动列表，请参阅此处](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr)。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) 是 Azure 中基于托管 Apache Spark 的分析平台。 它高度可缩放，并且可以根据需要在任意大的计算机群集上运行 Spark 作业。 Databricks 的工作运行源是 Notebook，这是一个供数据科学家、数据工程师和业务分析员开展协作的集中位置。

Databricks 是在需要转换或分析数据时使用的逻辑处理管道。 它可由数据工厂直接馈送数据，无论是见解生成时间至关重要的机器学习方案，还是简单的文件转换方案。

![Databricks](./assets/data-mgmt-in-banking-overview/data-mgmt-03.png)

## <a name="archiving-data"></a>存档数据

如果不再需要有效数据存储中的数据，可根据国家和地方银行法规，出于合规性或审核线索目的存档此类数据。 Azure 提供了不太常访问数据的存储方法。 对于必须在存储中保留数年的数据而言，通常都存在隐私问题。

数据存储成本可能会很高，特别是在本地数据库中存储时。 这些数据库有时不太常获访问，只是被写入新存档的数据，或从数据库中删除存档内不再需要的数据。
本地计算机不太常获访问意味着，硬件的总拥有成本更高。

### <a name="azure-archive-storage"></a>Azure 存档存储

对于文件或图像等非结构化数据，Azure 提供了[多个存储层](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=bankdm-docs-dastarr)以用于 Blob 存储，其中包括热存储层、冷存储层和存档存储层。 热访问层适用于预期性能最高且用于应用程序的有效数据。 冷访问层适用于短期备份和灾难恢复数据集，以及对应用程序可用但极少获访问的数据。 存档访问层的成本最低，适用于脱机数据。

可以将存档访问层数据重新水化到冷访问层或热访问层，但此操作可能需要几个小时才能完成。 如果数据至少在 180 天内不会获访问，存档存储层可能适用。 如果 Blob 位于存档存储层中，便无法读取它，但可以执行其他现有操作，如列出、删除和检索元数据。 存档数据层是用于 Blob 存储的成本最低数据层。

### <a name="azure-sql-database-long-term-retention"></a>Azure SQL 数据库长期保留

使用 Azure SQL 时，可使用[长期备份保留服务](/azure/sql-database/sql-database-long-term-retention?WT.mc_id=bankdm-docs-dastarr)，最多将备份存储 10 年。 用户可将备份安排为，保留以供长期存储，这样就能保留备份数周、数月或甚至数年。

若要从长期存储中还原数据库，请根据时间戳选择具体备份。 数据库可以还原到现有服务器上原始数据库所在的相同订阅下。

## <a name="deleting-unwanted-data"></a>删除不需要的数据

为了一直遵守与数据保留相关的银行法规或策略，必须经常删除不再需要的数据。 对此类不需要的数据实现技术解决方案前，请务必先制定清除计划，以免违反已达成一致的策略。 可随时从存档或 Azure 内的其他任何数据存储中删除数据。

删除不需要的数据的有效策略是定期删除，最常用的频率为每晚或每周一次。 也可以编写[时间触发型 Azure 函数](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr)来执行此作业。 如果用户删除了任何数据，Microsoft Azure 也将删除该数据，包括所有缓存或备份副本。

## <a name="getting-started"></a>入门

入门方法有很多种，具体视目前所用数据模型的当前使用情况和成熟度而异。 就一切情况而论，此时是检查数据存储、处理和每个数据存储所需的保留模型的最佳时机。 这对于生成合规性方案中的数据管理系统至关重要。
在这种情况下，云提供了本地暂时无法提供的新机会。 也就是说，可更新现有数据模型（若有）。

习惯使用新数据模型后，立即确定数据引入策略。 数据源有哪些？ 数据将存储在 Azure 中的什么位置？ 如何以及何时将数据迁移到 Azure 中？ 可帮助你根据内容类型、大小等进行迁移的资源有很多。 Azure 数据迁移服务就是这样一个例子。

将数据托管在 Azure 中后，立即为已失去效用或生命周期结束的数据创建数据清除计划。 虽然长期（冷）存储始终都是理想的存档方法，但清理过期数据可减少内存占用情况和整体存储成本。 [Azure 解决方案体系结构](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)备份和存档是有助于计划总策略的实用资源。

## <a name="relevant-technologies"></a>相关技术

- [Azure Functions](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr) 是可响应系统事件或计时器而运行的无服务器脚本和小程序。

- [Azure 存储客户端工具](/azure/storage/common/storage-explorers?WT.mc_id=bankdm-docs-dastarr)是用于访问数据存储的工具，包括的工具远不止 Azure 门户。

- [Blob 存储](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr)适合存储文本或图像等文件，以及其他类型的非结构化数据。

- [Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) 是一项完全托管服务，可便于轻松实现 Spark 群集。

- [数据工厂](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr)是一项云数据集成服务，用于将数据存储服务、传输服务和处理服务撰写到自动化数据管道中

## <a name="conclusion"></a>结束语

随着银行和金融业的数字环境快速变化，客户日越来越多地寄希望于找到能立即利用且无需缓慢增强的解决方案和合作伙伴。 随着数据引入激增，银行越来越需要通过快速、创新和安全的方法来存储、分析和使用重要数据。

Azure 有助于通过多个技术和策略来满足数据引入、处理、存档和删除需求。 将数据引入 Azure 很简单，可使用各种数据存储来存储数据，具体视数据类型、结构等而异。除了 SQL Server 和 SQL Azure 以外，数据解决方案还包含第三方数据库。

通过使用 Databricks 和数据工厂等 Azure 服务，可以很轻松地对数据执行操作。 存档存储适用于长期存储极少获访问的数据，可以根据需要按滚动周期删除它。

若要开始设计数据管理计划，请访问Azure 解决方案库的[备份和存档存储](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)。

**本文作者**：Howard Bush 和 David Starr
