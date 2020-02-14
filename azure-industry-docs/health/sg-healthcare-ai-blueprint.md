---
title: 使用 Azure 服务实现医疗保健 AI 蓝图
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 本文提供了 Microsoft Azure AI 蓝图的相关指南。
ms.openlocfilehash: 40919ffde2c2cac11339b40348cba7a5e0e0e16d
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052631"
---
# <a name="implementing-the-azure-blueprint-for-ai"></a>实现 Azure AI 蓝图

## <a name="introduction"></a>介绍

医疗保健组织逐渐意识到 AI（人工智能）和 ML（机器学习）可成为改进其许多业务环节的有用工具，从提高患者疗效到简化常规操作均有用。 通常医疗保健组织没有实现 AI/ML 系统的技术人员。 为改善此情况并使 AI/ML 解决方案快速在 Azure 上运行，Microsoft 创建了 [Azure 医疗保健 AI 蓝图](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)。 通过此蓝图，我们演示了如何以安全可靠且合规的方式快速启动 AI/ML。

健康 AI 蓝图使用 Azure 将 AI/ML 引入组织。 本文介绍了此蓝图的安装过程、组件及如何使用它运行 AI/ML 实验来预测患者住院时长。

### <a name="benefits"></a>优点

创建此蓝图的目的在于为医疗保健组织提供指南和适当 PaaS（平台即服务）体系结构的快速启动方法，以便在高度管控的医疗保健环境中支持 AI/ML，其中包括确保系统符合 HIPAA 和 HITRUST 合规性要求。

医疗保健组织中的技术人员通常只有少量时间用于新项目，尤其是要求他们必须学习复杂新技术的项目。 此蓝图可帮助技术人员快速熟悉 Azure 以及它的多个服务，从而节省学习曲线的成本。 安装此蓝图后，技术人员可以将其作为参考实现进行学习，然后使用该知识来扩展其功能，或仿照此蓝图创建新的 AI/ML 解决方案。

借助此蓝图，组织可以快速启动并运行新的 AI/ML 功能。 AI 和 ML 就绪后，技术人员即可使用通过各种源收集的数据运行 AI/ML 实验。 例如，可能已存在脓毒症旧案例以及许多随附变量（对有此病症的患者个体跟踪了这些变量）的相关数据。 借助此数据（使用匿名格式），技术人员可以在患者身上查找潜在脓毒症的迹象，并帮助更改工作流程，从而更有效地避免此病症。

此蓝图提供了用于学习如何预测患者住院时长的数据和示例代码。 此为示例用例，可使用它来了解 AI/ML 解决方案的组件。

### <a name="platform-or-infrastructure-as-a-service"></a>平台即服务或基础结构即服务

Microsoft Azure 提供了 PaaS 和 SaaS 两种服务，请根据不同的用例选择符合需求的适当服务。 此蓝图使用 PaaS 服务来解决预测患者住院时长的问题。 Azure 医疗保健 AI 蓝图会为医疗保健组织预配置安全合规的 AI/ML 解决方案，并提供实例化该方案所需的一切内容。 此蓝图使用的 PaaS 模型会将此蓝图安装并配置为完整的解决方案。

### <a name="paas-option"></a>PaaS 选项

使用 PaaS 服务模型可以降低总拥有成本 (TCO)，因为不存在要管理的硬件。 组织无需购买和维护硬件或 VM。 此蓝图以独占方式使用 PaaS 服务。

这降低了维护本地解决方案的成本，并使技术人员可以重点关注战略计划而非基础结构。 它还可以将计算和存储付费从资本投入预算转移到运营投入预算。 运行此蓝图方案的成本由这些服务的使用情况及数据存储的成本驱动。

### <a name="iaas-option"></a>IaaS 选项

此蓝图和本文均侧重于 PaaS 实现，但也存在此蓝图的[开放源代码扩展](https://github.com/Azure/Azure-Health-Extension)，借助此扩展可以在基础结构即服务 (IaaS) 环境中使用此蓝图。

在 IaaS 托管模型中，客户为 Azure 托管的 VM 的运行时间及其处理能力付费。 IaaS 提供了更高级别的控制能力，因为由客户管理自己的 VM，但通常成本也会增加，因为 VM 根据运行时间收费，而非使用情况。 此外，客户负责通过应用补丁、防范恶意软件等方式来维护 VM。

IaaS 模型不属于本文范围，本文重点介绍此蓝图的 PaaS 部署。

## <a name="the-healthcare-aiml-blueprint"></a>医疗保健 AI/ML 蓝图

此蓝图创造了将此技术用于医疗保健上下文的起点。 此蓝图安装到 Azure 后，将使用适当的执行组件、权限和服务创建用于支持 AI/ML 方案的所有资源、服务和多个用户帐户。

此蓝图包含用于预测患者住院时长的 AI/ML 实验，此实验有助于预测人员配备、床位数及其他组织工作。 此包包括安装脚本、示例代码、测试数据、安全和隐私支持等。

## <a name="blueprint-technical-resources"></a>蓝图技术资源

下列资源均可在此 GitHub 存储库中找到。

主要资源有：

1. 部署、配置和其他任务的 [PowerShell](https://docs.microsoft.com/powershell/scripting/powershell-scripting?WT.mc_id=ms-docs-dastarr) 脚本。
2. [安装详细说明](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)，其中包括如何使用安装脚本。
3. [常见问题解答综合](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md)。

此模型的横切关注点包括标识和安全性，处理患者数据时这两者均格外重要。 ML 管道的组件如下图中所示。

![ML 管道](assets/sg-healthcare-ai-blueprint-assets/ml-pipeline.png)

下图介绍了安装的 Azure 产品。 每个资源或服务提供一个 AI/ML 处理解决方案组件，其中包括标识和安全性横切关注点。

![组件区域](assets/sg-healthcare-ai-blueprint-assets/component-zones.png)

在受管控的医疗保健环境中实现新系统，此过程较为复杂。 例如，确保系统的各个方面都符合 HIPAA 并可受 HITRUST 认证，这不仅仅需要开发一个轻型解决方案。 此蓝图会安装标识和资源权限，以降低这些难度。

此蓝图还提供其他脚本和数据，用于模拟和研究入院患者或出院患者的结果。 借助这些脚本，员工可以快速了解如何使用此解决方案在安全独立的应用场景中实现 AI 和 ML。

### <a name="additional-blueprint-resources"></a>其他蓝图资源

此蓝图将为技术人员提供优质的指南和说明，还会提供项目来帮助创建功能完备的安装。 另外的这类项目包括：

1. [威胁模型](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)（与 [Microsoft 威胁建模工具](https://www.microsoft.com/download/details.aspx?id=49168&WT.mc_id=ms-docs-dastarr)结合使用）。 此威胁模型说明了解决方案的组件、组件之间的数据流以及信任边界。 若要扩展基本蓝图，可使用此工具进行威胁建模，或者可使用此工具来从安全性角度了解系统体系结构。

2. [HITRUST 客户责任矩阵](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)（使用 Excel 工作簿格式）。  它说明了客户和 Microsoft 要针对矩阵中的每个要求提供的内容。 有关此责任矩阵的详细信息，请参阅本文档中的“安全性和合规性”>“蓝图责任矩阵”部分。

3. [HITRUST 健康数据和 AI 审查](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)白皮书从进行 HITRUST 认证要满足的要求这一视角审视了此蓝图。

4. [HIPAA 健康数据和 AI 审查](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)白皮书以 HIPAA 规定为准则审查了此体系结构。

若要获取这些资源，请访问 [GitHub](https://github.com/Azure/Health-Data-and-AI-Blueprint)。

## <a name="installing-the-blueprint"></a>安装蓝图

只需投入少量时间即可启动并运行此蓝图解决方案。 建议掌握一些 PowerShell 脚本编写知识，也可用分步说明来指导安装过程，以便无论技术人员的脚本编写技能如何都可成功部署此蓝图。

经验不足的技术人员有望在 30 分钟到 1 个小时之内使用 Azure 安装好此蓝图。

### <a name="the-installation-script"></a>安装脚本

此蓝图提供了优质的指南和安装说明。 此外它还提供了用于安装和卸载蓝图服务和资源的脚本。 调用 PowerShell 部署脚本较为简单。 安装此蓝图前，必须收集特定数据，并将其用作 deploy.ps1 脚本的参数，如下所示。

```powershell
.\deploy.ps1 -deploymentPrefix <prefix> `
            -tenantId <tenant id> ` # also known as the AAD directory
            -tenantDomain <tenant domain> `
            -subscriptionId <subscription id> `
            -globalAdminUsername <user id> ` # ID from your AAD account
            -deploymentPassword <universal password> ` # applied to all new users and service accounts
            -appInsightsPlan 1 # we want app insights set up
```

### <a name="the-installation-environment"></a>安装环境

**重要说明：** 请勿从 Azure 外部的计算机中安装此蓝图。 若在 Azure 中创建一个干净的 Windows 10（或其他 Windows VM）并从此处运行安装脚本，成功安装的可能性会更高。 此方法使用基于云的 VM 来减少延迟，并促进安装顺利生成。

安装期间，脚本会调用要加载和使用的其他包。 从 Azure 中的 VM 进行安装时，安装计算机和目标资源之间的延迟会更少。 但某些下载的脚本包仍易延迟，因为脚本包位于 Azure 环境之外，这可能会导致超时故障。

### <a name="install-failure-dont-panic"></a>安装失败！ （不必惊慌）

安装时，安装程序会下载一些外部包。 有时，脚本资源请求会因安装计算机和包之间的延迟而超时。 发生此情况时，有以下 2 个选项：

1. 不进行任何更改，重新运行安装脚本。 安装程序查看已分配的资源，并仅安装所需的资源。 此技术可以起作用，但存在安装脚本会试图分配已就绪资源的风险。 这可导致错误，安装会失败。

2. 仍运行 deploy.ps1 脚本，但传递其他参数来卸载蓝图服务。

```powershell
.\deploy.ps1 -clearDeploymentPrefix <prefix> `
             -tenantId <value> `
             -subscriptionId <value> `
             -tenantDomain <value> `
             -globalAdminUsername <value> `
             -clearDeployment
```

完成卸载后，在安装脚本中更改前缀，并尝试重新安装。 延迟问题可不再发生。 若在下载脚本包时安装失败，则运行卸载程序脚本，然后重新运行安装程序。

运行卸载脚本后，会删除以下内容。

- 安装程序脚本安装的用户
- 会删除资源组及其各自的服务，其中包括数据存储
- 使用 AAD (Azure Active Directory) 注册的应用程序

请注意，Key Vault 会以“软删除”的方式保留，尽管门户中未显示它，但在 30 天内不会将其解除分配。 这样如有必要则可将其还原。 若要进一步了解此内容的含义及如何处理它，请参阅本文的“技术问题”>“Key Vault”部分。

### <a name="reinstall-after-an-uninstall"></a>卸载后重新安装

如需在卸载蓝图后再重新安装它，必须在下一个部署中更改前缀，因为如若不更改前缀已卸载的 Key Vault 会引发错误。 有关此内容的详细信息，请参阅本文的“技术问题”>“Key Vault”部分。

### <a name="required-administrator-roles"></a>所需的管理员角色

安装此蓝图的人员必须属于 AAD 的全局管理员角色。 此外，安装帐户必须是所使用的订阅的 Azure 订阅管理员。 若执行安装的人员不属于这两个角色的其中之一，安装将失败。

![蓝图安装程序](assets/sg-healthcare-ai-blueprint-assets/blueprint-installer.png)

此外，由于与 AAD 的集成紧密，因此安装不适用于使用 MSDN 订阅进行。 必须使用标准 Azure 帐户。 如有必要，则[获取免费试用版](https://azure.microsoft.com/free/?WT.mc_id=ms-docs-dastarr)，以使用它提供的额度安装蓝图解决方案并运行其演示。

## <a name="adding-other-resources"></a>添加其他资源

Azure 蓝图安装仅包含实现 AI/ML 用例所需的服务。 但可将其他资源或服务添加到 Azure 环境，使它成为其他计划的良好测试平台或生产系统的起点。 例如，可以添加同一个订阅和 AAD 中的其他 PaaS 服务或 IaaS 资源。

需要其他 Azure 功能时，可以将新资源（如 [Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=ms-docs-dastarr)）或新的 [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=ms-docs-dastarr) 添加到解决方案。 添加新资源或服务时，务必将它们配置为符合安全和隐私策略，以便符合法规和政策的要求。

可使用 [Azure REST API](https://docs.microsoft.com/rest/api/?view=Azure&WT.mc_id=ms-docs-dastarr)、[Azure PowerShell 脚本](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-6.6.0&WT.mc_id=ms-docs-dastarr)或 [Azure 门户](https://portal.azure.com/?WT.mc_id=ms-docs-dastarr)创建新资源和服务。

## <a name="using-machine-learning-with-the-blueprint"></a>将机器学习和蓝图结合使用

此蓝图用于通过模型中使用的回归算法演示 ML 方案来预测[患者的住院时长](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#example-use-case)。 这是医疗保健提供商要运行的常见预测，因为它有助于安排人员配备及其他工作决策。 此外，当特定病况的平均住院时长上升或下降时，可逐渐检测到异常。

### <a name="ingesting-training-data"></a>引入定型数据

安装蓝图且所有服务正常工作后，可引入要分析的数据。 [可引入 10 万份患者记录](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#ingest)并且可供模型使用。 引入患者记录是使用 [Azure 机器学习工作室](/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=ms-docs-dastarr)运行患者住院时长实验的第一步，如下所示。

![引入](assets/sg-healthcare-ai-blueprint-assets/ingest.png)

此蓝图包括一个实验以及在机器学习工作室 (MLS) 中运行 ML 作业所需的数据。 示例在实验中使用定型模型来基于大量变量预测患者住院时长。

在此演示环境中，引入 Azure SQL 数据库的数据没有任何缺陷或任何缺失的数据元素。 此为干净的数据。 通常引入不干净的数据后，必须先进行“清理”才能将其提供给机器学习定型算法或将其用于 ML 作业。 数据中存在缺失数据或错误值会对 ML 分析结果产生不利影响。

## <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室

许多医疗保健组织没有专注于 ML 项目的技术人员。 这通常意味着有用的数据被束之高阁，或高价引进顾问来创建 ML 解决方案。

AI/ML 专家及了解 AI/ML 的人员可以使用 Azure 机器学习工作室设计实验。 MLS 是基于 Web 的设计环境，用于创建 ML 实验。 借助 MLS，可以创建模型，并对模型进行定型、评估和评分，从而可以在使用不同工具开发模型时节省宝贵的时间。

MLS 会为 ML 工作负载提供完整的工具集。 这意味着借助此工具 ML 新手可以快速启动，并且生成结果的速度也会快于其他 ML 工具。 这样 IT 人员即可集中精力在其他方面发挥价值，并且无需引进 ML 专家。 在自己的健康保健组织中，此功能意味着可以检验各种假设，并且为可操作见解分析的生成数据，例如患者干预法会提供[预编写模块](/azure/machine-learning/studio-module-reference/index?WT.mc_id=ms-docs-dastarr#help-for-machine-learning-modules)，这些模块可用于拖放式画布，从而以可见的方式将端到端数据科学工作流撰写为实验。

有封装特定算法的预编写模块，例如决策树、决策林、聚类分析、时序、异常情况检测等。

可将自定义模块添加到任意实验。 这些使用 [R 语言](/azure/machine-learning/studio/extend-your-experiment-with-r?WT.mc_id=ms-docs-dastarr)或 [Python](/azure/machine-learning/studio/execute-python-scripts?WT.mc_id=ms-docs-dastarr) 编写。 这样即可使用预生成模块及自定义逻辑创建更复杂的实验。

借助 MLS，可以[创建和使用](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model?WT.mc_id=ms-docs-dastarr)学习模型，也可提供一组预设计实验供通用应用程序使用。 此外，无需更改蓝图的任何资源，即可将新实验添加到 MLS。

可访问 [Azure AI 库](https://gallery.azure.ai/industries/healthcare?WT.mc_id=ms-docs-dastarr)查找特定行业随时可用的 ML 解决方案（其中包括医疗保健行业），以节省时间。 例如，此库包含用于乳腺癌检测和心脏病预测的解决方案和实验。

## <a name="security-and-compliance"></a>安全和符合性

在医疗保健环境中创建、安装或管理软件系统时，安全和合规是最需注意的两个要点。 如果不符合安全策略和认证的要求，对软件系统采用的投资效力可能会下降。

本文和医疗保健蓝图侧重于技术安全，但其他类型的安全也很重要，其中包括物理安全和管理安全。 这些安全主题不属于本文范围，本文重点介绍此蓝图的技术安全。

### <a name="principle-of-least-privilege"></a>最低权限原则

此蓝图会安装具有相关角色的命名用户，用于支持和限制他们对解决方案中资源的访问需求。 此模型称为“最低权限原则”，它是系统设计中的一种资源访问方法。 此原则主张服务和用户帐户应只有实现合理用途所需的系统和服务的访问权限。

此安全模型可确保系统符合 HIPAA 和 HITRUST 的要求，并消除对组织的风险。

### <a name="defense-in-depth"></a>深层防御

使用多个安全控件抽象层的系统设计在使用深层防御。 深层防御在多个级别提供安全冗余。 这意味着不仅仅依赖单个防御层。 它可确保用户和服务帐户拥有适当的资源、服务和数据访问权限。 Azure 在系统体系结构的各个级别提供安全和监视资源，来为整个技术环境提供深层防御。

在软件系统中（如此蓝图所安装的系统），用户可以进行登录，但没有对特定资源的权限。 此深层防御示例由 RBAC（基于角色的访问控制）和 AAD 提供，它支持最低权限原则。

此外，双重身份验证也是一种深层技术防御形式，安装蓝图时也可以选择将它包括在内。

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault 服务是一种容器和保管库，用于存储应用程序使用的机密、证书及其他数据。 其中包括数据库字符串、REST 终结点 URL、API 密钥及开发人员不想硬编码到应用程序或不想分发到 .config 文件的其他内容。

此外，保管库可供具有 AAD 权限的应用程序服务标识或其他帐户访问。 这样需要保管库内容的应用程序即可在运行时访问机密。

可以将保管库中存储的密钥加密或签名，并可通过监视密钥使用情况来查找任何安全问题。

若删除了某个 Key Vault，则不会立即从 Azure 清除它。 有关此内容的含义的详细信息，请参阅本文的“技术问题”>“Key Vault”部分。

### <a name="application-insights"></a>Application Insights

医疗保健组织通常具有任务和生命关键系统，这些系统必须可靠且可复原。 服务异常和中断必须尽快检测到并加以修复。 [Application Insights](/azure/application-insights/app-insights-proactive-application-security-detection-pack?WT.mc_id=ms-docs-dastarr) 是一种应用程序性能管理 (APM) 技术，可监视应用程序并在出错时发送警报。 它会监视运行中的应用程序是否出现错误或应用程序异常。 它可与多种编程语言结合使用，并可提供一套丰富的功能，来帮助确保应用程序正常并顺利运行。

例如，应用程序可能会存在内存泄漏。 Application Insights 可通过它监视的大量报告和 KPI 来帮助查找和诊断此类问题。 对于应用程序开发人员而言，Application Insights 是一种可靠地 APM 服务。

此[互动演示](https://analytics.applicationinsights.io/demo#/discover/home?WT.mc_id=ms-docs-dastarr)介绍了 Application Insights 的主要功能，其中包括全面的监视仪表板，健康组织中的技术人员可使用此仪表板监视应用程序状态和运行状况。

#### <a name="azure-security-center"></a>Azure 安全中心

在任务关键应用程序中，必须实时监视安全性和 KPI。 Azure 安全中心 (ASC) 可帮助确保 Azure 资源安全且受保护。 ASC 是一种安全管理和高级威胁防护服务。 可使用它对各种工作负载应用安全策略、减少受到的威胁，以及检测和响应攻击。

安全中心标准提供以下服务：

- **混合安全** – 在所有本地和云工作负载上获得统一的安全视图。 此服务对医疗保健组织通过 Azure 使用的混合云网络尤其有用。
- **高级威胁检测** – ASC 使用高级分析和 [Microsoft Intelligent Security Graph](http://cloud-platform-assets.azurewebsites.net/intelligent-security-graph/?WT.mc_id=ms-docs-dastarr) 来压制不断演变的网络攻击，并立刻削弱这些攻击。
- **访问和应用程序控件** - 通过应用适合特定工作负载且由机器学习提供支持的允许列表建议，阻止恶意软件和其他不需要的应用程序。

在健康 AI 蓝图的上下文中，ASC 会分析系统组件，并提供一个仪表板来显示订阅中的服务和资源的漏洞。 清楚的仪表板元素会显示解决方案的问题，如下所示。

- 策略和符合性
- 资源安全机制
- 威胁防护

以下是示例仪表板，其中标识了用于改进系统威胁漏洞的 13 个建议。 此外，它还显示 HIPAA 和策略合规性仅为 46%。

![威胁防护](assets/sg-healthcare-ai-blueprint-assets/threat_protection.png)

钻取高严重性安全问题会显示受影响的资源以及各个资源所需进行的修正，如下所示。

IT 人员手动保护所有资源和网络可能会花费许多小时的时间。 借助 ASC 标识给定系统中的漏洞，则可将时间用于其他战略追求。 对于许多标识的漏洞，无需管理员深入探究问题，ASC 即可自动应用修正操作，并保护相应的资源。

![高风险](assets/sg-healthcare-ai-blueprint-assets/high_risks.png)

ACS 还通过自身的威胁检测和警报功能发挥其他作用。 可使用 ACS 监视网络、计算机和云服务是否有即将来袭的攻击和攻破后活动来保护环境。
ASC 自动从各种 Azure 资源收集、分析并集成安全信息和日志。 

ASC 中的 ML 功能可以检测手动方法无法发现的威胁。 ASC 会显示一个按优先级排列的安全警报列表，并提供快速调查问题所需的信息以及关于如何修复攻击的建议。

### <a name="rbac-security"></a>RBAC 安全性

[基于角色的访问控制](/azure/role-based-access-control/role-assignments-portal?WT.mc_id=ms-docs-dastarr) (RBAC) 提供或拒绝对受保护资源的访问，有时针对每个资源的特定权限。 这可确保只有适当的用户可以访问指定的系统组件。 例如，数据库管理员可以访问包含加密患者数据的数据库，而健康护理提供者只能通过可显示相应患者记录的应用程序访问这些记录。 此应用程序通常是电子病例或电子健康记录系统。 护士无需访问数据库，而数据库管理员无需查看患者的健康记录数据。

为实现此功能，RBAC 成为了 Azure 安全性的一部分，通过它可对 Azure 资源进行极有针对性的访问管理。 通过用于各个用户的细化设置，安全和系统管理员可以非常精细地向各个用户授予权限。

### <a name="blueprint-responsibility-matrix"></a>蓝图责任矩阵

HITRUST 客户责任矩阵是一个 Excel 文档，用于支持客户实现和记录在 Azure 上构建的系统的安全控件。 此工作簿列出了相关的 HITRUST 要求，并说明了 Microsoft 和客户的相互责任。

对于在 Azure 上构建系统的客户而言，了解在云环境中实现安全控件的共同承担责任至关重要。 实现特定安全控件可以是 Microsoft 的责任、客户的责任或 Microsoft 与客户共同承担的责任。 不同的云实现会影响 Microsoft 和客户共同分担责任的方式。

请参阅以下责任表中的示例。

|Azure 责任  |客户责任  |
|---------|---------|
|Azure 负责实现、管理及监视与其服务预配环境相关的信息保护程序方法和机制。 | 客户负责实现、配置、管理及监视用于访问和使用 Azure 服务的客户控制资产的信息保护程序方法和机制。 |
|Azure 负责实现、配置、管理及监视与其服务预配环境相关的帐户管理方法和机制。 |客户还负责已部署 Azure 虚拟机实例及驻留应用程序组件的帐户管理。|

以上仅是部署云系统时要考虑的众多责任中的两个示例。 HITRUST 客户责任矩阵用于支持组织在 Azure 系统实现方面的 HITRUST 合规性。

## <a name="customization"></a>自定义

安装此蓝图后通常会对它进行自定义。 自定义此环境的原因和方法不一。

在安装此蓝图前，可通过修改安装脚本对它进行自定义。 此方法可行，但建议创建独立的 PowerShell 脚本，并在初始安装完成后运行它。 进行初始安装后，也可通过门户向系统添加新服务。

自定义可包括以下任意操作或其他更多操作。

- 向机器学习工作室添加新实验
- 向环境添加其他无关服务
- 将数据引入和 ML 实验输出修改为使用不用于 Azure SQL 患者数据库的数据源
- 向 ML 实验提供生产数据
- 清理任何即将引入的专有数据，以匹配实验所需的数据

自定义安装与使用任意 Azure 解决方案没有区别。 可以添加或删除服务/资源，从而提供新功能。 自定义此蓝图时，请注意不要改变整体的 ML 管道，以便确保实现继续工作。

## <a name="technical-issues"></a>技术问题

下列问题可能会导致蓝图安装失败，或使用不符合需求的配置进行安装。

### <a name="key-vault"></a>Key Vault

删除 Azure 资源时，Key Vault 较为独特。 保管库会由 Azure 保留，以便还原。 相应地，每次运行安装脚本时均必须将不同的前缀传入安装脚本，否则安装将因与旧保管库名称冲突而失败。 Key Vault 及所有其他资源使用提供给安装脚本的前缀命名。

安装脚本创建的 Key Vault 会以“软删除”的方式保留 30 天。 虽然当前无法通过门户访问软删除的 Key Vault，但可[从 PowerShell 管理 Key Vault](/azure/key-vault/key-vault-soft-delete-powershell?WT.mc_id=ms-docs-dastarr)，甚至可以将其手动删除。

### <a name="azure-active-directory"></a>Azure Active Directory

强烈建议在空白的 AAD 中安装此蓝图，而非安装到生产环境。 创建新的 AAD 实例，并在安装时使用其租户 ID，以避免向实时 AAD 实例添加蓝图帐户。

## <a name="technologies-presented"></a>所提供的技术

- 请详细了解 [Azure 健康数据和 AI 蓝图](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)。
- 在下方下载、克隆或创建分支：[GitHub 存储库](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)。
- [机器学习工作室](/azure/machine-learning/?WT.mc_id=ms-docs-dastarr)是科学家用于创建机器学习实验的工作区个工具数据。 它允许使用内置算法、特殊用途的小组件以及 Python 和 R 脚本。
- 机密、证书及其他专用数据保管在 [Azure Key Vault](/azure/key-vault/key-vault-whatis?WT.mc_id=ms-docs-dastarr) 中。
- 安装说明提供了所需的命令，但脚本语言 PowerShell 可帮助安装此蓝图。
- [Azure AI 库](https://gallery.azure.ai/) 根据客户的行业提供有用的 AI/ML 解决方案方案盒。 数据科学家及其他医疗保健方面的专家发布了多个解决方案。
- 借助 [Azure 安全中心](/azure/security-center/?WT.mc_id=ms-docs-dastarr)，可了解应用程序的行为、漏洞及缓解方法。

## <a name="wrapping-up"></a>总结

[Azure 健康数据 AI 蓝图](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)是一个完整的解决方案，技术人员可将它用作学习工具来深入了解 Azure 及如何确保系统符合医疗保健法规要求。 也可将其用作将 Azure 机器学习工作室作为焦点的生产系统的起点。

[下载蓝图](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)，以在几个小时内启动实现，无需数天或数周。 若安装出现问题或有蓝图方面的疑问，请访问[常见问题解答页](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md)。

请下载支持附件，以从安装和 ML 实验角度之外深入了解蓝图实现。 此附件包括以下内容：

1. [HITRUST 客户责任矩阵](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
2. [全面的威胁模型](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
3. [HITRUST 健康数据和 AI 审查白皮书](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
4. [HIPAA 健康数据和 AI 审查](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics?WT.mc_id=ms-docs-dastarr)

无论是出于学习目的使用此蓝图，还是将其用于组织的 AI/ML 解决方案种子，它都为以医疗保健为焦点在 Azure 中使用 AI/ML 提供了起点。