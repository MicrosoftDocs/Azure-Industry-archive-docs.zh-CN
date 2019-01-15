---
title: 预测性维护解决方案
author: ercenk
ms.author: ercenk
ms.date: 05/03/2018
ms.topic: article
ms.service: industry
description: 有关如何为 Azure 上的制造客户开发预测性维护的解决方案说明。
ms.openlocfilehash: 1c7b95e2da21df46465ccaf21827ae97597206a2
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654314"
---
# <a name="predictive-maintenance-in-manufacturing-solution-guide"></a>制造解决方案指南中的预测性维护


## <a name="introduction"></a>介绍

预测性维护综合使用传感器、人工智能和数据科学来优化设备维护。 预期的设备维护需要最大程度地降低维护成本并最大程度地提高正常运行时间，从而为制造商提供巨大价值。

数据是解决方案的核心。 数据需要有足够的故障指示器，以及描述上下文的其他方面。 它可以来自多个来源，例如传感器、机器日志和制造应用程序日志。

本文介绍了生成预测性维护解决方案的选项。 它提供了不同的视角并引用现有材料，以帮助你入门。 预测性维护解决方案的要求因设备、环境、流程和组织而异；我们正在尝试提供替代方法和技术，以指导你提出你所需的解决方案。

让我们从预测性维护解决方案的高级别组件开始。


![高级别的解决方案](./assets/pdm-assets/highlevelsolution.png)

在此明细中，发生以下高级别活动：

1. 收集定型数据，包括失败数据

2. 利用此定型数据，训练机器学习 (ML) 模型，以在给定一组条件的情况下预测未来资产故障

3. 继续不断收集数据

4. 将收集的数据输入 ML 模型，该操作可预测故障，通常具有一定的可信度（例如：“机器在未来 24 小时内出现故障的可能性为 85%”）

5. 显示预测的故障情况

6. 根据数据中的见解进行规划并采取操作

## <a name="training-the-ml-model"></a>定型 ML 模型

生成 ML 模型需要足够、正确且完整的数据。 此外，预测性维护带来了独特的挑战，其中一个主要问题是故障数据的可用性。 故障是相对罕见的事件 - 特别是在投资较大的设备中（例如 CNC 机器或炼油厂的组件），因此，即使我们长时间收集传感器数据，也可能不具有足够的故障数据。 考虑如何定义“故障”，故障究竟由什么构成？ 设备意外停止工作时是否可称为故障？
设备降级到它无法继续在所需级别运行的级别时是否可称为故障？ 在灾难发生之前，由于金属疲劳引起的组件故障或其他指示故障的指标导致切割机损坏是否可称为故障情况？

## <a name="considering-the-data-needed-for-ml"></a>考虑 ML 所需的数据

还要考虑我们是否正在捕获足够的数据来正确记录这些故障？ 在许多情况下，单独的传感器数据可能不足以识别故障。 有时我们可能需要外部数据来将机器的状态“标记”为故障状态或辅助信息源，例如运算符通过不同的系统捕获故障情况。 该数据可能存在于 ERP、制造执行系统 (MES)、历史库等外部系统中，并且跨越了制造企业中普遍存在的著名 IT/OT 鸿沟，使得保护必要数据的安全更加具有挑战性。

从本质上讲，预测性维护是一个动态问题，因此，相关机器学习模型需要不断刷新（或重新定型）。 如果做得好，预测性维护应可减少故障实例，这是一件好事，但会导致故障数据变得更少。 此外，影响故障的功能可能会改变，致使先前机器学习模型无效。 我们建议定期定型模型，以应对故障状况的任何变化。

“新鲜”数据也意味着将新条件引入模型，而不是先前用于定型模型的条件。 换句话说，我们可以将故障模型化为变量 _x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>，f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>)_ 的函数，但最终我们可能会发现变量 _x<sub>(n+1)</sub>,⋯,x<sub>(m+n)</sub>_ 也会影响故障，因此我们可能需要修改 _f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>(m+n)</sub>)_ 的模型定型。 该模型可能无法很好地检测故障，并且可以生成新模型，包括来自计算机的 MES 日志的数据点以及下一次迭代。

即使没有现代 IoT 环境将数据流式传输到云，定型机器学习模型所需的数据可能也已经存在于 MES、历史库或其他生产系统中。 这只是准备数据的问题，因此可用于定型机器学习模型。

下图说明了定型 ML 模型的典型工作流。 标有“重复”的箭头表明这是一个迭代过程，即随着新数据进入并且条件发生变化，我们不断重新定型模型的过程。 此循环何时需要重复以及重复的频率取决于实现的具体条件，并且需要仔细监测先前生成的模型的性能，以预测在模型“老化”或“降级”时要检测的故障。

继续定型新模型并部署它们还会在管理它们时面临新挑战。 Microsoft 为模型的 CI/CD（持续集成和持续交付）提供 Azure 机器学习模型管理服务。

![机器学习模型生成阶段](assets/pdm-assets/mlmodelbuildingstages.png)

Microsoft 已发布有关如何准备数据和训练机器学习模型的[详细指南](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)。 存在三个典型维护问题以及相关的机器学习算法：

- _对于资产，在接下来的 X 小时内发生故障的概率是多少？_ 回答：0-100%
  - **二进制分类：** 二进制分类是一种机器学习方法，它使用数据将项目或数据行的类别、类型或类确定为两个类之一的成员。 有多种类型的分类算法，Microsoft 发布了一组可用作[机器学习工作室模块](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-classification?WT.mc_id=pdmsolution-docs-ercenk)的算法。
- _资产的剩余使用寿命是多少？_ 回答：X 小时
  - 回归：回归是一类机器学习算法，在给定一组其他变量的情况下预测变量的值。 机器学习工作室包括一组作为[模块](https://docs.microsoft.com/en-us/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-regression?WT.mc_id=pdmsolution-docs-ercenk)的回归算法。
    - 长短时记忆 (LSTM)：[LSTM](http://colah.github.io/posts/2015-08-Understanding-LSTMs/?WT.mc_id=pdmsolution-docs-ercenk) 网络是一种深度神经网络 (DNN)。 DNN 的灵感来自于模拟大脑中个体神经元的行为。 Microsoft 已发布了用于介绍如何使用 LSTM 进行预测性维护的[分步指南](https://docs.microsoft.com/en-us/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)
- _哪种资产最急需服务？_ 回答：资产 X
  - 多类分类：多类分类是一种机器学习方法，它使用数据将项目或数据行的类别、类型或类确定为多于两个类的成员。

同样，引入数据可能意味着利用多个通道，首先批量初始化它，然后继续接收流数据以预测故障，同时将其用于模型的后续生成。

## <a name="bringing-data-to-azure"></a>将数据引入 Azure

Microsoft Azure 提供用于引入和存储数据的各种服务。 我们建议使用批处理方法将数据传输到 Azure（如果尚未存在）。 如果可以将数据作为文件导出为已知格式（例如 csv、json、xml 等），这些都是不错的选择。 你也可以选择在上传之前压缩它们，并在云端进一步处理它们。

- 使用 [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy?WT.mc_id=pdmsolution-docs-ercenk) 上传到 blob 存储（适用于 Windows 和 Linux）

- [将 Blob 存储装载为](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux?WT.mc_id=pdmsolution-docs-ercenk) Linux 上的文件系统

- 使用[导入/导出服务](https://docs.microsoft.com/en-us/azure/storage/common/storage-import-export-service?WT.mc_id=pdmsolution-docs-ercenk)，如果数据量很大，则上传时间会很长

- 在 Windows、Linux 和 MacOS 上[装载 Azure 文件](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-windows?WT.mc_id=pdmsolution-docs-ercenk)共享

如果数据位于 SQL Server 数据库中，你还可以使用[数据迁移助手](https://docs.microsoft.com/en-us/sql/dma/dma-overview?WT.mc_id=pdmsolution-docs-ercenk)将数据上传到 Azure SQL 数据库。

Azure 平台上有各种用于提取、转换和加载 (ETL) 操作的工具和服务。 最突出的服务是 [Azure 数据工厂](https://docs.microsoft.com/en-us/azure/data-factory/?WT.mc_id=pdmsolution-docs-ercenk)，它提供了一整套用于处理数据的功能。 Azure 上的许多 ML 服务通过开放源代码库提供用于操作数据的其他选项。

至于定型 ML 模式，Microsoft Azure 提供了许多选项，所有选项都可以以不同的组合使用。

- [Azure 机器学习服务](https://docs.microsoft.com/en-us/azure/machine-learning/preview/?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure 机器学习工作室](https://docs.microsoft.com/en-us/azure/machine-learning/studio/?WT.mc_id=pdmsolution-docs-ercenk)

- [Data Science Virtual Machine](https://docs.microsoft.com/en-us/azure/machine-learning/data-science-virtual-machine/?WT.mc_id=pdmsolution-docs-ercenk)

- [HDInsight 中的 Spark MLLib](https://docs.microsoft.com/en-us/azure/hdinsight/spark/apache-spark-machine-learning-mllib-ipython?WT.mc_id=pdmsolution-docs-ercenk)

- [Batch AI 定型服务](https://docs.microsoft.com/en-us/azure/batch-ai/?WT.mc_id=pdmsolution-docs-ercenk)

决定使用哪种工具取决于操作的复杂性、团队经验以及数据的大小。

除了云服务成本之外，云解决方案的成本公式还包含许多变量，例如额外的工程、管理、数据传输费用等。在评估成本时使用这些变量，然后做出明智决定。 仅服务不构成总成本公式。

设计用于分析数据和发布模型的过程是详细主题，并且根据所使用的技术而不同。 这些主题超出了本文的范围。 介绍流程的一系列文章以及可用于生成模型的 Azure 服务可用。 Microsoft 还提供了一种用于生成解决方案的系统化方法，使数据科学团队能够在数据生命周期内有效协作。

Microsoft [机器学习文档](https://docs.microsoft.com/en-us/azure/machine-learning?WT.mc_id=pdmsolution-docs-ercenk)是探索生成、部署和管理 ML 和到云的 AI 模型选项的良好起点。

Microsoft Azure 平台为按比例处理数据并生成 ML 模型提供了丰富的选择。 云平台上几乎可无限缩放的的计算和存储功能使生成 ML 和 AI 模型成为可能。 因此，使用 Azure 服务生成模型是实现此数据流的最合理选择。

## <a name="using-the-model"></a>使用模型

一旦有了 ML 模型，我们就需要一种消耗它（或“使用”它）的机制来预测设备维护的需要。 在从设备接收到数据之后，它通过处理层移动以预测未来的故障情况，并为维护团队提供要采取的各种方式。

![使用模型](assets/pdm-assets/usingthemodel.png)

联机时通过将实时传感器数据流式传输到解决方案或脱机时通过定期将传感器数据导入到解决方案可完成数据引入。

Microsoft Azure 平台提供了各种用于引入、处理和存储数据的服务，例如：

- [Azure 事件中心](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure 服务总线](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure IoT 中心](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-what-is-iot-hub?WT.mc_id=pdmsolution-docs-ercenk)

- [Apache Kafka for HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=pdmsolution-docs-ercenk)

与生成 ML 模型的过程不同，使用它不需要大量计算资源。 根据需要，可以将模型部署到云中的服务，或部署到本地工厂车间。

ML 模型的执行位置有两种主要替代方案，仅限本地或云。

## <a name="local-execution"></a>本地执行

ML 模型在本地使用，而数据则发送到云以供引入、存储和进一步处理。 此选项非常适合早期检测至关重要的场景。

![本地执行](assets/pdm-assets/localandcloud.png)

## <a name="cloud-execution"></a>云执行

ML 模型的引入、处理、存储和执行都可以在 Azure 云中进行。 当在多个租户或地理区域之间共享 ML 模型执行结果并且延迟并不重要时，此选项可能更适合。 按照称为[“代表”模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)的模式，可以在本地添加一个可选组件（通常称为“边缘网关”）来执行某些工作，例如数据聚合和投影、流分析等。

在 Azure 上使用该模型有多种方法。 [Azure 机器学习 Web 服务](https://docs.microsoft.com/en-us/azure/machine-learning/studio/consume-web-services?WT.mc_id=pdmsolution-docs-ercenk)是最直接的方法，并使用 [Azure 机器学习工作室](https://docs.microsoft.com/en-us/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=pdmsolution-docs-ercenk)作为创建模型的选择。 还可以选择 [Azure 机器学习模型管理](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview?WT.mc_id=pdmsolution-docs-ercenk)方法，它提供了一整套用于管理模型的服务，并提供 REST API 端点，具有身份验证、负载均衡、自动横向扩展和加密功能。 该模型可以部署到单台计算机（例如 Data Science Virtual Machine、IoT 设备、本地 PC）或 [Azure 容器服务](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes?WT.mc_id=pdmsolution-docs-ercenk)。 一旦通过 REST API 公开此模型，从自定义应用程序到企业解决方案集成，它的用途是无穷无尽的。

![仅限云](assets/pdm-assets/cloudonly.png)

仅限云的部署并不一定意味着仅存在数据实时流式传输。 潜在策略是定期从本地系统（例如，历史库或 MES）导出数据，将其导入云系统，并呈现结果。 当设备无法将数据直接推送到系统、现有系统已经在收集数据或者不需要准实时数据处理时，此选项可能是一种可行的方法。 在这些情况下，无需考虑边缘网关。

## <a name="predictive-maintenance-in-the-iot-context"></a>IoT 上下文中的预测性维护

许多 IoT 解决方案都会将数据作为其功能集的一部分进行引入和存储。 由于预测性维护解决方案通常依赖于 IoT 数据，因此它们可以成为添加到 IoT 解决方案的自然功能。 在这种情况下要强调的一个关键点是，记录现有数据中的故障以定型故障的预测模型的重要性。

一些用例需要进行准实时数据处理。 在这些情况下，我们需要具有高数据引入率功能的可缩放 IoT 解决方案。 Microsoft Azure 平台提供了许多服务，以便为可高度缩放的 IoT 需求提供解决方案。 Azure 平台上的 [Microsoft IoT 解决方案体系结构](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-what-is-azure-iot?WT.mc_id=pdmsolution-docs-ercenk)在三个阶段具有逻辑组件：

- 设备连接

- 数据处理和分析

- 呈现

![IoT 解决方案体系结构](assets/pdm-assets/iot.png)

Azure IoT 解决方案体系结构的详细信息[可联机获取](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf?WT.mc_id=pdmsolution-docs-ercenk)。
但是，由于可能有大量设备连接到后端服务，因此可能会出现独特挑战。

## <a name="data-ingestion-and-stream-processing"></a>数据引入和流式传输处理

从设备引入数据是两项单独服务之间的通信问题，即生成数据（设备）的系统和处理该数据的系统（即定型 ML 模型、针对定型模型运行传入数据点以预测剩余使用寿命）。

根据定义，分布式系统由不同的组件组成，这些组件具有彼此通信的固有需求。 用于实现通信的一个选项可以是使相关组件彼此直接对话。 这将创建一个难以维护和缩放的系统。 随着组件数量的增加，它将产生通信链接的 _O(n<sup>2</sup>)_ 复杂性。 更好的方法是在公共集线器上发布和读取数据。

![子组件通信](./assets/pdm-assets/subcomponentcommunication.png)

为数据引入注入新组件可使通信更具可缩放性。 该组件需要具有可缩放性、安全性并且大概可以进行全局访问，具有异地分区数据引入进程的选项。 

考虑预测性维护是 IoT 解决方案的一个特征。 当数据流通过网关时，需要将其路由到与预测性维护功能相关的服务。
另一种需要考虑的模式是[网关路由](https://docs.microsoft.com/en-us/azure/architecture/patterns/gateway-routing?WT.mc_id=pdmsolution-docs-ercenk)。

这两种模式都可以使用 Azure 服务、[IoT 中心](https://azure.microsoft.com/en-us/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk)和 [Azure 流分析](https://azure.microsoft.com/en-us/services/stream-analytics/?WT.mc_id=pdmsolution-docs-ercenk)来实现。

## <a name="edge-and-cloud-processing-cooperation"></a>Edge 和云处理协作

并非所有设备和装置都可以直接稳定地访问互联网。
有时它们的数据需要从通用网关中拉取出来。 例如，[MTConnect](http://www.mtconnect.org/) 代理仅提供用于拉取数据的 REST 接口。

可能存在其他考虑因素，例如延迟，在将设备数据发送到云之前需要在本地推移设备数据（多租户情况），以及需要对设备数据执行投影或聚合。 [代表模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)是解决这些需求的好方法。 [Microsoft Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-iot-edge-works?WT.mc_id=pdmsolution-docs-ercenk) 是一种实现，可以充当 [Microsoft Azure IoT 中心](https://azure.microsoft.com/en-us/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk)的代理，并提供本地处理及远程管理功能。

常见部署包括车间的准实时警报，同时仍推移数据并将其发布到云中的多租户解决方案，以进行存档、模型定型和非时间关键报告。 借助 Azure IoT Edge 和 IoT 中心的功能，客户可以控制边缘设备上的数据筛选选项，以及与其他车间系统交互以发送警报。

![多租户](assets/pdm-assets/multitenant.png)

## <a name="multitenant-perspective"></a>多租户角度

如前所述，某些制造商或第三方可能希望为其客户提供预测性维护服务。 这些服务很可能会在多租户云部署中提供，这会带来一系列挑战：

### <a name="data-security-and-isolation"></a>数据安全性和独立性

提供服务的一方必须确保可识别并妥善保护或推移其客户的机密信息。Microsoft Azure 提供了根据所使用的存储服务加密数据的功能。

设备生成和提交数据的方式也需要使用已知方法进行保护，例如每设备证书、每设备启用/禁用、TLS 安全、X.509 支持、IP 允许列表/阻止列表以及共享访问策略。 提供服务的一方必须确保可识别并妥善保护或推移客户的机密信息。[Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption?WT.mc_id=pdmsolution-docs-ercenk)、[Azure 存储](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption?WT.mc_id=pdmsolution-docs-ercenk)、[Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest?WT.mc_id=pdmsolution-docs-ercenk) 和 [Azure SQL 数据库](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?WT.mc_id=pdmsolution-docs-ercenk)是可用于加密静态数据的服务示例。 解决方案提供商还应考虑如何在同一资源（例如数据库）或多个资源内对数据进行分区。 

### <a name="geographical-considerations"></a>地理位置注意事项

最有可能的情况是，生成数据的设备在地理上是分散的。 解决方案需要提供最接近数据源的引入点。 有时可能会出现连续连接问题，并且必须批量引入数据，或者需要存在本地存储转发机制。

### <a name="scalability"></a>可伸缩性

生成 ML 模型需要可弹性缩放的计算资源。
解决方案提供商必须设计有效使用计算资源的进程，并使用进程按需缩放解决方案。

### <a name="provisioning-tenants-and-secure-access"></a>预配租户和安全访问

服务提供商需要设计有效加入新租户的方法，并为他们提供自己管理帐户的方法。 作出部署到独占或共享资源决定时也是这样。


## <a name="pillars-of-software-quality-review"></a>软件质量评审的构成要素 

除了满足功能要求之外，复杂系统还需要进行额外的详细审查。 一个成功的云解决方案应注重五大构成要素：可伸缩性、可用性、复原能力、管理和安全性。 除了五大构成要素外，我们还希望提高解决方案的成本效益。

有关详细信息，请参阅[软件质量的构成要素](https://docs.microsoft.com/en-us/azure/architecture/guide/pillars?WT.mc_id=pdmsolution-docs-ercenk)文章。

| 构成要素                      |                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 可伸缩性                 | 大多数 Azure 服务都支持垂直和水平缩放选项。 利用 Azure 平台上的资源按需部署功能，并通过自动化服务控制其规模（实例大小和数量）。                                                                                                                                                                   |
| 可用性和复原能力 | 可以使用许多 Azure 服务根据需要弹性预配计算和存储资源。 所有 Azure 服务都提供不同级别的 SLA，但是解决方案必须通过使用适当的设计原则来考虑和利用 SLA 级别。                                                                                                                    |
| 管理                  | 可以通过各种方式部署和管理 Azure 资源，例如 ARM 模板、命令行工具、PowerShell cmdlet 以及 Azure 管理 API。 考虑在管理 Azure 资源时生成自动化解决方案，而不是使用带有 UI 的工具。                                                                                                                                |
| 安全                    | Azure IoT 中心支持 TLS 上的对称和非对称密钥（X509 证书和 TPM）。 数据存储使用标识和访问管理 (IAM) 设置进行保护，它们还支持静态数据加密。 作为常规高级安全清单，请考虑查看授权、身份验证、传输和静态加密以及审核机制。 |
| 经济高效              | 考虑在需要时预配资源，并在不使用时自动放弃资源。                                                                                                                                                                                                                                                                                                  |

## <a name="conclusion"></a>结束语

预测性维护是长期以来一直在讨论的主题。 云平台的最新发展（如 Microsoft Azure）使预测性维护的实施者能够克服过去在处理数据时成为阻碍的许多挑战。 凭借计算和存储容量的弹性缩放，云平台为实施预测性维护提供了新的机会，同时提供了新的收益机遇。 Microsoft Azure 平台提供了许多不同功能的服务，以实现预测性维护解决方案的业务目标。

本文提供了如何收集数据并定型数据模型，以及利用定型模型对前面部分中预测的结果采取操作的愿景。

## <a name="further-reading"></a>延伸阅读

1. [面向未来：停止缅怀过去，并通过 IoT 展望不可预知的未来](https://blogs.microsoft.com/iot/2017/02/28/future-focused-stop-thinking-in-the-past-and-get-ahead-of-the-unexpected-with-iot-2/?WT.mc_id=pdmsolution-docs-ercenk)

2. [通过已启用 IoT 的预测性维护提高设备可靠性](https://www.microsoft.com/en-us/internet-of-things/predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)

3. [获取物联网的价值：如何处理预测性维护项目](http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF?WT.mc_id=pdmsolution-docs-ercenk)

4. [合作伙伴透视：一线预测性维护](https://blogs.microsoft.com/iot/2017/03/21/partner-perspectives-predictive-maintenance-on-the-frontlines/?WT.mc_id=pdmsolution-docs-ercenk)

5. [从商品化到服务化：转换业务，以便在新的现场服务时代通过 IoT 进行竞争](https://blogs.microsoft.com/iot/2016/11/07/from-commodization-to-servitization-transforming-your-business-to-compete-in-the-new-age-of-field-service-with-iot/?WT.mc_id=pdmsolution-docs-ercenk)
