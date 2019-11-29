---
title: 使用 Azure Batch、Azure Data Lake 进行网格计算风险分析
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 介绍了在 Azure 上实现银行业务风险网格计算的企业注意事项。
ms.openlocfilehash: 746b93e545aa8ff61a8fab4a021b6c5caa1889bb
ms.sourcegitcommit: f030566b177715794d2ad857b150317e72d04d64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234651"
---
# <a name="risk-grid-computing-in-banking-overview"></a>概述：银行业务风险网格计算

## <a name="introduction"></a>介绍

在企业财务和投资银行业务中，最重要的工作之一就是风险分析。

为了全面呈现与投资组合相关的风险，金融风险分析员会研究综述、监视经济和社会条件、掌握最新法规，还会创建投资环境的计算机模型。

由于跨许多影响投资组合的向量执行风险分析非常复杂，因此必须要进行计算机建模。 大多数分析员花费大量时间来使用计算机模型模拟和预测财务状况变化趋势。 评估投资风险（市场风险、信用风险和运营风险）时，由于有大量各种各样的数据，因此处理预测模型的计算负载可能会相当大。

云计算提供了风险网格计算或风险建模的显著优势，因为它可便于分析员按需访问大量计算资源，而不会产生资本成本或无需管理基础设施。 本文介绍了如何利用 Microsoft Azure 增加当前的风险网格计算资源，并优化风险网格计算工作负载的成本和速度。 涵盖的主题包括安全可靠地连接、批处理，以及在本地服务器满负荷时按需增加计算资源。

## <a name="grid-computing-services"></a>网格计算服务

分析员需要通过一种简单可靠的方法，将自己的模型提供给批处理管道。此管道先引入数据，再处理和分析数据，这样便能根据生成的数据得出见解。

风险模型输入数据有多种形式，最常见的形式是 Excel 文件或 .csv 文件。 这些文件通常被重构为，更适合在风险计算管道的后期阶段处理风险模型的格式。 常用于分析和处理这些文件的技术是，通过虚拟机网格 (VMs?WT.mc_id=gridbank-docs-dastarr) 进行批处理。这些虚拟机相互协作，以实现共同目标。

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) 是一项 Azure 服务，可便于多个辅助 VM 并行运行，如下所示。 处理数据文件并将结果提交到机器学习系统或数据存储，这些都是辅助节点的常见任务。 由于辅助节点运行的应用程序代码是由客户创建，因此可以在批处理作业中执行几乎任何操作。

![本地批处理](./assets/risk-grid-compute-assets/01-on-prem.png)

Azure 提供了使用 Azure Batch 进行风险网格计算的精妙解决方案。 使用 Azure Batch，客户可以扩展自己的现有风险计算网格，也可以将本地资源替换为完全基于云的解决方案。

直接安全地连接到 Azure 云是完全受支持的。 如果使用混合网络连接到 Azure，风险处理网格辅助节点可以在连接到本地存储的数据时访问建模数据。 客户还可以将数据上传到 Azure 内的相应存储，同时允许 Batch 直接访问数据。

## <a name="secure-connectivity-to-azure"></a>安全连接到 Azure

在 Azure 上构建风险网格计算解决方案时，企业通常会继续使用现有的本地应用程序，如交易系统、中间部门风险管理、风险分析等。 Azure 成为这些现有投资的扩展。

连接到云时，安全性是首要考虑因素。 将当前的安全模型纳入考虑范围是直接连接到 Azure 的第一步。 对于已在本地使用 Active Directory (AD?WT.mc_id=gridbank-docs-dastarr) 的客户，连接到 Azure 可以利用现有的标识资源。 服务帐户可以驻留在本地 AD 中。

### <a name="hybrid-network-solution"></a>混合网络解决方案

混合网络将 Azure 直接绑定到客户的本地网络。 Azure 提供了两种模型，用于安全可靠地将当前本地系统连接到 Azure、[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) 和 [VPN 网关](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr)。 两者都是受信任的连接解决方案，尽管在实现、性能、成本和其他属性方面存在差异。

![Azure 连接](./assets/risk-grid-compute-assets/02-connectivity.png)

当现有资源激增时（增加了客户的数据中心或私有云资源），“云爆发”会将计算作业卸载到基于云的计算机中。 使用混合网络模型，可以轻松执行云爆发方案，因为基于云的风险计算网格是现有网络的简单扩展。

除了上面逻辑体系结构中提供的简单模型之外，还有几种网络连接配置。 若要获取将网络连接到 Azure 方面的决策制定和体系结构指导帮助，请参阅[将本地网络连接到 Azure  ](/azure/architecture/reference-architectures/hybrid-networking/) 一文。

### <a name="rest-api-solution-over-internet"></a>通过 Internet 的 REST API 解决方案

创建混合网络的另一种方法是，将数据上传到 Azure 存储（文件或 Blob 存储，可能是 candidates?WT.mc_id=gridbank-docs-dastarr），并让 Batch 从存储中读取数据文件。 为此，可使用安全 (SSL?WT.mc_id=gridbank-docs-dastarr) 连接来连接到 Azure，将文档存储在 Azure 存储中，再通过 [Batch 服务 REST API](/rest/api/batchservice/?WT.mc_id=gridbank-docs-dastarr) 或 SDK 以及适用应用程序管理风险网格计算作业，同时协调 Batch 运行。

### <a name="azure-data-factory"></a>Azure 数据工厂

方案适用的另一个解决方案可能是，使用基于云的数据集成服务 [Azure 数据工厂](/azure/data-factory/?WT.mc_id=gridbank-docs-dastarr)构成大型存储、移动和处理管道。 数据可通过数据工厂管道按需上传。 此服务在 Azure 门户中提供了可视化设计器，用于在 Azure 中构建提取、转换和加载 (ETL?WT.mc_id=gridbank-docs-dastarr) 解决方案。 数据工厂有助于将数据引入 Azure，以供进一步处理。

## <a name="matching-processing-needs-with-demand"></a>将处理需求与负载需求相匹配

计算风险时，无论是每天计算一次，还是累积到月末一起计算，计算都会消耗大量计算资源。 这些计算不会全天候运行。 如果风险计算不是在本地网格上运行，组织会让有价值且费用高昂的服务器一直没有工作负载，但电力、冷却和数据中心空间会持续产生成本以及其他固定成本。

### <a name="augmenting-on-premises-grid-with-azure-batch"></a>使用 Azure Batch 扩大本地网格

为了最大程度地降低成本，企业可以选择拥有并管理刚好够用的辅助节点，以在负载需求低时满足需求。 然后，可以将高负载需求风险网格计算作业推送到 Azure 中的高性能服务器，同时根据工作负载需求弹性地纵向扩展和缩减。

Azure Batch 处理模型带来了多项风险网格计算优势：

- 增加对各种本地系统的现有投资。
- 让现有基础设施能够在负载需求低时满足风险分析需求，同时取消分配基于 Azure 的辅助节点。
- 当负载需求高时，提供额外的风险计算网格容量。
- 即使负载需要[高性能计算 (HPC)](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 配置，也能将计算机配置文件与 Batch 工作负载所需的处理能力相匹配。

常见解决方案是，在本地辅助节点全部都获使用时，自动在 Azure 中添加辅助节点。 风险网格头节点仅要求有更多辅助节点。 这会自动缩放 Azure 中的网格辅助节点数，并启用弹性需求解决方案。

![混合云](./assets/risk-grid-compute-assets/03-hybrid-cloud.png)

除了能高效利用资源外，这种安排还带来了其他优势。 对于独立任务，添加更多辅助角色可便于负载线性缩放。 使用 Azure，还可以灵活地试用非常大的 VM 实例，或有多个 GPU 卡的计算机。 借助这种灵活性，可以不断试验和创新。

如果需要更多计算容量（如季度评估），还可以通过 Azure Batch 自动缩放获取额外容量。 自动缩放让 Batch 解决方案有弹性。 通过缩放资源以匹配所需负载，Azure 提供明显更大的容量，但成本却低于硬件拥有成本。

大多数商业网格产品确实支持某种形式的云爆发，可便于你更轻松地利用风险分析负载的概念证明。 例如，[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 可以在 Azure 中运行，也可以在 TIBCO、Univa 等公司的产品中运行。 这些第三方工具或系统大多可以通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?WT.mc_id=gridbank-docs-dastarr)获得。

### <a name="migrating-additional-resources-to-the-cloud"></a>将其他资源迁移到云中

随着工作负载增加或本地数据中心基础设施老化，组织可以将用于风险网格计算的整个 Batch 处理模型迁移到 Azure 中。

### <a name="growing-into-azure"></a>扩展到 Azure 中

当本地计算机生命周期结束时，可以进一步将辅助节点分发到云中。 对于 Batch 头节点，也是如此。 这会反转本地网络与 Azure 之间的关系。 这可能是降低成本的机会，具体是通过停用任何网络间产品（如 Azure ExpressRoute）和剩余任何本地辅助节点。

在此更改期间，可使用各种文件流入技术将数据提供给 Azure。 Azure 有许多可供选择的存储选项，包括允许直接上传数据的 REST 终结点，而不是让计算作业从本地网络中拾取数据。

 ![本地批处理](./assets/risk-grid-compute-assets/04-batch-process.png)

在此模型下，所有风险网格计算活动都可以在云中进行。 辅助角色处理的数据文件可以存储在 Azure 存储中，数据可以直接馈送到 Azure Data Lake 中，并且 Azure HDInsight 能满足机器学习需求。 最后一点，Power BI 和 Azure Analytics 是出色的数据分析工具，可以处理 Azure 中存储的所有数据。

### <a name="data-security-considerations-for-risk-grid-computing"></a>风险网格计算的数据安全注意事项

虽然计算数据通常不包含任何个人身份信息 (PII?WT.mc_id=gridbank-docs-dastarr)，但大多数银行仍可能会先进行安全风险评估，再将任何工作负载置于云中。 此评估可能需要 Microsoft 输入数据，并可能会生成安全建议。

值得注意的风险网格计算注意事项是，[在 Azure VNet 中运行批处理](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)。 这样一来，池计算节点可以与其他计算节点或本地网络安全通信。 应由批处理计算节点创建和使用相应的服务帐户和网络服务组 (NSG)。 对于 Azure 存储内的传输中数据和静态数据，[Azure 还有相应的加密解决方案](/azure/security/blueprints/financial-services-regulated-workloads?WT.mc_id=gridbank-docs-dastarr)。

可能需要注意以下几个方面：加入 Active Directory (AD) 或未加入 AD 的计算节点（对于 Windows Server nodes?WT.mc_id=gridbank-docs-dastarr）、[VM 磁盘加密](/azure/security/azure-security-disk-encryption?WT.mc_id=gridbank-docs-dastarr)、静态和传输中计算输入和输出数据的安全性、Azure 网络配置、权限等。 身份验证还可以通过密钥在 REST API 一级进行处理。

## <a name="getting-started"></a>入门

许多客户都有已使用的内部风险计算网格。 如果你的公司在内部开发了网格，不妨使用 Azure Batch 扩展网格。 若要开始使用 Azure Batch，最好是复制当前的处理应用程序逻辑，并将它作为 Batch 作业在 Azure 中运行，以扩展任何当前本地解决方案。 这可能需要使用网络解决方案，将 Azure Batch 计算节点加入本地网络，具体视应用程序功能而定。

若要降低任何安全性、速度和连接可靠性问题的风险，请考虑使用 Azure ExpressRoute 或 VPN 网关将本地网络连接到 Azure。 随后，便可以让本地头节点预配基于 Azure 的辅助节点群集，同时按需纵向扩展和缩减。

最后，可以将风险计算基础设施完整迁移到 Azure 中。 如果是这种情况，请参阅[这篇文章](/azure/batch/?WT.mc_id=gridbank-docs-dastarr)立即入门。

## <a name="technologies-presented"></a>所提供的技术

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) 可以增加本地风险计算辅助节点，以按需动态提供计算资源。

[Azure Data Lake](/azure/data-lake-store?WT.mc_id=gridbank-docs-dastarr) 可以存储、处理和分析风险分析数据。

[Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) 可以通过连接提供商提供的专用连接，将本地网络扩展到 Azure 中。

[Azure HDInsight](/azure/hdinsight/?WT.mc_id=gridbank-docs-dastarr) 是一项完全托管的开放源代码分析服务，用于处理大量数据，如月末批处理运行中提供的数据。

[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 可以预配高性能计算群集，以供批处理。

[Power BI](/power-bi/?WT.mc_id=gridbank-docs-dastarr) 是风险分析员用于获取和共享见解的一套业务分析工具。

[VPN 网关](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr)通过 Internet 将本地网络扩展到 Azure 云中。

## <a name="conclusion"></a>结束语

本文中介绍的解决方案是银行业务风险网格计算方法。 鉴于 Azure 产品和服务的丰富功能，以及各种现有客户端系统体系结构，可使用其他体系结构。 即使如此，鉴于本文中列出的优势，Batch 提供的风险网格计算模型很合理。

通过[将本地网络扩展到 Azure 中](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbank-docs-dastarr)，Azure 可以轻松访问本地网络中已有的网络资源和其他处理系统。 当本地计算机生命周期即将结束时，可能更有意义的做法是，在 Azure 中完整使用 Batch 计算，而不是支持混合模型。

不需要混合网络即可利用 Batch 的另一种方法是，在 Batch 作业开始前，将文件上传到 Azure 存储中。 这可以递增方式完成，也可以作为 Batch 运行的启动流程完成。

在你选择连接策略后，从风险计算开始的逻辑位置先将现有作业置于 Azure 计算辅助节点，再在测试环境中运行它们，以确定是否需要更改任何代码。 [可以从这篇文章入手](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)，通过所选择的语言或工具开始使用 Azure Batch。

[银行业务风险网格计算解决方案指南](/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=banking-docs-dastarr)
