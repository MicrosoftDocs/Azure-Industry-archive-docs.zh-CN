---
title: 用于从面向制造业的 IoT 提取见解的体系结构
description: 使用 Azure 服务从 IoT 数据中提取见解。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 11/28/2019
ms.openlocfilehash: c08e6bbb1da47084122dae1ed6a9e1cea0b59473
ms.sourcegitcommit: db3bee67c1467884af223a48a895715afba8e08c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2019
ms.locfileid: "75005306"
---
# <a name="extracting-actionable-insights-from-iot-data"></a>从 IoT 数据中提取可操作见解

如果你负责工厂车间的机器，则应该知道物联网 (IoT) 是改进流程和结果的下一步。 第一步是在机器上或工厂车间安装传感器。 下一步是使用数据，这是本文档的目标。 本指南从技术角度概述了从 IoT 数据分析中提取可操作见解所需的组件。

IoT 分析解决方案旨在将来自一组设备的原始 IoT 数据转换成更适合分析的形式。 一旦数据具有可分析的形式，我们便可以执行分析。 一些分析示例包括：

- 遥测数据的简单可视化，例如，显示温度随时间变化的条形图
- KPI 计算，例如，设备综合效率 (OEE)
- 由机器学习模型提供支持的预测分析

这些分析反过来提供告知操作信息的见解。 操作范围包括向机器发送简单的命令、调整操作参数、在另一个软件系统上执行操作，以及在全公司范围内执行操作改进程序。

下图显示了在工厂机器和最终结果之间发生的一系列操作，这是通过仪表板形式表示的“利用率”，其中显示了一个图表和标签“87.5%”。

![工厂到仪表板。](assets/extracting-insights-from-iot/factory-to-dashboard.png)

为了便于说明，我们将使用一个简单的 KPI 计算：机器利用率。 机器利用率  是机器实际生产  部件的时间百分比。 例如，如果一个班次为 8 小时，而机器生产部件的时间为 7 小时，那么该班次的机器利用率是 87.5% (7/8×100)。

## <a name="approach"></a>方法

IoT 应用程序有三个组件：内容  （或设备），发送用于生成见解  的数据或事件，以便用于生成操作  来帮助改进业务或流程。

制造工厂中的设备（内容）在运行时发送各种类型的数据。 例如，铣床发送进给率和温度数据；这用于评估机器是否正在运行（即见解），进而用来优化工厂（即操作）。 我们将通过这些步骤来提取数据，在仪表板中对其进行可视化，提取新见解，并采取进一步操作。
![内容到见解再到操作。](assets/extracting-insights-from-iot/things-insights-actions.png)

Microsoft 已经为 IoT 应用程序发布了一个高级参考体系结构，介绍了各种子系统以及这些子系统的推荐方法。
IoT 应用程序由以下子系统组成：

1. 设备或本地边缘网关，是一种可以安全地将消息源（设备）注册到云中的特定类型的设备。 边缘网关还可以将消息从本机协议转换为另一种格式（如 JSON）。
2. 云网关服务或中心（如 [Azure IoT 中心](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)或 [Azure 事件中心](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk)），用于安全地引入数据并提供设备管理功能。 
3. 使用流数据的流处理器。 处理器还可以与业务流程集成，并将数据放入存储中。
4. 一个仪表板形式的用户界面，用于可视化 IoT 数据和加速设备管理。

![解决方案的体系结构。](assets/extracting-insights-from-iot/architecture.png)

在本文中，我们将重点讨论提取见解的过程。 下面是主要步骤：

1. 访问数据，并将其处理为数据流。
2. 处理并存储数据。
3. 可视化或呈现数据。 

下图显示了从数据源到转换、引入、处理和存储、表示形式，最后到操作的数据流。

![数据流：从源到引入到呈现再到操作。](assets/extracting-insights-from-iot/data-flow.png)

## <a name="converting-the-data-to-a-stream"></a>将数据转换为流

IoT 数据是时间序列数据：来自“内容”的值在一段时间内可能更有意义。 工厂车间上的设备会在随时间的运转过程中发生事件。 如果工厂车间上的数据没有发送到数据引入服务，例如 [Azure IoT 中心](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)，我们必须从其存储中定期轮询数据（例如，制造执行系统 (MES) 或 HTTP 终结点），并将数据发送到引入服务。  
要将数据转换为流，我们通常：

1. 访问数据源。
2. 转换和扩充数据。
3. 将数据发布到可以引入流数据的终结点。

这里我们对数据源的访问不做详细介绍，因为访问取决于数据驻留的位置，而且变化太多。

## <a name="transforming-and-enriching-the-data"></a>转换和扩充数据

原始数据通常必须经过转换操作才能标准化，并为引入做好准备。  具体转换将根据使用的分析类型而异。  数据转换的一些常见示例是时间序列数据，在这些数据中可能缺少度量值并需要输入，或者需要对不同机器之间的时间跨度进行合理化。  我们希望数据记录具有时间戳（如果源包含它的话）并采用名称-值对的形式。 通常，数据采用层次结构格式，并且必须转换为平面结构。

下图显示了一个具有交错配置文件的分层数据结构，转换为标准化的列和行格式（块）。

![数据形状从层次结构到平面不一。](assets/extracting-insights-from-iot/hierarchy-to-flat.png)

通常情况下，数据无法从 Internet 上获取。 一个常见的模式是使用边缘网关将数据从工厂车间推送到引入点。 [Azure IoT Edge](/azure/iot-edge?WT.mc_id=iotinsightssoln-docs-ercenk) 是基于 IoT 中心构建的服务。 IoT Edge 设备可以充当网关覆盖三种[模式](/azure/iot-edge/iot-edge-as-gateway?WT.mc_id=iotinsightssoln-docs-ercenk)：透明网关、协议转换和标识转换。

如果数据可以在外部使用，并且可以从 Internet 访问，可以使用几个 Azure 服务来访问、转换和扩充数据。 这些选项包括：

- 部署在各种 Azure 计算服务中的自定义代码，例如[应用服务](/azure/app-service/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure Kubernetes 服务](/azure/aks/?WT.mc_id=iotinsightssoln-docs-ercenk) (AKS)、[容器实例](/azure/container-instances/?WT.mc_id=iotinsightssoln-docs-ercenk)或 [Service Fabric](/azure/service-fabric/service-fabric-overview?WT.mc_id=iotinsightssoln-docs-ercenk)。
- [Azure 逻辑应用](/azure/logic-apps/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure 数据工厂中的管道和活动](/azure/data-factory/copy-activity-overview/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [BizTalk 服务](https://azure.microsoft.com/services/biztalk-services/)

上述每个服务都有其自己的优势和成本，这要取决于场景。 例如，逻辑应用提供[转换 XML 文档](/azure/logic-apps/logic-apps-enterprise-integration-transform?WT.mc_id=iotinsightssoln-docs-ercenk)的方法。 然而，数据可能是一个过于复杂的 XML 文档，因此，开发一个大型 XSLT 脚本来转换数据可能并不可行。 在这种情况下，可以使用来自不同 Azure 服务的多个微服务来开发混合解决方案。 例如，在 Azure 逻辑应用中实现的微服务可以轮询 HTTP 终结点，临时存储原始结果，并通知另一个微服务。 用于转换消息的另一个微服务可以是托管在 [Azure Functions 主机](https://github.com/Azure/azure-functions-host)上的自定义代码。  

![Https 对数据进行轮询并由 Functions 负责处理。](assets/extracting-insights-from-iot/poll-logic-process.png)

或者，可以选择由 Azure 数据工厂编排的工作流，其中有一系列活动执行转换。 有关可用活动类型的详细信息，请参阅 [Azure 数据工厂中的管道和活动](/azure/data-factory/concepts-pipelines-activities)。
消息可以在接收时加盖时间戳，也可以包含一个时间戳，以便我们重新构造几个度量值的时间序列。 因此，可以忽略的引入延迟和高吞吐量是保证信息完整性和最终响应适时性的基础。 为了减少延迟，我们将时间戳规范化为尽可能接近工厂。

## <a name="ingesting-the-data-stream"></a>引入数据流

为了将数据作为一个流进行分析，我们可以基于时间窗口对数据进行查询，以识别模式和关系。 Azure 平台上有各种各样的服务，可以高吞吐量地引入数据。
以下服务的选择取决于项目需要，例如设备管理、协议支持、可伸缩性、团队对编程模型的偏好等。例如，团队可能因为他们的经验而倾向于使用 Kafka，或者需要一个 Kafka 代理来解决这个问题。 或者，对于另一种情况，项目可能需要数据引入系统来利用 [IoT 中心设备预配服务的 TPM 密钥证明](/azure/iot-dps/?WT.mc_id=iotinsightssoln-docs-ercenk)，以确保设备可以访问引入点。

- [Azure IoT 中心](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk)是 IoT 应用程序和设备之间的双向通信中心。 它是一项可扩展服务，通过提供安全通信、消息路由、与其他 Azure 服务的集成以及控制和配置设备的管理功能，来支持功能齐全的 IoT 解决方案。

- [Azure 事件中心](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk)是一个用于以极高吞吐率从并发源收集遥测数据的大规模仅限引入服务。
- [Apache Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 是一个托管 [Apache Kafka](https://kafka.apache.org/) 的托管服务。 Apache Kafka 是开源分布式流式处理平台，还提供消息代理功能。 托管服务对 Kafka 正常运行时间提供 99.9% 的服务级别协议 (SLA)。

## <a name="processing-and-storing-the-data"></a>处理和存储数据

IoT 应用程序带来了挑战，因为它们是事件驱动型系统，也需要保留历史数据并基于这些数据运行。 引入数据是一种追加类型的数据，可能会变大。 需要将数据保留较长时间，主要原因如下：存档、批处理分析和构建机器学习 (ML) 模型。 另一方面，事件流对于近实时分析以检测异常、识别滚动时间窗口中的模式或触发警报（如果值超过或低于阈值）至关重要。
Microsoft Azure IoT 参考体系结构为使用 Lambda 体系结构的 IoT 解决方案中的设备到云消息和事件提供了建议的数据流。 Lambda 体系结构支持对接近实时的流数据和存档数据进行分析，这使得它成为处理传入数据的最佳选择。

## <a name="lambda-architecture"></a>Lambda 架构

Lambda 体系结构通过创建两个数据流路径来解决此问题。 所有进入系统的数据都经过这两个路径：

- 批处理层（冷路径）以原始形式存储所有传入数据，并对数据进行批处理。 该处理的结果作为批处理视图存储。 它是一个执行复杂分析的缓慢处理管道，例如，合并来自多个源和较长时间的数据（数小时、数天或更长时间），并生成新的信息，如报告、机器学习模型等。
- 速度层（暖路径）可实时分析数据。 设计此层是为了降低延迟，但代价是准确性也会降低。 它是一个更快的处理管道，可以对传入消息进行存档和显示，并分析这些记录，以生成短期关键信息和警报等操作。
- 批处理层将结果馈送到“服务层”，以便对查询做出响应。 批处理层编制批处理视图的索引，以便提高查询效率。 速度层会根据最新数据使用增量更新来更新服务层。

下图显示了表示转换阶段的五个块。 第一个块是数据流，它以并行方式向速度层和批处理层馈送。 这两个层均向所服务的层馈送，速度层和所服务的层均向分析客户端馈送。
![Lambda 体系结构。](assets/extracting-insights-from-iot/lambda-schematic.png)

 Azure 平台提供各种服务，可用于实现体系结构。 下图显示了如何映射这些服务来实现它。 图中显示了转换的五个阶段，每个阶段都包含相关的 Azure 技术。 颜色较深的框表示执行这些任务的多个选项的可用性。

![Lambda 体系结构。](assets/extracting-insights-from-iot/lambda-architecture-all.png)

速度层上的数据引入服务选项在前一节“引入数据流”中进行了介绍。

[Apache Kafka on HDInsight](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) 可以是一个服务选项，用于实现数据引入服务和流处理的数据流。

如果你正在为数据引入服务使用事件中心，请使用 [Azure 流分析](/azure/stream-analytics?WT.mc_id=iotinsightssoln-docs-ercenk) (ASA)。 Azure 流分析是一个事件处理引擎，用于检查从设备流式传输的大量数据。 传入数据可能来自设备、传感器、网站、社交媒体源、应用程序等。 它还支持从数据流提取信息，以及确定模式和关系。

流分析查询从流数据源开始，可以使用引入到 Azure 事件中心、Azure IoT 中心的数据，也可以使用从 Azure Blob 存储之类的数据存储引入的数据。 若要检查流，请创建一个流分析作业，以便指定可流式传输数据的输入源。 该作业还指定了转换查询，用于定义如何查找数据、模式或关系。 转换查询利用类似 SQL 的查询语言对某个时段的流数据进行筛选、排序、聚合和联接操作。

## <a name="warm-path"></a>暖路径

本文档的示例场景是机器利用率 KPI（在本指南开头进行了介绍）。 我们可以选择对数据进行简单的解释，并假定如果机器正在发送数据，那么它正在被使用。 然而，机器可能在发送数据的同时并没有真正生成任何内容（例如，它可能处于空闲或维护状态）。 这是当试图从物联网数据中提取见解时一个很常见的难题：你正在查找的数据在你获取的数据中不可用。 因此，在本示例中，我们没有获得清楚而明确地告诉我们机器是否处于生产状态的数据。  所以，我们需要通过将获取的数据与其他数据源相结合，并应用规则来确定机器是否处于生产状态，以此来推断利用率。 此外，这些规则可能因公司而异，因为它们可能对“生产”有不同的解释。 暖路径旨在当数据流经系统时进行分析。 我们以近实时的方式处理此流，将它保存到暖存储中，并将其推送到分析客户端。

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 事件中心为数据流暖路径的第一步提供了完美匹配。

下图显示了速度层阶段。 它包含事件中心、流分析实例和暖存储的数据存储。

![Lambda 体系结构：突出显示速度层。](assets/extracting-insights-from-iot/lambda-2.png)
  
Azure 平台提供了多种在事件中心处理事件的选项，但我们推荐使用流分析。 流分析还可以将数据推送到 Power BI 服务以可视化流数据。

流分析可以大规模执行复杂分析，例如，翻转/滑动/跳跃窗口、流聚合和外部数据源联接。 对于更复杂的处理，可以通过层叠事件中心、流分析作业和 Azure Fucntions 等多个实例来扩展性能，如下图所示。

![事件中心到分析再到 Power BI。](assets/extracting-insights-from-iot/event-hubs-to-power-bi.png)
  
可以在 Azure 平台上使用各种服务实现暖存储，比如 Azure SQL 数据库。 我们建议使用 [Azure Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=iotinsightssoln-docs-ercenk)。 它是 Microsoft 推出的全球分布式多模型数据库。 对于那些可以从灵活的、与架构无关的、自动索引和丰富的查询接口中获益的数据集来说，这是最佳选择。 Cosmos DB 允许多区域、读/写，除了自动故障转移之外，还支持手动故障转移。 此外，Cosmos DB 允许用户在他们的数据上设置生存时间 (TTL)，从而使旧数据自动过期。 建议使用该功能来控制记录在数据库中的保存时间，从而控制数据库大小。

Cosmos DB 的定价基于使用的存储和预配的[请求单位](/azure/cosmos-db/request-units)。 Cosmos DB 最适合于不需要涉及大型数据集聚合查询的场景，因为这些查询需要的请求单位比基本查询（如设备的最后一个事件）更多。  

[Microsoft Power BI](/power-bi/power-bi-overview?WT.mc_id=iotinsightssoln-docs-ercenk) 是一个软件服务、应用程序和连接器集合，它们共同协作，将不相关的数据源转换为一致的、直观逼真的交互式见解。 Power BI 帮助你及时了解重要信息。 可以利用 [Power BI 中的实时流](/power-bi/service-real-time-streaming?WT.mc_id=iotinsightssoln-docs-ercenk)将数据推送到服务。 此实时流可以作为 Power BI 仪表板上各种视觉对象的实时流数据源。

## <a name="cold-path"></a>冷路径

在暖路径中，流处理会随着时间的推移发现模式。 然而，我们还想计算在过去一段时间内的利用率，使用不同的数据透视表和聚合，如机器、生产线、工厂、生产部件等等。 我们希望将这些结果与暖路径结果合并，向用户提供一个统一的视图。 冷路径包括批处理层和所服务的层。 该组合可提供系统的长期视图。

冷路径包含解决方案的长期数据存储。 它还包含批处理层，这会创建预先计算的聚合视图，以提供长时间的快速查询响应。 Azure 平台上可用于这一层的技术选项非常多样化。

![Lambda 体系结构：突出显示批处理层。](assets/extracting-insights-from-iot/lambda-3.png)
  
[Azure 时序见解](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk) (TSI) 是针对时间序列数据的分析、存储和可视化服务。 它提供了类似于 SQL 的筛选和聚合，从而缓解对用户定义函数的需求。 TSI 可以接收来自事件中心、IoT 中心或 Azure Blob 存储的数据。 TSI 中的所有数据都存储在内存和 SSD 中，这确保了数据可以随时进行交互式分析。 例如，一个超过数千万个事件的典型聚合在发出指令后数毫秒内返回。 它还提供可视化效果，例如不同时间序列的叠加、仪表板比较、可访问的表格视图和热度地图。 TSI 的主要功能包括：

- 为不需要立即报告数据的解决方案提供内置的可视化服务。 TSI 在查询数据记录时大约有 30-60 秒的延迟。 
- 查询大型数据集的功能。
- 任意数量的用户都可以进行无限数量的查询，而无需额外付费。

TSI 的最大保留期为 400 天，最大存储空间上限为 3 TB。 如果需要更长的保留时间或更大的容量，请使用冷存储数据库（将数据交换到 TSI 以根据需要进行查询）。

随着时间的推移，IoT 应用程序的冷存储将越来越大。 这是长期存储数据并在用于分析的批处理视图中聚合的位置。 ML 模型数据也存储在此处。 建议使用 [Azure 存储](/azure/storage/?WT.mc_id=iotinsightssoln-docs-ercenk)进行冷存储。 它是一项 Microsoft 托管服务，能够提供在可用性、安全性、持久性、可伸缩性和冗余性方面都很高的云存储。 Azure 存储包括 Azure Blob（对象）、Azure Data Lake Storage Gen2、Azure 文件、Azure 队列和 Azure 表。 冷存储可以是 Blob、Data Lake Storage Gen2 或 Azure 表，也可以是这些产品的组合。

[Azure 表存储](/azure/cosmos-db/table-storage-overview?WT.mc_id=iotinsightssoln-docs-ercenk)是一项用于在云中存储结构化 NoSQL 数据的服务，通过无架构设计提供键/属性存储。 因为表存储无架构，因此，可以很容易地随着应用程序需求的发展使数据适应存储。 对于许多类型的应用程序来说，访问表存储数据速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。 我们将一个表用于示例，一个表用于从数据流接收的事件。 分区键设计是一个特别重要的概念；两个表都使用事件或示例上的时间戳的小时数。 有关详细信息，请参阅 [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?WT.mc_id=iotinsightssoln-docs-ercenk)（了解表服务数据模型）。

要存储大量非结构化数据，比如 JSON 或包含 IoT 应用程序接收到的未处理数据的 XML 文档，最佳选择是 [Blob 存储](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure 文件](/azure/storage/files/storage-files-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)或 [Azure Data Lake Storage Gen2](/azure/storage/data-lake-storage/introduction?WT.mc_id=iotinsightssoln-docs-ercenk)。

可以通过 HTTP 或 HTTPS 从世界上的任何位置安全地访问 Azure Blob 存储。 必须使用服务所用的[授权机制](/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)之一对 Blob 存储访问进行授权。 该服务提供多个复制[选项](/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)：本地冗余、区域冗余、异地冗余和读取访问异地冗余。 此外，还有三个[访问层](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=iotinsightssoln-docs-ercenk)，可以提供最具成本效益的解决方案。

数据进入冷存储后，需要在 Lambda 体系结构所服务的层上创建批处理视图。 [Azure 数据工厂](/azure/data-factory/introduction?WT.mc_id=iotinsightssoln-docs-ercenk)是在所服务的层上创建批处理视图的理想解决方案。 它是基于云的托管数据集成服务，用于在云中创建数据驱动型工作流，以便协调和自动完成数据移动和数据转换。 可以使用 Azure 数据工厂创建和计划[数据驱动型工作流](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=iotinsightssoln-docs-ercenk)（称为“管道”），以便从不同的数据存储引入数据。 它可以使用服务（例如 [Azure HDInsight Hadoop](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Spark](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk) 和 [Azure Databricks](/azure/azure-databricks/?WT.mc_id=iotinsightssoln-docs-ercenk)）处理和转换数据。 这允许你构建机器学习模型并与分析客户端一起使用。

例如，如下图所示，数据工厂管道将从主数据存储中读取数据。 一个管道汇总和聚合数据，以填充 Azure 数据仓库实例。 数据工厂管道还包含用于构建 ML 模型的 [Azure Databricks 笔记本活动](/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=iotinsightssoln-docs-ercenk)。

![Lambda 体系结构：突出显示批处理层。](assets/extracting-insights-from-iot/master-data-to-ml-analytics.png)
  
[Azure SQL 数据库](/azure/sql-database/?WT.mc_id=iotinsightssoln-docs-ercenk)或 [Azure SQL 数据仓库](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=iotinsightssoln-docs-ercenk)是用于托管批处理视图的最佳选项。 这些服务可以在主数据上提供预计算和聚合视图。 

Azure SQL 数据库 (SQL DB) 是基于最新版 Microsoft SQL Server 数据库引擎的关系数据库即服务。 SQL DB 是一个可靠、安全的高性能数据库，可以使用它来构建数据驱动型应用程序和网站。 作为一项 Azure 服务，不需要管理它的基础结构。 随着数据量的增加，解决方案可以开始使用数据聚合和存储技术来加速查询。 预先计算的聚合是众所周知的方法，尤其是对于仅追加的数据。 它对成本管理也很有用。

Azure SQL 数据仓库提供了在某些场景中有用的许多附加功能。 它是基于云的企业数据仓库，可利用大规模并行处理对 PB 级数据快速运行复杂的查询。 如果需要保留 PB 级数据，并且希望查询能够快速运行，则建议使用 SQL 数据仓库。

## <a name="visualizing-the-data"></a>可视化数据

在这一层上，我们希望合并两个数据管道（暖路径和冷路径），以呈现数据的内聚视图。 在此示例中，我们使用多个指标来推断机器在暖路径和冷路径上的利用率。 在分析阶段，我们提供合并来自这些路径的数据的可视化效果。

![Lambda 体系结构：突出显示分析客户端层。](assets/extracting-insights-from-iot/lambda-4.png)

[Microsoft Power BI](/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk) 和 [Azure 时序见解](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk)提供现成的数据可视化效果。 Power BI 是一种业务分析解决方案，可以用来可视化数据，并在组织内共享见解，或将它们嵌入到应用或网站中。 [Power BI Desktop](https://powerbi.microsoft.com/desktop/?WT.mc_id=iotinsightssoln-docs-ercenk) 是功能强大的免费工具，用于建模报表及其基础数据源。  嵌入 Power BI 可视化效果的应用程序使用由桌面工具编写并在 Power BI 服务上托管的报告。

时序见解有一个数据浏览器，可用来可视化和查询数据以及 REST 查询 API。 此外，它还公开了一个 JavaScript 控件库，允许你将 TSI 支持的图表嵌入到自定义应用程序中。 下面是 TSI 上的一个基本热度地图视图，用于获取接近车间机器利用率的输入数据，只需查看观察到的样本数量即可。

![Lambda 体系结构：突出显示批处理层。](assets/extracting-insights-from-iot/client-screen.png)

如果需要一个基于浏览器的用户界面来聚合来自多个源的数据，TSI 和 Power BI 服务都要允许嵌入可视化控件。 两者都提供了 REST API（[Power BI REST API](/rest/api/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk)、[TSI REST API](/rest/api/time-series-insights/time-series-insights-reference-queryapi?WT.mc_id=iotinsightssoln-docs-ercenk)）和 JavaScript SDK（[Power BI JavaScript SDK](https://github.com/Microsoft/PowerBI-JavaScript?WT.mc_id=iotinsightssoln-docs-ercenk)、[TSI JavaScript SDK](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk)），允许大量的自定义项。

## <a name="next-steps"></a>后续步骤

我们介绍了大量的概念，并希望为读者提供一系列起点来了解更多信息，并将技术应用于他们自己的需求。 为此，我们提供了下面一些有用的教程。

- 将数据转换为流
  - [创建按计划运行的逻辑应用](/azure/logic-apps/tutorial-build-schedule-recurring-logic-app-workflow?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [对逻辑应用执行数据操作的代码示例](/azure/logic-apps/logic-apps-data-operations-code-samples?WT.mc_id=iotinsightssoln-docs-ercenk)
  - 多个位置介绍了有关[在一个容器中运行 Azure Functions](/azure/azure-functions/functions-create-function-linux-custom-image?WT.mc_id=iotinsightssoln-docs-ercenk) 以托管 Azure 函数的信息。 使用自定义映像在 Linux 上创建函数，在任何平台上运行函数，在 Azure Functions 运行时运行 Docker 映像
  - [在 Azure Functions 上使用各种绑定](/azure/azure-functions/functions-triggers-bindings?WT.mc_id=iotinsightssoln-docs-ercenk)

- 热路径
  - 端到端教程演示了事件中心、Azure 流分析和 Power BI 的使用。 有关分步说明，请参阅[教程：教程：将发送到 Azure 事件中心的实时事件中的数据异常可视化](/azure/event-hubs/event-hubs-tutorial-visualize-anomalies?WT.mc_id=iotinsightssoln-docs-ercenk)以及[创建一个流分析工作来分析电话呼叫数据](/azure/stream-analytics/stream-analytics-manage-job?WT.mc_id=iotinsightssoln-docs-ercenk)，并在 Power BI 仪表板中将结果可视化。
  -[在 .NET 中使用 Azure Cosmos DB](/azure/cosmos-db/sql-api-get-started?WT.mc_id=iotinsightssoln-docs-ercenk)
- 冷路径
  - 在 Azure 数据工厂中[使用 Spark 活动转换云中的数据](/azure/data-factory/tutorial-transform-data-spark-portal?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [教程：创建 Azure 时序见解环境](/azure/time-series-insights/tutorial-create-populate-tsi-environment?WT.mc_id=iotinsightssoln-docs-ercenk)
- 分析客户端
  - [学习 Power BI](/power-bi/guided-learning/?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [创建时序见解 SPA](/azure/time-series-insights/tutorial-create-tsi-sample-spa?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [探索时序见解 JavaScript 客户端库](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk)
  - 请参阅 [TSI 演示](https://insights.timeseries.azure.com/demo)和 [Power BI 演示](https://microsoft.github.io/PowerBI-JavaScript/demo/v2-demo/index.html)。

## <a name="appendix-pillars-of-software-quality-posq"></a>附录：软件质量的构成要素 (PoSQ)

一个成功的云应用程序应基于[软件质量的五大构成要素](/azure/architecture/guide/pillars?WT.mc_id=iotinsightssoln-docs-ercenk)而构建：可伸缩性、可用性、复原能力、管理和安全性。 在本节中，我们将根据需要简要介绍每个组件的上述构成要素。 我们不会对可用性、复原能力、管理和 DevOps 进行说明，因为它们主要在实现级别上讨论，而我们想要提到的是，Azure 平台提供了通过 API、工具、诊断和日志记录来实现这些功能的广泛方法。 除了上面提到的构成要素，还将涉及到成本效益。

让我们快速查看这些构成要素：

- 可伸缩性  是指系统处理增加的负载的能力。 应用程序可通过两种主要方式进行扩展。 垂直扩展（纵向扩展）指增加资源的容量，例如通过使用更大的 VM。 水平扩展（横向扩展）指添加资源的新实例，比如 VM 或数据库副本。 可伸缩性构成要素还包括性能和处理负载的能力。
- 可用性  指系统正常工作时间所占的比例。 通常通过运行时间百分比衡量。 应用程序错误、基础结构问题和系统负载都会降低可用性。 Microsoft Azure 服务的服务级别协议已发布并在[服务级别协议](https://azure.microsoft.com/support/legal/sla/?WT.mc_id=iotinsightssoln-docs-ercenk)中提供。 可用性是系统级别上唯一有意义的指标。 单独的组件有助于系统的整体可用性。
- 复原能力  是指系统从故障中恢复并继续正常运行的能力。 复原能力的目标是在故障发生后将应用程序恢复到可完全正常运行的状态。 复原能力与可用性密切相关。
- **管理和 DevOps**。 此构成要素涵盖让应用程序在生产环境中持续运行的操作过程。 部署必须可靠且可预测。 它们应实现自动化，以减少人为失误的可能性。 它们应当是一个快速、例行的过程，这样就不会减缓新功能或 bug 修复的发布速度。 如果更新出现问题，你必须能够快速回滚或前滚，这一点也同样重要。
- 安全性  应该是从设计和实施到部署和操作的整个解决方案生命周期的主要关注点。 标识管理、保护基础结构、应用程序安全性、授权、数据主权和加密、审核是需要解决的所有广泛领域。

## <a name="posq-converting-the-data-to-a-stream"></a>PoSQ：将数据转换为流

**可伸缩性**：我们可以从两个角度来考虑可伸缩性。 首先，从组件的角度来看，其次，从提供源数据的系统的角度来看。

每个 Azure 服务都提供了垂直和水平缩放的选项。 强烈建议在设计解决方案时考虑可伸缩性需求。

对于提供源数据的系统，我们需要小心，不要让系统不堪重负，并且不要通过频繁地查询系统而导致系统遭受拒绝服务 (DoS) 攻击。 如果正在轮询系统，应记住，调整轮询频率有两个影响：数据粒度（查询频率越高，就越接近实时）以及在远程系统上创建的负载。

**安全性**：如果通过对称或非对称密钥来访问远程系统，建议将机密保留在 [Azure Key Vault](/azure/key-vault/?WT.mc_id=iotinsightssoln-docs-ercenk) 中。

## <a name="posq-warm-path"></a>PoSQ：暖路径

**可伸缩性**：如果 Azure 事件中心在引入子系统中使用，则主要可伸缩性机制是[吞吐量单位](/azure/event-hubs/event-hubs-features#throughput-units?WT.mc_id=iotinsightssoln-docs-ercenk)。 事件中心以静态方式提供设置吞吐量单位的功能，或者通过[自动膨胀功能](/azure/event-hubs/event-hubs-auto-inflate?WT.mc_id=iotinsightssoln-docs-ercenk)进行设置。

流分析中的[流单元](/azure/stream-analytics/stream-analytics-streaming-unit-consumption?WT.mc_id=iotinsightssoln-docs-ercenk) (SU) 表示分配用于执行作业的计算资源。 SU 的数量越多，为作业分配的 CPU 和 内存资源就越多。 此容量使你能够专注于查询逻辑，并且无需管理及时运行流分析作业所需的硬件。 除了 SU 之外，通过[正确地并行化查询](/azure/stream-analytics/stream-analytics-scale-jobs?WT.mc_id=iotinsightssoln-docs-ercenk)来高效地使用它们至关重要。

Azure Cosmos DB 实现需预配正确的吞吐量参数和适当的分区设计。 预配吞吐量可以在容器或数据库级别进行，并以[请求单位](/azure/cosmos-db/request-units?WT.mc_id=iotinsightssoln-docs-ercenk) (RU) 表示。 Cosmos DB 提供了用于估算 RU 的工具。 除了预配吞吐量外，[有效地对数据库分区](/azure/cosmos-db/partition-data?WT.mc_id=iotinsightssoln-docs-ercenk)也是关键。

**安全性**：客户端对 Azure 事件中心的访问是通过用于客户端身份验证的共享访问签名 (SAS) 令牌与事件发布者的组合实现的。 后端应用程序的安全性遵循与服务总线主题相同的概念。 有关事件中心安全模型的详细描述，请参阅[事件中心身份验证和安全模型概述](/azure/event-hubs/event-hubs-authentication-and-security-model-overview?WT.mc_id=iotinsightssoln-docs-ercenk)。

确保 Cosmos DB 数据库的安全提供了对数据的受控访问权限和静止加密。 有关详细信息，请参阅 [Azure Cosmos DB 数据库安全性](/azure/cosmos-db/database-security?WT.mc_id=iotinsightssoln-docs-ercenk)。

**成本效益**：事件中心的定价取决于 SKU（标准或高级）、接收的数百万事件，以及吞吐量单位。 通过查看传入消息指示的数据引入速率，可以实现最佳组合。

当使用 Cosmos DB 时，建议通过 RU 利用率观察存储的最优使用。 如上文所述，Cosmos DB 还有一个控制数据保留的功能，建议使用该功能来控制记录在数据库中的保留时间，从而控制数据库大小。

## <a name="posq-cold-path"></a>PoSQ：冷路径

**可伸缩性**：Azure 时序见解 (TSI) 根据一个名为“容量”的指标进行缩放，该指标是一个乘数，用于计算入口速率、存储容量以及与 SKU 相关的成本。 

Azure 时序见解拥有多个 SKU，这些 SKU 也会对其垂直缩放产生直接影响。 有关缩放的详细信息，请参阅文档[规划 Azure 时序见解环境](/azure/time-series-insights/time-series-insights-environment-planning?WT.mc_id=iotinsightssoln-docs-ercenk)。 与许多其他 Azure 服务一样，TSI 也会受到限制，以避免“邻近干扰”问题。 邻近干扰是共享环境 /azure/sql-database/sql-database-service-tiers-vcore?WT.mc_id=iotinsightssoln-docs-ercenk 中的应用程序，它独占资源并导致其他用户缺乏可用资源。 有关管理限制的信息，请参阅 [TSI 文档](/azure/time-series-insights/time-series-insights-environment-mitigate-latency?WT.mc_id=iotinsightssoln-docs-ercenk)。 

有关存储帐户的可伸缩性目标，请参阅 [Azure 存储可伸缩性和性能目标](/azure/storage/common/storage-scalability-targets?WT.mc_id=iotinsightssoln-docs-ercenk)。 存储超出单个存储帐户容量的数据的一种常见技术是跨多个存储帐户进行分区。

根据购买模型（基于 [DTU](/azure/sql-database/sql-database-service-tiers-dtu?WT.mc_id=iotinsightssoln-docs-ercenk) 和基于 vCore），Azure SQL 数据库有许多选项来垂直和水平管理可伸缩性。 建议进一步研究，使用本主题的 [SQL 数据库文档](/azure/sql-database/sql-database-scale-resources?WT.mc_id=iotinsightssoln-docs-ercenk)找到未来解决方案的最佳选择。

**安全性**：TSI 环境为相互独立的管理访问和数据访问提供[访问策略](/azure/time-series-insights/time-series-insights-data-access?WT.mc_id=iotinsightssoln-docs-ercenk)。 除了已定义的数据源之外，没有其他方法可以直接向 TSI 环境添加数据。 管理访问策略授予的权限与环境配置相关。 数据访问策略授予的权限适用于：发出数据查询、操作环境中的引用数据，以及共享已保存的与环境关联的查询和透视。

Azure 数据工厂服务提供了多种方法来保护数据存储凭据，无论是在其托管存储中，还是在 Azure Key Vault 中。 传输中的数据加密取决于数据存储传输（例如 HTTPS 或 TLS）。 静态数据加密也依赖于数据存储。 有关更多详细信息，请参阅 [Azure 数据工厂中数据移动的安全注意事项](/azure/data-factory/data-movement-security-considerations?WT.mc_id=iotinsightssoln-docs-ercenk)。

SQL 数据库提供了一组广泛的针对数据访问的安全功能，用于监视、审核和加密静态数据。 有关详细信息，请参阅 [SQL Server 数据库引擎和 Azure SQL 数据库安全中心](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database?WT.mc_id=iotinsightssoln-docs-ercenk)。

**成本效益**：任何分析解决方案的核心都是存储。 分析引擎需要速度、效率、安全性和吞吐量，以便在合理时间内处理大量数据。 通过聚合和汇总数据，以及有效地使用多语言存储来充分利用基础平台的设计机制，是有效管理成本的方式。 由于 Azure 是一个云平台，可以通过一些方法以编程方式停用、重用和调整资源大小。 例如，[创建或更新操作](/rest/api/sql/databases/createorupdate?WT.mc_id=iotinsightssoln-docs-ercenk)提供了一种方法来更改 Azure SQL 数据库的数据库大小。