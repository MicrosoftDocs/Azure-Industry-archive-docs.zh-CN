---
title: 将电子商务解决方案迁移到 Azure 的概述
author: scseely
ms.author: scseely, mazoroto
ms.date: 07/16/2018
ms.topic: article
ms.service: industry
description: 本文介绍了将电子商务基础结构从本地迁移到 Azure 的阶段。
ms.openlocfilehash: 847950673475f7f59a0c8c8c8bfc821414b2cc77
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654264"
---
# <a name="migrating-your-e-commerce-solution-to-azure-overview"></a>将电子商务解决方案迁移到 Azure 的概述

## <a name="introduction"></a>介绍

将现有电子商务解决方案移动到云为企业带来了许多好处：支持可伸缩性，可供客户全天候访问，以及更易于集成云服务。 但首先，将电子商务解决方案移动到云是一项重要任务，决策者必须了解所需花费的成本。 本文档介绍 Azure 迁移的范围，目的在于告知你相关选项。 在第一阶段中，IT 专业人员先将组件移动到云。 一旦移动到 Azure，我们即可介绍电子商务团队可用来增加 ROI 并充分利用云的步骤。

**处在抉择关头**

虽然全球电子商务交易只占零售总额的一小部分，但渠道仍认为它会逐年稳步增长。 在 2017 年，电子商务占零售总额的 10.2%，与 2016 年（[源](https://www.emarketer.com/Report/Worldwide-Retail-Ecommerce-Sales-eMarketers-Updated-Forecast-New-Mcommerce-Estimates-20162021/2002182)）的 8.6% 相比有所提高。 随着电子商务趋于成熟，以及云计算的问世，零售商正处在抉择关头。  需要做出选择。 他们可以借助可能随着技术不断发展而生成的新功能构建其业务模型，也可以根据其当前功能布局计划现代化。

**改进客户旅程**

电子商务主要侧重于客户旅程，有许多不同的属性。 这些属性可以分为以下四个主要方面：发现、评估、采购和售后。

客户行为作为数据捕获。 购物漏斗图是指向用于查看产品数据、交易、清单、发货、订单履行、客户配置文件、购物车和产品建议等应用程序的连接的集合。

典型的零售业依赖于软件解决方案的大型集合，范围包括从整个堆栈中面向客户的应用程序到基础应用程序。  下图显示了典型零售业中提供的功能的视图。

 ![](./assets/migrating-ecommerce-solution-to-azure/ecommerce-system-sketch.png)

云提供了改变组织获取、使用和管理技术的方式的机会。  其他好处包括：降低了维护数据中心的成本，提升了可靠性和性能，以及灵活地添加其他服务。 在此用例中，我们会看到零售业可用来将其现有基础结构迁移到 Azure 的方法。 我们也会使用重新托管、重构和重新生成的分阶段方法来充分利用新环境。 尽管许多组织可能会使用本系列方法实现现代化，但在大多数情况下，组织可以放入任何阶段作为其起点。  组织可能会选择放弃在 Azure 上重新托管当前应用程序，而是直接重构甚至重新生成。  此决策将特定于应用程序和组织，以便最符合其现代化需求。

## <a name="rehost"></a>重新托管

也称为&quot;直接迁移&quot;，此阶段需要将物理服务器和 VM 按原样迁移到云。 通过将当前服务器环境直接迁移到 IaaS，可享有成本节省、安全性和更高可靠性的好处。 通过在大小合适的 VM 上运行工作负载等方法可实现节省。 现在，本地 VM 和物理计算机的功能经常超过零售商的日常需求。 VM 必须能够处理一年仅出现几次的季节性业务高峰。 因此，你需要在淡季对未使用的功能付费。 借助 Azure，你可以根据当前业务周期的需求选取大小合适的 VM。

若要在 Azure 中重新托管，有以下三个阶段：

- 分析：标识和清点应用程序、工作负载、网络和安全性等本地资源。 在此阶段结束时，你已完成现有系统的记录。
- 迁移：将每个子系统从本地移动到 Azure。 在此阶段中，你会将 Azure 用作数据中心的扩展，并继续与应用程序进行通信。
- 优化：当系统移动到 Azure 中时，请确保大小合适。 如果环境显示向某些虚拟机分配了过多的资源，请将 VM 类型更改为具有更合适的 CPU、内存和本地存储组合的类型。

### <a name="analyze"></a>分析

请执行以下操作：

1. 列出本地服务器和应用程序。 此操作依赖代理或管理工具收集有关服务器、服务器上运行的应用程序、当前服务器使用情况以及如何配置服务器及其应用程序的元数据。 此结果是环境中所有服务器和应用程序的报告。
1. 标识依赖项。 可以使用工具标识哪些服务器相互通信，以及相互通信的应用程序。 结果是所有应用程序和工作负载的一个或多个映射。 这些映射将注入迁移规划。
1. 分析配置。 目标是了解在 Azure 中运行时需要哪些 VM 类型。 结果是有关可移动到 Azure 的所有应用程序的报告。 可以将其进一步分类为：
      1. 无修改
      1. 基本修改，如命名更改
      1. 少许修改，如轻微的代码更改
      1. 不兼容的工作负载，需要付出额外努力才能移动
1. 创建预算。 现在有一个列表，其中枚举了每个 CPU（内存等）以及每个应用程序的要求。 将这些工作负载放置在大小合适的 VM 上。 云平台根据使用情况对成本进行计费。 工具存在的原因是将你的需求映射到大小合适的 Azure VM。 如果要迁移 Windows VM 或 SQL Server，还应查看可减少 Azure 相关费用的 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/?WT.mc_id=retailecomm-docs-scseely)。

Microsoft 提供了多个用来分析和编录系统的工具。 如果运行 VMWare，则可以使用 [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) 协助进行发现和评估。 该工具标识可移动到 Azure 的计算机，建议要运行的 VM 的类型，并估计工作负载的成本。 对于 Hyper-V 环境，使用 [Azure Site Recovery 部署规划器](/azure/site-recovery/hyper-v-deployment-planner-overview?WT.mc_id=retailecomm-docs-scseely)。 对于需要移动数百个或更多虚拟机的大型迁移，你可能希望与 [Azure 迁移合作伙伴](https://azure.microsoft.com/migration/partners/?WT.mc_id=retailecomm-docs-scseely)一起合作。 这些合作伙伴在移动工作负载方面拥有专业知识和经验。

### <a name="migrate"></a>迁移

开始计划将哪些服务移动到云以及采用哪种顺序。 由于此阶段涉及移动工作负载，因此请按照以下顺序：

1. 构建网络。
2. 合并标识系统 (Azure Active Directory)。
3. 在 Azure 中预配存储块。

在迁移期间，Azure 环境是本地网络的扩展。 你可以将逻辑网络与 [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely)连接。 你可以选择使用 [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) 在从未触及 Internet 的专用连接上保持网络与 Azure 之间的通信。 你也可以使用站点到站点 VPN，Azure VPN 网关在其中通过使用 Azure 与你的网络之间的加密通信安全发送的所有流量与本地 VPN 设备进行通信。 我们在[此处](/azure/architecture/reference-architectures/hybrid-networking/vpn?WT.mc_id=retailecomm-docs-scseely)已发布详细说明如何设置混合网络的参考体系结构。

配置网络后，规划业务连续性。 建议使用实时复制将本地数据移动到云并确保云和现有数据相同。 电子商务商店永远不会关闭；通过复制可以从本地切换到 Azure，但会轻微影响你的客户。

开始将数据、应用程序和相关服务器移动到 Azure 中。 许多公司使用 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) 服务迁移到 Azure。 该服务以业务连续性和灾难恢复 (BCDR) 为目标。 这是从本地迁移到 Azure 的理想之选。 你的实施团队可以在[此处](/azure/site-recovery/migrate-tutorial-on-premises-azure?WT.mc_id=retailecomm-docs-scseely)阅读如何将本地 VM 和物理服务器迁移到 Azure 的详细信息。

将子系统移动到 Azure 后，通过测试来确保一切按预期工作。 关闭所有问题后，将工作负载移动到 Azure。

### <a name="optimize"></a>优化

此时，你将继续监视环境并更改基础计算选项，以适应随环境不断变化的工作负载。 无论谁监视环境的运行状况，都应监视每个资源的使用情况。 目标应为大多数 VM 的利用率达 75-90%。 在利用率极低的 VM 上，请考虑使用更多应用程序对其进行打包，或迁移到 Azure 上成本最低且保持正确性能级别的 VM。

Azure 也提供了用来优化环境的工具。 [Azure 顾问](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely)可以监视你的环境的组件，并基于最佳做法提供个性化建议。 这些建议有助于改善应用程序中使用的资源的性能、安全性和可用性。 Azure 门户还公开了有关应用程序运行状况的信息。 你的 VM 应充分利用[适用于 Linux 和 Windows 的 Azure 虚拟机扩展](/azure/virtual-machines/extensions/overview?WT.mc_id=retailecomm-docs-scseely)。 这些扩展提供了后期部署配置、防病毒、应用监视等。 你还可以充分利用许多其他 Azure 服务进行网络诊断、服务使用以及通过[网络观察程序](/azure/network-watcher/network-watcher-monitoring-overview?WT.mc_id=retailecomm-docs-scseely)、[服务映射](/azure/monitoring/monitoring-walkthrough-servicemap?WT.mc_id=retailecomm-docs-scseely)、[Application Insights](/azure/application-insights/app-insights-overview?WT.mc_id=retailecomm-docs-scseely) 和 [Log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=retailecomm-docs-scseely) 等服务发出警报。

虽然组织中的部分人员现在正在 Azure 中优化系统，但开发团队可以开始转到迁移后阶段：重构。

## <a name="refactor"></a>重构

迁移完成后，你的电子商务应用程序可以开始充分利用它在 Azure 中的新本组织。 重构阶段不必等到移动整个环境。 如果你的 CMS 团队已迁移，但 ERP 团队没有，则不存在任何问题。 CMS 团队仍可以开始重构工作。 此阶段涉及到使用其他 Azure 服务通过重构你的应用程序来优化成本、可靠性和性能。 而在“直接迁移”中，你仅利用提供程序托管的硬件和操作系统，在此模型中还可以利用云服务来降低成本。 继续按原样使用当前应用程序（仅做一些轻微的应用程序代码或配置更改），并将应用程序连接到新的基础结构服务（如容器、数据库和标识管理系统）。

重构工作几乎不会更改代码和配置。 在自动化上花费更多时间的主要原因是此阶段采用的技术依赖脚本构建和部署资源；部署说明是一个脚本。

虽然可以使用多个 Azure 服务，但我们将侧重于重构阶段中使用的最常见服务：容器、应用服务和数据库服务。 为何要介绍重构？ 重构提供了强大的代码基础，可通过使代码债务保持在合理范围内来降低长期成本。

容器提供了一种绑定应用程序的方法。 由于容器虚拟化操作系统所采用的方式，因此可以将多个容器打包到一个 VM。 可以将应用程序移动到容器（无代码更改或少量代码更改）；可能需要进行配置更改。 这项工作也会导致编写可将应用程序捆绑到容器中的脚本。 开发团队将花费其重构时间来编写和测试这些脚本。 Azure 支持通过 [Azure Kubernetes 服务](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) (AKS) 以及可用于管理容器映像的相关 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/?WT.mc_id=retailecomm-docs-scseely)进行容器化。

对于应用服务，可以充分利用各种 Azure 服务。 例如，现有基础结构可能会通过将消息放入队列（如 [RabbitMQ](https://www.rabbitmq.com/)）来处理客户订单。 （例如，一条消息是对客户进行收费，第二条消息是配送订单。）重新托管时，将 RabbitMQ 放在单独的 VM 中。 在重构期间，将[服务总线](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions?WT.mc_id=retailecomm-docs-scseely)队列或主题添加到解决方案中，重写 RabbitMQ 代码，并停止使用提供队列功能的 VM。 此更改会将一组 VM 替换为始终可用的消息队列服务以降低成本。 可在 Azure 门户中找到其他应用服务。

对于数据库，可以将你的数据库从 VM 移动到服务。 Azure 支持使用 [Azure SQL 数据库](/azure/sql-database/sql-database-cloud-migrate?WT.mc_id=retailecomm-docs-scseely)和 [Azure SQL 数据库托管实例](/azure/sql-database/sql-database-managed-instance?WT.mc_id=retailecomm-docs-scseely)的 SQL Server 工作负载。 [数据迁移服务](https://azure.microsoft.com/services/database-migration/?WT.mc_id=retailecomm-docs-scseely)会评估你的数据库，告知你需要在迁移之前执行的工作，并将数据库从 VM 移动到服务。 Azure 支持 [MySQL](https://azure.microsoft.com/services/mysql/?WT.mc_id=retailecomm-docs-scseely)、[PostgreSQL](https://azure.microsoft.com/services/postgresql/?WT.mc_id=retailecomm-docs-scseely) 以及[其他数据库](https://azure.microsoft.com/services/#databases?WT.mc_id=retailecomm-docs-scseely)引擎服务。

## <a name="rebuild"></a>重新生成

到目前为止，我们已尝试最大限度地减少对电子商务系统的更改 — 我们仅保留工作系统。 接下来讨论如何真正充分利用云。 此阶段是指通过积极采用 PaaS 甚至 SaaS 服务和体系结构来修改现有应用程序。 该过程包括添加新功能或为云重构应用程序的重大修改。  托管 API 是充分利用云系统的新概念。 我们可以通过创建用于在服务之间通信的 API 来使系统更易于更新。  第二个好处是能够深入了解我们拥有的数据。 为此，需移动到微服务和 API 体系结构并使用机器学习和其他工具来分析数据。

### <a name="microservices--apis"></a>微服务 + API

微服务通过面向外部的 API 进行通信。 每个服务都是自包含的且应实现单个业务功能，例如：向客户推荐项目、维护购物车等。 将应用程序分解为微服务需要时间和规划。 虽然没有硬性规则来定义微服务，但总体理念为将可部署单元减少到一组几乎总是一起更改的组件。 通过微服务可以根据需要经常部署更改，同时减轻整个应用程序的测试负担。 某些服务可能极小。 因此，在不使用任何非使用中的资源时，使用 [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) 变为无服务器有助于根据需要扩展为多个调用方。 其他服务将按业务功能划分：管理产品、捕获客户订单等。

无服务器机制确实存在缺点：在负载较轻时，它们的响应速度可能很慢，因为云中的某些服务器需要几秒钟时间才能配置和运行你的代码。 对于客户频繁使用的环境的一部分，你希望确保它们可以快速轻松地查找产品、下订单、请求退款等。 每当性能速度变慢时，就会有丢失购物漏斗图中的客户的风险。 如果你有必须快速响应的功能，则在 [Azure Kubernetes 服务](/azure/aks/?WT.mc_id=retailecomm-docs-scseely)中将该功能重新生成为单独可部署的单元。  对于其他情况（例如，需要大量内存、多个 CPU 和大量本地存储的某些组合的服务），在其自己的 VM 中托管微服务可能有利。

每个服务都使用 API 进行交互。 对 API 的访问权限可以直接访问微服务，但这需要与该服务通信的任何人了解应用程序拓扑。 [API 管理](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely)等服务为你提供了一种发布 API 的重要方法。 所有应用程序只需连接到 API 管理服务。 开发人员可以发现哪些 API 是可用的。 API 管理服务还能够使零售环境表现良好。 该服务可以按应用程序的不同部分限制对 API 的访问（以防止遇到瓶颈），缓存慢更改值的响应，从 JSON 转换为 XML 等。 可以在[此处](/azure/api-management/api-management-policies?WT.mc_id=retailecomm-docs-scseely)找到策略的完整列表。

### <a name="make-use-of-your-data-and-the-azure-marketplace"></a>使用你的数据和 Azure 市场

由于 Azure 中存在所有数据和系统，因此可以轻松地将其他 SaaS 解决方案合并到你的业务中。 可以立即执行某些操作。 例如，使用 [Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) 将各种数据源整合到一起以创建可视化效果和报告，并获取见解。

接下来看看 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/?WT.mc_id=retailecomm-docs-scseely)中的产品/服务，这些产品/服务可以帮助你执行诸如优化清单、基于客户属性管理市场活动以及基于首选项和历史记录向每个客户提供正确的项目等操作。 希望花些时间来配置你的数据以便在 Marketplace 产品/服务中工作。

## <a name="next-steps"></a>后续步骤

许多开发团队试图同时执行重新托管和重构操作来解决技术债务并更好地利用容量。 在跳转到后续步骤之前进行重新托管有好处。  部署到新环境的任何问题都会更易于诊断和修复。 这反过来会为开发和支持团队提供时间来熟悉作为新环境的 Azure。 开始重构和重新生成系统时，你是在稳定且正常运行的应用程序上进行构建。 这样可以进行更小且有针对性的更改并进行更频繁的更新。

我们已发布有关迁移到云的更常规白皮书：[云迁移概要](https://azure.microsoft.com/resources/cloud-migration-essentials-e-book/?_lrsc=9618a836-9f81-4087-901f-51058783c3a8&WT.mc_id=retailecomm-docs-scseely)。 这在规划迁移时非常适合通读。

## <a name="technologies-presented"></a>所提供的技术

重新托管期间使用：

- [Azure 顾问](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely)是个性化的云顾问程序，可帮助遵循最佳做法来优化 Azure 部署。
- [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) 服务会评估要迁移到 Azure 的本地工作负载。
- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) 可安排和管理 Azure VM 以及本地 VM 和物理服务器的灾难恢复操作。
- [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely)允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。
- 使用 [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。

重构期间使用：

- [Azure Kubernetes 服务](/azure/aks/?WT.mc_id=retailecomm-docs-scseely)管理托管的 Kubernetes 环境，使用户无需具备容器业务流程专业知识即可快速、轻松地部署和管理容器化的应用程序。
- [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview?WT.mc_id=retailecomm-docs-scseely)是 Microsoft Azure 中的通用关系数据库管理服务。 它支持关系数据、JSON、空间和 XML 等结构。 SQL 数据库提供托管的单一 SQL 数据库、弹性池中的托管 SQL 数据库以及 SQL 托管实例。

重新生成期间使用：

- Azure [API 管理](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely)可帮助组织将 API 发布给外部、合作伙伴和内部开发人员，以充分发挥其数据和服务的潜力。
- [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) 是用于在云中轻松运行小段代码或&quot;函数&quot;的一个解决方案。
- [Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) 是一套业务分析工具，用于在整个组织内提供见解。

## <a name="conclusion"></a>结束语

将你的电子商务系统移动到 Azure 中将采取分析、规划和定义的方法。 我们探讨了重新托管、重构和重新生成的三阶段方法。 这将使组织能够从一个工作状态转为另一个工作状态，同时在每个步骤中最大限度地减少更改量。 零售商还可以选择重构甚至重新生成组件，正在跳过一起重新托管。 很多时候，你将具有实现现代化的明确路径 — 在你可以时采用它。 获取在 Azure 中运行的经验时，你将看到更多的机会来添加新功能、降低成本以及改善整个系统。


_本文作者是 Scott Seely 和 Mariya Zorotovich。_