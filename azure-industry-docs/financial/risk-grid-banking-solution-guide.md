---
title: 风险网格计算解决方案指南
author: dstarr
ms.author: dastarr
ms.date: 5/2/2018
ms.topic: article
ms.service: industry
description: 介绍了为银行业务风险网格计算实施 Azure Batch 的技术方面。
ms.openlocfilehash: d3470a2e546e73f4c0f1478413ca4b1af7433a66
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654294"
---
# <a name="risk-grid-computing-in-banking-solution-guide"></a>银行业务风险网格计算解决方案指南

本文提供了使用 Microsoft Azure 支持和增强银行业务风险网格计算的技术概述，包括建议的系统和高级体系结构。

本文档适用于解决方案架构师，在某些情况下还适用于想要深入了解建议用于风险计算的解决方案的技术决策者。

## <a name="introduction"></a>介绍

财务风险分析模型通常作为批处理作业得以处理，其中繁重的计算负载对计算能力、数据访问和分析能力提出了很高的要求。 对风险网格计算的需求通常随时间推移而增长，计算资源的需求也会相应地增长。

Azure 中广泛的产品和服务意味着大多数问题可以有多个解决方案。 本文概述了使用 [Microsoft Azure Batch](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr) 判定最适用于银行业务风险网格计算解决方案的技术、模式和实践。

Azure Batch 是一项免费服务，可为通常用于风险网格计算模型的基础结构和各个批处理阶段提供经济高效且安全的解决方案。 Azure Batch 可以使用混合网络或通过将整个批处理过程移动到 Azure 中来增强、扩展甚至替换当前的本地计算资源投资。  数据可以在云中向上和向下遍历或保留在本地，而其他数据可以在本地资源不足时在“迸发到云”模型中由计算节点处理。

## <a name="anatomy-of-a-batch-run"></a>批处理运行剖析

批处理运行中通常至少有两个应用程序。 一个应用程序通常在“头节点”上运行，可将作业提交到池，有时安排计算节点。 还可以通过 Azure 门户配置业务流程。 另一个应用程序作为任务由计算节点运行（见图 1）。

计算节点应用程序将执行并行处理风险建模文件的任务。 可以在计算节点上安装并运行多个应用程序。

这些应用程序可以通过 [Batch API](/azure/batch/batch-apis-tools?WT.mc_id=gridbanksg-docs-dastarr)、直接通过 Azure 门户或通过 [Batch 的 Azure CLI 命令](/azure/batch/cli-samples?WT.mc_id=gridbanksg-docs-dastarr)进行上载。

![Azure Batch 网格计算](./assets/risk-grid-compute-assets/05-batch-grid-computing.png)

**图 1：** Azure Batch 网格计算

批处理运行由多个逻辑元素组成。 图 2 显示了批处理作业的逻辑模型。 池是批处理运行中涉及的 VM 的容器，并预配计算节点 VM。 池也是计算节点上安装的应用程序的容器。 作业将在池内创建并运行。 任务将由作业执行。 任务是运行辅助角色应用程序，并通过命令行指令调用。

辅助角色应用程序在创建后将安装到计算节点。

![池、作业和任务](./assets/risk-grid-compute-assets/06-pool-job-logical-model.png)

**图 2：** 逻辑批处理概念模型

执行作业后，池将预配任何所需的辅助角色 VM 并安装辅助角色应用程序。 作业将任务分配给这些计算节点，进而运行通常调用已安装应用程序或脚本的命令行指令。
使用批处理通常遵循原型模式，如下所述：

1. 首先，创建一个资源组，使之包含 Batch 资产。
2. 在该资源组中创建 Batch 帐户。
3. 创建链接的存储帐户。
4. 创建在其中预配辅助角色 VM 的池。
5. 将计算节点应用程序或脚本上载到该池。
6. 创建将任务分配给池中的 VM 的作业。
7. 将该作业添加到池中。
8. 开始批处理运行。
9. 该作业会将要在计算节点上运行的任务排入队列。
10. 计算节点在 VM 变得可用时运行任务。

图 3 显示了此过程的示意图。

![批处理运行过程](./assets/risk-grid-compute-assets/07-batch-run-process.png)

**图 3：** 逻辑批处理概念模型

任务完成后，删除计算节点以防在不使用时产生费用。 若要通过代码或门户删除它们，可以删除包含池，这将删除辅助角色 VM。

有关如何开始使用 Batch 的更多详细演练，[5 分钟快速入门](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)（提供多种语言）可指导你完成此过程，或可通过 Azure 门户完成。

## <a name="batch-process-scheduling"></a>批处理计划

Azure Batch 内置了一个计划程序，因此可以在门户中或通过 API 定义每次运行的计划。 批处理作业计划程序可以定义用来触发多个作业的多个计划。 每个作业都有其自己的属性，如作业开始和结束时所要执行的操作。 可以定期设置作业计划或一次性运行。

许多银行网格计算系统已经有其自己的计划服务。 可能无需立即将我的计划程序移动到 Azure。 可以无缝执行此操作，因为 Azure Batch 可以手动调用或通过 SDK 调用，从而允许计划仍在本地和 Azure 中要处理的工作负荷上发生。

可以按预先确定的计划或按需进行批处理，但在任一情况下无需使未使用的计算节点 VM 保持活动状态。  使用数百个（甚至数千个）VM 计算节点时，可以通过取消预配已完成排队任务的服务器来大幅度节省成本。

## <a name="compute-node-applications"></a>计算节点应用程序

计算节点需要应用程序在调用任务后运行。 这些应用程序由公司编写以在将处理作业安装在辅助角色上时执行这些作业。 在银行业务风险网格计算方案中，此应用程序通常会执行将数据转换为特别适用于下游分析或其他处理的格式的工作。

向池中提供应用程序以分发到计算节点时，会在应用程序包中进行上载。 应用程序包可能是以前上载的应用程序包的另一个版本。 可以将多个应用程序包安装到一个计算节点。 作业包含要加载到辅助角色计算机上的应用程序包。

也可以按版本管理应用程序包部署。 如果已将多个版本的应用程序包加载到池中，则可以指定特定版本以用于批处理运行，如图 4 所示。 这可能在审核环境中或在公司想要重现前一次运行时有必要。 如果辅助角色应用程序中引入了 bug，则还可用于回滚目的。

![批处理运行过程](./assets/risk-grid-compute-assets/08-versioning-worker-applications.png)

**图 4：** 计算节点任务应用程序版本控制

应用程序包作为 .zip 文件（其中包含应用程序二进制文件和应用程序运行任务所需的支持文件）上载到池中。 应用程序包有 2 种范围。 可以在池的范围或任务的范围内指定应用程序包。

## <a name="pool-application-packages"></a>池应用程序包

这些包部署到池中的每个计算节点。 计算节点 VM 进行预配、重新启动或重置映像后，如果存在更新的应用程序，则会安装任何池应用程序包的新副本。 可以将一个或多个应用程序包分配给池，这意味着计算节点将指定所有包。

## <a name="task-application-packages"></a>任务应用程序包

针对任务级别的应用程序包仅安装到计划运行任务的计算节点。 任务应用程序包适合在一个池中运行多个作业时使用。

任务应用程序在聚合池级作业生成的数据时很有用，风险网格计算方案可能与之密切相关。 例如，任务应用程序可能会运行一组风险计算，此计算可生成稍后要在风险计算工作流中使用的数据。

## <a name="scaling-batch-jobs"></a>缩放批处理作业

未充分利用计算资源时，银行通常会在周末或晚上执行风险分析批处理运行。 虽然此模型适用于一些银行，但它会过快增长而无法满足要求，因此需要更多资金来向网格中添加更多辅助角色计算机。

如果批处理作业的运行时间太长，或者希望在批处理运行中具有更多计算能力，则 Azure 会提供多个选项。

1. 分配更多计算节点计算机进行横向扩展。
2. 分配更多功能强大的计算节点计算机进行纵向扩展。 Azure 计算机可能会预配为满足内核和内存，甚至 GPU 计算能力的高性能需求。

> 注意：Microsoft HPC Pack 与 Batch 配合使用是更为复杂的模型，本文未作讨论。

在批处理群集中，可能只有两个处理 VM 或数千个同时任务正在具有数以万计的内核的数千个 VM 计算节点上运行。 每个 VM 负责一次运行一个任务。 池中的 VM 数可能会按负荷增加或减少时配置的比例手动或自动缩放。

### <a name="burst-to-cloud"></a>迸发到云

当本地网格中的计算资源由于执行大型分析作业而不足时，“迸发到云”提供了通过在 Azure 中添加更多计算节点来增强这些资源的方法。 迸发到云是当本地资源的需求提高时私有云或基础结构向云服务器分发其工作负荷所采用的模型。

这些计算节点可以预配置为要在 Azure 的 IaaS 平台中预配的 Linux 或 Windows 虚拟机。 此外，服务器还可以预配并自动配置为使用 Tibco Gridserver 和 IBM Symphony 等现有投资。

### <a name="automatic-scaling-formulas"></a>自动缩放公式

可以在 Azure 门户中或通过使用[自动缩放公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)配置此弹性。 自动缩放公式是上载到批处理计划程序以便对批处理行为进行精细控制的脚本。 在计算节点的池上自动缩放通过将节点与自动缩放公式关联来完成。

下面是自动缩放公式的一个示例，指示自动缩放以一个 VM 开始，并根据需要扩展到 50 个 VM。 任务完成时，VM 会逐个变为可用状态，自动缩放公式会收缩池。

```Formula
startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

### <a name="other-scaling-techniques"></a>其他缩放技术

还可以通过 Enable-AzureBatchAutoScale PowerShell cmdlet 来启用自动缩放。 通过 Enable-AzureBatchAutoScale cmdlet 可以自动缩放指定池。 以下是一个示例。

1. 第一个命令定义公式，然后将其保存到 `$Formula` 变量。
2. 第二个命令使用 `$Formula` 中的公式在名为 `RiskGridPool` 的池上启用自动缩放。

```console
C:\> $Formula = ‘startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second?WT.mc_id=gridbanksg-docs-dastarr);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);’;

C:\> Enable-AzureBatchAutoScale -Id "RiskGridPool" -AutoScaleFormula $Formula -BatchContext $Context
```

还可以使用 Azure CLI 与 `az batch pool resize` 命令和 Azure 门户完成缩放。

## <a name="data-storage-and-retention"></a>数据存储和保留

数据由计算节点引入并进行处理后，生成的输出数据可能会存储在数据库中以进行进一步处理和分析，或在存储前引入时进行转换来确保下游处理的格式正确。 Microsoft Azure 提供了多个存储选项。 [要使用哪个数据存储技术](/azure/architecture/data-guide/?WT.mc_id=gridbanksg-docs-dastarr)的选择很大程度上取决于下游进程中的分析和报告需求。

使用混合网络时，数据存储目标可能是本地数据存储。 通过混合网络使用 Batch 时，计算节点可以将数据写回到本地数据存储，而无需使用基于 Azure 的存储位置。 辅助角色也可以写入 Azure 文件存储，该文件存储可以在本地计算机上装载为磁盘，以便由使用本地文件的任何进程轻松访问。

## <a name="monitoring-and-logging"></a>监视和日志记录

若要优化将来的批处理作业运行，应记录数据以帮助标识优化的区域。 例如，如果辅助角色将要耗尽 CPU 容量，则向计算节点添加内核可帮助避免占用大量 CPU，进而可以更快地完成作业。 批处理作业中的每个应用程序运行都有其自己的特征，并且对批处理运行中的 VM 所做的优化可能会有所不同。 对于内存密集型任务，可以通过在下次运行时以不同的方式配置计算机来分配更多内存。

可以通过计算节点和网格头应用程序或使用 [Batch 诊断日志记录](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)通过作业来完成日志记录。 可以配置有关批处理运行性能的日志记录信息，以帮助标识为提高性能和更快地完成任务而需改进的区域。

### <a name="custom-batch-monitoring-and-logging"></a>自定义 Batch 监视和日志记录

控制应用程序和计算节点应用程序可以生成此数据并进行存储以供进一步分析。 有助于优化批处理作业的数据包括：

- 每个任务的开始和结束时间
- 每个计算节点处于活动状态且正在运行任务的时间
- 每个计算节点处于活动状态且未运行任务的时间
- 批处理作业运行总时间

### <a name="batch-diagnostic-logging"></a>Batch 诊断日志记录

使用控制器和计算节点应用程序发出检测数据存在替代方法。 [Batch 诊断日志记录](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)可以捕获大量运行数据。 默认情况下未启用 Batch 诊断日志记录，但必须为 Batch 帐户启用 Batch 诊断日志记录。

Batch 诊断日志记录提供了大量有助于对批处理运行进行故障排除和优化的数据。 作业和任务的开始和结束时间、内核计数、总节点计数和许多其他指标。

批处理日志记录需要已发出日志的存储目标，用于存储批处理运行生成的事件，例如池创建、作业执行、任务执行等。除了将诊断日志事件存储在 Azure 存储帐户中之外，还可以将 Batch 服务日志事件流式传输到 [Azure 事件中心](/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=gridbanksg-docs-dastarr)，并将它们发送到 [Azure Log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=gridbanksg-docs-dastarr)。

使用这些数据，内核计算和头节点应用程序可能会得到优化。 这样可以降低成本，因为在不再需要辅助角色 VM 时会更快地取消预配这些 VM，而不是等待批处理运行完成。

### <a name="batch-management-tools"></a>Batch 管理工具

Azure 门户提供了 Batch 监视仪表板，其中显示正在运行作业时的 Batch 相关信息，甚至帐户配额使用情况。 这对于许多批处理作业应用程序已经足够了。

除了 Azure 门户中提供的 Batch 管理和可视化工具之外，还有一个免费的开放源代码工具 [BatchLabs](https://github.com/Azure/BatchLabs) 用于管理 Batch。 这是一个独立客户端工具，可帮助创建、调试和监视 Azure Batch 应用程序。 下载适用于 Mac、Linux 或 Windows 的安装包。

## <a name="network-models"></a>网络模型

风险分析通常需要将数百个（甚至数千个）文档引入到风险网格计算过程中。 这些文件通常位于本地文件存储、网络共享或其他存储库。 使用基于 Azure 的 VM 访问和处理这些文件时，将本地网络无缝连接到 Azure 网络非常有益，可使文件访问既简单又快速。 此方法甚至可能意味着在计算节点上进行处理时无需任何代码更改。

Azure 提供了两种模型，用于安全可靠地将当前本地系统连接到 Azure、[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) 和 [VPN 网关](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)。 两者都提供安全可靠的连接，尽管在实现、性能、和[其他属性](/azure/networking/networking-overview?WT.mc_id=gridbanksg-docs-dastarr)方面存在差异。

此外，风险网格计算头节点可能位于本地，并通过 REST API 或 SDK 采用 .NET 和其他语言执行批处理作业。

还有其他方法可用来缩小 Azure 与本地资源之间的差距，而不使用混合网络解决方案。 下面提供了相关详细信息。

### <a name="expressroute"></a>ExpressRoute

ExpressRoute 可通过连接合作伙伴（如当前 Internet 服务提供商 (ISP?WT.mc_id=gridbanksg-docs-dastarr)）提供的专用连接将本地或数据中心网络绑定到 Azure。 这使两个网络能够将双方视为相同的网络实例，从而在网络之间提供无缝访问。 在你想要将现有本地系统与 Azure 网络集成，并且 ExpressRoute 提供尽可能最快的连接速度时，网络集成十分重要。

[可在此处找到](https://azure.microsoft.com/en-us/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr) Azure ExpressRoute 的其他定价信息。

### <a name="vpn-gateway"></a>VPN 网关

VPN 网关是将网络连接到 Azure 的另一种方法。 此模型的缺点是通过 Internet 传送流量。 因此连接可能会缺少弹性，并且网络速度无法达到 ExpressRoute 的速度，但这可能不是风险网格计算方案的障碍，因为读取数据文件通常是一个快速操作。

[可在此处找到](https://azure.microsoft.com/en-us/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr) VPN 网关的其他定价信息。

### <a name="choices-for-connectivity-details"></a>连接详细信息的选项

实质上有两个模型用于将网络扩展到 Azure，如图 5 所示。

1. 虚拟网关 – 站点到站点
2. ExpressRoute – Exchange 或 ISP 提供商

![站点到站点和 ExpressRoute](./assets/risk-grid-compute-assets/10-s2s-expressroute.png)

**图 5：** 站点到站点和 ExpressRoute

#### <a name="virtual-gateway-site-to-site-integration"></a>虚拟网关站点到站点集成

[站点到站点 VPN 网关](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal?WT.mc_id=gridbanksg-docs-dastarr)将本地网络连接到 Azure VNet。 这样可以缩小网络之间的差距，实质上使它们组成同一个网络，从而双向访问资源、服务器和项目。 这样可以从运行风险网格计算批处理作业的 Azure 辅助角色 VM 直接访问数据文件。

#### <a name="expressroute-integration"></a>ExpressRoute 集成

Azure 合作伙伴网络提供商提供的 ExpressRoute 连接实现了与站点到站点连接相同的权益，但速度和可靠性更高。

详细了解 [ExpressRoute 连接模型] （(/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="batch-processing-without-an-azure-hybrid-network"></a>在没有 Azure 混合网络的情况下进行批处理

另一个批处理方案是将所有数据文件上载到 Azure 存储以供基于 Azure 的计算机稍后处理。 文件存储和 Blob 存储可能是存储风险网格计算数据的候选项。

在此方案中，作业控制器和所有计算节点均位于 Azure 中，如图 6 所示。 已处理数据的可能目标是 Azure 数据存储，以准备由 Azure 机器学习解决方案或其他系统进行进一步处理。 此进一步处理超出了本文的范围。

![站点到站点和 ExpressRoute](./assets/risk-grid-compute-assets/09-batch-upload-process.png)

**图 6：** 批处理上载到执行的生命周期

### <a name="hybrid-network-connectivity-resources"></a>混合网络连接资源

可能有多个配置适用于你的情况。 若要帮助制定有关将网络连接到 Azure 的决策和体系结构指南，请参阅模式与实践组提供的_[将本地网络连接到 Azure](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)_ 一文。

- 有关 VPN 网关配置替代方法，[请参阅本文](/azure/vpn-gateway/vpn-gateway-about-vpngateways?WT.mc_id=gridbanksg-docs-dastarr)。
- 了解 [ExpressRoute 连接模型](/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr)。
- 计算 [ExpressRoute 定价](https://azure.microsoft.com/en-us/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。
- 计算 [VPN 网关定价](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)。

## <a name="security-considerations"></a>安全注意事项

可能会创建 Azure [虚拟网络 (VNet)](/azure/virtual-network/virtual-networks-overview?WT.mc_id=gridbanksg-docs-dastarr) 并在其中创建池的计算节点。 这样会为批处理运行提供额外的隔离级别，并允许使用 [Azure Active Directory (AAD)](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) 进行身份验证。 有关详细信息，请参阅[池网络配置](/azure/batch/batch-api-basics#pool-network-configuration?WT.mc_id=gridbanksg-docs-dastarr)。

使用 AAD 对批处理应用程序进行身份验证有以下两种方法：

1. **集成身份验证**。 使用 AAD 帐户的批处理应用程序可以使用该帐户获取数据存储的资源和其他资源。

2. **服务主体**。 AAD 服务主体定义用户和应用程序的访问策略和权限。 服务主体使用绑定到该应用程序的密钥向用户提供身份验证。 这样可以使用密钥对无人参与应用程序进行身份验证。 服务主体定义应用程序的策略和权限，使其能够在运行时访问资源时代表应用程序。 [在此处了解更多信息](/azure/active-directory/develop/active-directory-application-objects?WT.mc_id=gridbanksg-docs-dastarr)。

有关使用 AAD 进行批处理的安全性的详细信息，[请参阅本文](/azure/batch/batch-aad-auth?WT.mc_id=gridbanksg-docs-dastarr)。

Batch 服务还可以使用共享密钥进行身份验证。 身份验证服务需要将两个标头值添加到 HTTP 请求、数据和授权。 有关共享密钥身份验证的详细信息，[请参阅此处](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service?WT.mc_id=gridbanksg-docs-dastarr)。

## <a name="cost-considerations"></a>成本注意事项

Azure Batch 可免费使用。 仅针对所使用的基础资源（例如虚拟机运行时间、存储和网络）计费。 但是，处于空闲状态的计算节点 VM 仍会产生费用，因此最好在不再需要计算机时对其进行取消预配。 通常通过删除包含它们的池来完成此操作。
>
创建池时，可以指定所需的计算节点类型以及每个计算节点的数量。 有以下两种类型的计算节点：
>
>**专用计算节点**将为工作负荷保留。 它们比低优先级节点开销高，但可确保永远不会被抢占。
>
>**低优先级计算节点**利用 Azure 中的多余容量运行 Batch 工作负荷。 低优先级节点每小时的成本比专用节点低，可支持需要大量计算能力的工作负荷。 有关详细信息，请参阅[在 Batch 中使用低优先级 VM](/azure/batch/batch-low-pri-vms?WT.mc_id=gridbanksg-docs-dastarr)。
>
>专用和低优先级节点可能位于同一个池中。
>
>有关低优先级计算节点和专用计算节点的定价信息，请参阅 [Batch 定价](https://azure.microsoft.com/pricing/details/batch/?WT.mc_id=gridbanksg-docs-dastarr)。

使用 Batch 诊断日志记录服务时，发送到 Azure 存储的数据将产生费用。 这是与任何其他数据类似的存储数据，定价会受所保留的诊断数据量影响。

## <a name="getting-started"></a>入门

虽然可以从很多地方开始了解类似于 Batch 风险网格计算的复杂技术，但下面提供了一些逻辑起点可让你更好地了解 Batch 技术。

[建议首先](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)通过编码和 Azure 门户示例使用 Batch。 [在 GitHub 上](https://github.com/Azure/azure-batch-samples)免费提供有 Azure Batch 示例应用程序。

下面是一些快速教程，可帮助你生成简单的应用程序来创建和运行批处理计算作业。 用于生成应用程序的选项如下：

- [Batch .NET API](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [适用于 Python 的 Batch SDK](/azure/batch/batch-python-tutorial?WT.mc_id=gridbanksg-docs-dastarr)
- [适用于 Node.js 的 Batch SDK](/azure/batch/batch-nodejs-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [使用 PowerShell 的 Batch 管理](/azure/batch/batch-powershell-cmdlets-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [使用 Azure CLI 的 Batch 管理](/azure/batch/batch-cli-get-started?WT.mc_id=gridbanksg-docs-dastarr)

请考虑启动概念证明计划。 将数据引入到 Azure 中所用的方法是什么？ 是否将使用混合网络或通过 SDK 或 REST 接口上载数据？ 如果你考虑使用混合网络，请考虑启动试点将其落实到位。

评估批处理计算作业的大小并选择正确的缩放解决方案。 [自动缩放公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)启用复杂的计划方案，而更简单的方案可通过使用 Azure 门户获得。

## <a name="technologies-presented"></a>所提供的技术

[Azure Batch](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr) 提供在云中运行大规模并行处理作业的功能。

[Azure Active Directory](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) 是基于云的多租户目录，也是标识管理服务，可以将核心目录服务、应用程序访问管理和标识保护组合到单个解决方案中。

[自动缩放公式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)是上载到批处理计划程序以便对批处理缩放行为进行精细控制的脚本。

[Batch 诊断日志记录](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)是 Azure Batch 支持从批处理运行和生成的事件中创建详细日志的一项功能。 日志存储在 Azure 存储中。

[BatchLabs](https://github.com/Azure/BatchLabs) 是用于进行适用于 Windows、MacOS 和 Linux 的 Batch 监视和管理的独立应用程序。

[ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) 是用于联接本地和 Azure 网络的高速可靠的混合网络解决方案。

[VPN 网关](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)是使用 Internet 联接本地和 Azure 网络的混合网络解决方案。

## <a name="conclusion"></a>结束语

本文档概述了使用 Azure Batch 进行银行业务风险网格计算时的技术解决方案和注意事项。 本文涵盖甚广，从 Azure Batch 定义到[网络选项](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)均包含在内，甚至还包括了成本注意事项。

在考虑进一步评估使用 Azure Batch 进行风险网格计算时，[此页面](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)是一个良好的开端。 它提供了风险网格计算中固有的并行文件处理的示例指导教程。 这些教程通过 Azure 门户、Azure CLI、.NET 和 Python 提供。
