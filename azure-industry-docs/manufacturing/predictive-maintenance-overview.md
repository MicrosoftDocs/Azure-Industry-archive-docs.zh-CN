---
title: 制造业预测性维护简介
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 有关如何为 Azure 上的制造客户开发预测性维护的概述。
ms.openlocfilehash: 14ef249685c9ee90846dbfeba993742f7502037b
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77053821"
---
# <a name="predictive-maintenance-in-manufacturing-overview"></a>制造商预测性维护概述

## <a name="introduction"></a>介绍

预测性维护 (PdM) 可预测维护需求，以避免计划外的停机产生成本。 通过连接到设备和监视设备生成的数据，可以标识导致发生潜在问题或故障的模式。 然后，在这些问题发生之前，可以使用这些见解来解决问题。 这一预测设备或资产何时需要进行维护的功能，使用户能够优化设备生存期并最大限度地减少停机时间。

PdM 从车间的设备所生成的数据中提取见解，并依照这些见解执行操作。 Pdm 的概念可追溯到 20 世纪 90 年代初，并扩展了常规计划的预防性维护。 在早期，由于缺乏生成数据的可用传感器以及缺乏收集数据和分析数据的计算资源，所以很难实现 Pdm。 今天，物联网 (IoT)、云计算、数据分析和机器学习 (ML) 的发展使预测性维护能够走向主流。

PdM 要求设备提供来自监视设备的传感器的数据以及其他操作数据。 Pdm 系统将分析这些数据并存储结果。 人们基于这些分析采取措施。

为本文提供一些背景知识后，我们将探讨如何结合使用本地数据、Azure 机器学习来实现各个部分的 Pdm 解决方案以及机器学习模型的使用。 Pdm 在进行决策时非常依赖于数据，因此，我们先来了解数据收集。 必须收集数据，然后将其用于评估现在发生的情况，并用于在以后生成预测性更好的模型。 最后，我们将解释分析解决方案是什么，其中包括在报表工具（如 [Power BI](https://docs.microsoft.com/power-bi/)）中可视化分析结果。

## <a name="maintenance-strategies"></a>维护策略

在制造业的历史上，出现了几种维护策略。 响应式维护在问题出现后修复问题。 预防性维护按照基于以前发生的故障的维护计划在问题出现前修复问题。 Pdm 也是在问题出现前修复问题，但会考虑设备的实际使用情况，而不是使用固定的计划。 在以上三种维护策略中，Pdm 是最难实现的维护策略，这是因为过去在数据收集、处理和数据可视化方面存在限制。 让我们更为深入地了解每个选项。

响应式维护体现的是&quot;如果没有出现故障，则不对其进行修复&quot;的思维模式。 仅在资产出现问题时才对其进行维护。 例如，只有在你的 5 轴 CNC 加工中心的电机停止工作时才对其进行维护。 响应式维护最大程度地延长最终出现故障的组件的生存期。 响应式维护还导致未知的停机时间、由发生故障的组件导致的对组件的意外附加伤害，以及其他问题。

预防性维护要求你在预先确定的时间间隔维护资产。 时间间隔通常基于资产在过去曾出现故障的频率。 这些时间间隔基于历史性能、模拟、统计建模等。此策略的优点是，它会增加运行时间、降低故障，并可计划维护。 在许多情况下，其缺点是资产上被替换的组件可能还具有一些生存期。 此结果导致过度维护和浪费。 另一方面，某些部分在计划的维护到来前仍可能出现故障。 你可能很了解预防性维护：在固定的若干小时的操作（或某些其他指标）后，停止使用机器并检查机器。 替换任何在到期时间应被替换的部件。

预防性维护使用模型来预测资产可能出现组件故障的时间，以监视资产的使用情况。 然后，组件将具有针对实时维护&quot;&quot;计划的维护。 PdM 通过最大化正常运行时间和资产生命周期在以前的策略之上进行了改进。 因为你在接近组件最大生存期的时候维护设备，所以替换工作组件的成本会更低。 缺点是 PdM 的实时特性更难执行，因为它要求具有更好的响应性和更为灵活的服务组织。 回到 5 轴 CNC 加工中心的电机的例子，例如，如果预测模型预测电机在未来 24 小时内出现故障的可能性的概率为 75%（基于来自机器中的传感器的信息），则应&quot;合理&quot;地计划其维护（例如，在不中断生产的情况下预先计划）。

 ![](./assets/pdm-assets/maintenancestrategies.png)


## <a name="different-ways-pdm-can-be-offered"></a>可提供 PdM 的不同方法

制造商可直接使用 PdM 解决方案来监视其自己的生产操作中的数据。 还有供其他组织使用的其他方法，这意味着新的商机和收益。 例如：

- 制造商通过为其产品提供预测性维护服务为其客户增添价值。
- 制造商在产品即服务模型下提供其产品，即客户&quot;订阅&quot;产品，而不是直接购买产品。 在此模型下，制造商希望最大程度地延长产品正常运行时间；在不使用该产品时，它不会产生收入。
- 公司为由一个或多个制造商生产的产品提供预测性维护产品和服务。

## <a name="building-a-predictive-maintenance-solution"></a>生成预测性维护解决方案

若要生成 PdM 解决方案，我们先从数据开始；理想的数据是显示正常操作的数据以及显示设备在出现故障前、出现故障期间和出现故障后的数据。 数据来自传感器、设备操作员维护的注释、运行信息、环境数据、机器规格等。记录系统可能包含历史学家、制造执行系统、ERP 等。可以通过多种方式对数据进行分析。 [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)（如下图所示并针对生产进行了自定义）可以很好地解释在生成和执行机器学习模型时的各种问题。

 ![](./assets/pdm-assets/DataScienceDiagram.png)


你的首要任务是确定你想要预测的故障类型。 明确这一点后，标识具有围绕这一故障类型生成的数据的数据源。 管道将数据从你的环境引入系统。 数据科学家将使用其最喜欢的机器学习工具来准备数据。 此时，他们已准备好创建模型并定型，以识别不同类型的问题。 该模型将回答以下问题：

- _对于资产，在接下来的 X 小时内发生故障的概率是多少？_ 回答：0-100%
- _资产的剩余使用寿命是多少？_ 回答：X 小时
- _该资产的行为是否异常？_ 回答：是或否
- _哪种资产最急需服务？_ 回答：资产 X

开发后，模型可能位于设备本身以供自我诊断（位于生产环境某处的边缘设备中或在 Azure 中）。 你还会继续将数据从主源发送到中央存储，以便你能够继续生成和维护 PdM 解决方案。

Azure 的强大功能使你能够在你所选的技术上定型和测试模型。 你可以利用 GPU、FPGA、CPU、大型内存计算机等。 使用 Azure 的最大优势之一是该平台完全支持数据科学家使用的开源工具（例如 R 和 Python）。 分析完成后，结果可显示在仪表板的其他分面或显示在其他报表中。 这些报表可能显示在自定义工具中，或者，可以利用报表工具，例如 [Power BI](https://docs.microsoft.com/power-bi/) 或[时序见解](https://docs.microsoft.com/azure/time-series-insights/)。

无论你的 PdM 需要什么内容，Azure 都能提供生成可靠的解决方案所需的工具、扩展和功能。

## <a name="getting-started"></a>入门

在车间有许多收集数据的设备以及提供从设备收集数据的机制。 尽快开始收集这些数据。 发生故障时，让数据科学家分析数据，以创建能够检测未来故障的模型。 随着有关故障检测方面的知识的增加，将移至预测模式，该模式在计划的停机期间修复组件。 [预测性维护建模指南](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1)提供如何生成解决方案的机器学习部分的详细演练。

若要查看示例解决方案，请查看针对[航空工业中的 PdM](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace) 的解决方案、指南和演练手册。 如果需要深入学习构建模型，我们建议访问 [AI 学校](https://aischool.microsoft.com/)。 [使用 Azure ML 的机器学习简介](https://aischool.microsoft.com/learning-paths/4ZYo4wHJVCsUSAKa2EoAk8)课程将帮助你熟悉我们的工具。

## <a name="technologies-presented"></a>所提供的技术

[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)可根据数据访问频率，将数百至数十亿的对象存储在“热”、“冷”或“存档”层中。

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) 是具有极低延迟的数据库和可在全球任意位置大规模缩放的应用程序，享受 NoSQL 本机支持。

[Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/) 包括了所有所需的功能，使开发人员、数据专家和分析师可以更轻松地存储任何大小、形状和速度的数据以及跨平台和语言进行各种类型的处理和分析。

[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)是超大规模的遥测引入服务，可收集、传输和存储数百万的事件。 作为分布式流式处理平台，它为用户提供低延迟和可配置的时间保留，使用户可以将大量遥测数据引入到云中，并使用发布-订阅语义从多个应用程序中读取数据。

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) 是基于物联网 (IoT) 中心构建的 IoT 服务。 此服务供想要在设备上 &quot;在边缘上&quot;，而不是在云中。 通过将部分工作负荷移至边缘，设备将消息发送到云所花费的时间可以更少，并且设备可以对状态更改更快地做出响应。

[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)是一项完全托管的服务，可在数百万个 IoT 设备和一个解决方案后端之间实现安全可靠的双向通信。

[Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/)使计算机能够从数据和经验中学习，无需经过显式编程便可操作。 客户可以构建能够智能感知、处理和操作信息的人工智能 (AI) 应用程序，提升用户能力、提高组织速度和效率以及帮助组织实现更多成果。

[Azure 服务总线](https://docs.microsoft.com/azure/service-bus/)是一种中转通信机制。 服务总线消息传送基础结构的核心组件是队列、主题和订阅。

[Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/)是智能、完全托管的关系型云数据库服务，提供最广泛的 SQL Server 引擎兼容性，因此你可以在不更改应用的情况下迁移 SQL Server 数据库。

[Power BI](https://docs.microsoft.com/power-bi/) 是一套业务分析工具，用于在整个组织内提供见解。 连接数百个数据源、简化数据准备工作、加快临时分析。

[时序见解](https://docs.microsoft.com/azure/time-series-insights/)是一种完全托管的分析、存储和可视化服务，用于管理云中的 IoT 时序数据。

## <a name="conclusion"></a>结束语

PdM 要求机器具有某些级别的检测和连接，以允许我们构建可预测问题的系统，并允许我们在故障发生前采取操作。 PdM 通过确定要检查的特定组件并进行维修或替换来扩充预防性维护计划。 有许多可帮助你入门的资源。 Microsoft 的基础结构可帮助你生成在设备上、在边缘和在云中运行的解决方案。 

若要开始，选择你想要阻止的 1-3 个故障，并使用这些项开始发现过程。 然后，确定帮助识别故障的数据所驻留的位置。 将该数据与你从[使用 Azure ML 的机器学习简介](https://aischool.microsoft.com/learning-paths/4ZYo4wHJVCsUSAKa2EoAk8)中学习的技术结合，以生成你的 PdM 模型。