---
title: 通过 Azure 借助 R 重复使用推荐器系统和算法
author: kbaroni
ms.author: kbaroni
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 如何重用和优化用 R 语言编写的推荐应用程序。 依赖 Azure VM 上的 Machine Learning Server。
ms.openlocfilehash: c5c35de681abc52641952f8bc9e95095b9d99d97
ms.sourcegitcommit: b8f9ccc4e4453d6912b05cdd6cf04276e13d7244
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74263489"
---
# <a name="optimize-and-reuse-an-existing-recommendation-system"></a>优化和重用现有推荐系统  

本文介绍了如何成功地重用和改进用 R 语言编写的现有推荐系统。此过程的关键是，Microsoft Machine Learning Server 中内置的 MicrosoftML 库与 RevoScaleR 库的并行度。

## <a name="recommendation-systems-and-r"></a>推荐系统和 R

对于零售商，了解消费者偏好和购买历史记录可提升自己的竞争优势。 多年来，零售商一直在结合使用此类数据和机器学习，以确定与消费者相关的产品，并提供个性化购物体验。 这种方法称为“产品推荐”  ，可为零售商带来重要收入源。 推荐系统有助于回答如下问题：*此人将在下一次观看哪部电影？这位顾客可能会对其他什么服务感兴趣？这位顾客希望去哪儿度假？*
新客户想要了解：*使用者（订阅者）是否将续订其合同？* 这位客户的现有推荐系统会预测订阅者续订合同的概率。 在预测生成后，系统会应用额外处理，以将响应分类为“是”、“否”或“可能”。 然后，模型响应被集成到呼叫中心业务流程中。 此流程启用了服务代理，以向消费者提供个性化推荐。  
这位客户的很多早期分析产品都已内置到[编程语言 R](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server) 中，包括作为推荐系统核心的机器学习模型。 随着订阅者群扩增，数据要求和计算要求也越来越高， 以至于推荐工作负载现在非常缓慢且处理效率低下。 现在，这位客户正逐渐将 Python 纳入自己的分析产品策略。 但近期内必须要保留 R 投资，并找到更高效的开发和部署流程。 难题是如何使用 Azure 功能优化现有方法。 首先，我们执行了一项任务，以提供和验证推荐工作负载的概念证明技术堆栈。 下文总结了可用于类似项目的常规方法。  

## <a name="design-goals"></a>设计目标

关键优先级是重新设计和自动运行模型工作流，这可带来以下几项优势：

- 缩短模型开发周期和创新时间。 当数据准备和模型开发周期高效时，可增加试验量。  
- 通过新数据的更频繁重新定型周期，提供更准确的模型结果和更优质的推荐。
- 概念证明 (POC) 实现的可扩展性更高，可应用于整个生产环境。
- 通过自动执行开发、测试和部署流程，缩短投产时间。 自动化还可以减少运营错误和延隔时间。  

## <a name="requirements"></a>要求

工作流重新设计的要求如下：

- 利用团队当前掌握的 R 技能和专业知识。
- 在有意义的情况下重用代码。
- 将订阅者数据存储在数据库中，以便轻松快速地将此类数据集成到模型定型和重新定型流程中。
- 通过 Web 应用程序接口调用模型重新定型和评分。
- 使用 Azure Active Directory 在 Web 前端进行身份验证。

## <a name="technology-stack"></a>技术堆栈

此项目的管道需要使用多种技术和工具，并以下列四个方面为中心：

1. 订阅者数据的暂留和可访问性。
2. 模型开发、定型和选择。  
3. 模型部署和运营化。
4. 使用 Web 应用程序来调用评分和模型重新定型。

下面更详细地介绍了管道图中的技术组件。

![优化体系结构](assets/recommendation-engine-optimization/recommendation-architecture.png)

### <a name="microsoft-machine-learning-server"></a>Microsoft 机器学习服务器

选择 R 工作负荷的主要原因是 **[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)** 和 **Microsoft ML**。 这些包随附的函数在整个代码中广泛使用，以导入数据、创建分类模型并将模型部署到生产环境中。
MLS 部署在 Azure 中的两个 Linux 虚拟机上：一个配置为用于“开发”，另一个配置为用于“运营”。 开发 VM 预配有更多的内存和处理能力，以便促进定型和测试数百个模型。 它还托管了 RStudio Server，以便远程用户能够轻松访问 RStudio IDE。 运营服务器配置在较小的 VM 上，包含托管可通过 REST API 从 Web 应用程序调用的 R 模型所需的附加扩展。

### <a name="rstudio-server"></a>RStudio Server

[RStudio Server](https://www.rstudio.com/products/rstudio/#Server)  是 Linux 应用程序，用于为远程客户端或笔记本电脑客户端提供基于浏览器的接口。 它在端口 8787 上运行，并且在 Azure VM 上创建有网络安全规则后可用于远程连接。 对于更青睐 RStudio IDE 的分析员和数据科学家，可使用它高效提供对包含大量计算和内存容量的虚拟机的访问权限。 可下载源版本和商业版。

### <a name="azure-sql-database"></a>Azure SQL 数据库

最初，订阅者数据存储在一个非常大的 .csv 文件中，具体包含 500 名唯一订阅者的 600 万行购买和偏好信息。 将数据存储在数据库中意味着，可以更快地从 R 访问数据，并允许执行筛选读取。 无需再导入整个数据集以供定型或重新定型：数据由订阅者在数据库源处进行筛选，大大减少了导入和处理数据所需的资源。  
Azure 中有多个托管云数据库选项。 之所以选择 [Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/)是因为，客户熟悉 SQL Server；更重要的是，未来计划将 SQL Server 机器学习服务更广泛地引入 Azure SQL 数据库。 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017)是数据库内置功能，用于通过存储过程执行 R 和 Python 工作负载。

### <a name="nodejs-and-reactjs"></a>Node.js 和 React.js

创建 Web 接口是为了调用 R 脚本，并保护网站。 之所以选择 Node.js 作为 Web 服务器框架是因为，它为分布式环境中的 Web 应用程序启用了轻型高效的运行时环境。 React 位于前端，用于生成用户界面，并调用 Web 服务器上托管的 Web 服务。 Node + React 被认为是打造模型管道 Web 服务原型的最快速途径。  

## <a name="infrastructure-implementation"></a>基础设施实现

以下各部分介绍了此项目的服务器基础设施的部署方式。 正确开发和部署基础设施与确定要应用的建模方法和技术同样至关重要。

### <a name="initial-database-load"></a>初始数据库加载

第一步是，将非常大的 .csv 文件中的订阅者数据导入 Azure SQL 数据库。 这篇[参考文章](https://blogs.msdn.microsoft.com/sqlcat/2010/07/30/loading-data-to-sql-azure-the-fast-way/)中介绍了多种将数据导入 Azure SQL 数据库的方法。 我们采用的方法如下：

1. 按照[此处](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)的步骤操作，通过 Azure 门户创建了数据库。
2. 下载并使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)，以从 VM 连接到数据库。
3. 选择了 [SQL 导入/导出向导](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard?view=sql-server-2017)（若有时间约束，可使用更多高性能数据导入选项）。 请注意，导入/导出向导会将数据类型从数据源映射到目标位置；在我们的方案中，所有数据元素都映射到可接受的 varchar(max) 数据类型。 如果你的方案需要其他映射，可以在向导中修改数据类型（[参考文章](https://docs.microsoft.com/sql/integration-services/import-export-data/data-type-mapping-in-the-sql-server-import-and-export-wizard?view=sql-server-2017)）。  
4. 由于提交到数据库的大多数查询都按字段 subscriber_id  进行筛选，因此我们为此字段创建了索引。

### <a name="web-application"></a>Web 应用程序

Web 应用程序负责执行三个功能：

- 身份验证：Web 用户通过 React  前端对 Azure Active Directory  进行身份验证。
- 模型评分：接受用户提供的有关特定订阅者的输入数据，使用 REST API 将订阅者数据提交到 Web 服务，这会返回预测响应。  
- 模型重新定型：接受订阅者标识符作为输入，并对开发服务器调用 R 脚本，以重新定型相应订阅者的模型。

使用 Azure Active Directory  实现单一登录 (SSO) 比预期更具挑战性。 这是由于单页应用程序 (SPA) 框架所致。 一个 Azure Active Directory 库是取得成功的关键，就是 [react adal](https://github.com/salvoravida/react-adal)。 下面的参考文章提供了有关实现身份验证的实用指南：

- [Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)
- [单页应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios#single-page-application-spa)

### <a name="development-vm-mls-930"></a>开发 VM (MLS 9.3.0)

开发 VM 托管了模型开发、定型和重新定型以及分类模型部署。 Azure VM (DS13 V2) 预配有 Linux/Ubuntu 16.10，并将以下内容安装到基础 VM 上：

- Machine Learning Server 9.3.0（有关说明，请单击[此处](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-install)）。 请务必完成创建验证步骤，以确认是否安装。 由于这是开发 VM，我们忽略了“启用 Web 服务部署和远程连接”  部分。
- [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/)（开放源代码版本）。 请注意，不要重新安装 R/r-base（之前已与 MLS 9.3.0 一起安装）。  
- [将网络安全组添加到](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal) VM，以便能够通过端口 8787 为 RStudio Server 实现入站连接。  
- ODBC 驱动程序，用于处理开发 VM 和 Azure SQL 数据库之间的通信。 VM 上安装了以下 odbc 驱动程序：  
- 与 Linux/ubuntu 16.10 兼容的 [ODBC Driver for SQL Server 17](https://www.microsoft.com/download/details.aspx?id=56567)  
- 开放源代码 ODBC 驱动程序 unixodbc（有关安装说明，请参阅[使用 ODBC 导入关系数据](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-odbc)）。 注意：这篇文章中的 Ubuntu 说明有两个拼写错误。  
- 若要检查 unixodbc 是否已安装，请运行以下命令：       ````Apt list –installed | grep unixODBC (should be unixodbc)````
      - 若要安装此驱动程序，请运行以下命令：````sudo apt-get install unixodbc-devel unixodbc-bin unixodbc (should be unixodbc-dev)````

### <a name="operations-vm-mls-930"></a>运营 VM (MLS 9.3.0)

运营 VM 托管了模型 Web 服务和终结点，存储了 Swagger 文件和分类模型的序列化版本。 配置非常类似于 MLS 开发服务器。 不过，它配置为用于运营化。也就是说，为 REST 终结点提供服务所需的 Web 服务已安装。 若要部署运营 VM，可使用 ARM 模板进行快速部署。 请参阅：[使用 ARM 模板配置 Microsoft Machine Learning Server 9.3 以使分析可操作化](https://blogs.msdn.microsoft.com/mlserver/2018/02/27/configuring-microsoft-machine-learning-server-9-3-to-operationalize-analytics-using-arm-templates/)。 我们的项目使用此 [ARM 模板](https://github.com/Microsoft/microsoft-r/tree/master/mlserver-arm-templates/one-box-configuration/linux)部署了一体化配置  。  
这样一来，支持模型管道的服务器组件便能正常运行了。

## <a name="model-implementation"></a>模型实现

一项关键决策影响了此项目的最终模型设计，即迁移到“多模型”设计，而不是单一整体模型。 两者的区别在于，每个订阅者都有自己的分类模型，而不是使用一个大型分类模型为所有订阅者提供服务。 对于这位客户而言，之所以首选这种方法是因为，较小模型的内存占用情况也较小，并且更易于在生产中实现横向扩展。

### <a name="data-import"></a>数据导入

模型开发所需的全部数据都驻留在 Azure SQL 数据库  中。 对于模型定型和重新定型，数据导入分两个阶段完成：

1. 将查询提交到数据库，以检索特定 subscriber_id  的数据和返回的结果集。 为了对数据库进行查询访问，我们考虑了以下两种方法：

- 名为 [RxSQLServerData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata) 的 RevoScaleR 函数
- R odbc 包

最终决定使用在数据库一级启用了数据筛选的 R odbc 库。 从数据库表中仅筛选出特定订阅者模型所需的行，最大程度地减少了要读入 R 并处理的行数。 这还减少了定型或重新定型每个模型所需的内存、计算和总时间。  

1. 将结果集转换为 R 数据帧，并按分类算法要求，将一些数据类型从 varchars 显式转换为整数或数字。 对于此功能，使用的是 RevoScaleR 函数 [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport)。 rxImport  函数与 RevoScaleR 和 MicrosoftML 捆绑在一起，设计为采用多线程。 下面的示例展示了我们是如何使用它的：

````r

# Populate the data frame and modify column types as needed
input_data <- rxImport(sqlServerDataDS, colClasses = c(  
Approved="integer",
      OnTimeArrivalRate="numeric",
      Amount="numeric",
      IsInformed="numeric",
      <continue with list of columns> )
# View the characteristics of the variables in the data source
rxGetInfo (input_data, getVarInfo = TRUE)
````

## <a name="unbalanced-data"></a>非均衡数据

因为推荐模型的目标是预测客户续订合同的概率，并将概率分类为“是”、“否”或“可能”，所以使用了分类算法。 非均衡数据集可能会严重影响分类算法的准确度和性能。  
如果一个“分类”的样本多于另一个“分类”，那么数据集就是非均衡。 在这种情况下，对每个订阅者可用的行数是非均衡的：在高端，一个订阅者有超过 100 万行；在低端，330 个订阅者有不到 100 数据行。 下图展示了每个订阅者的行数（样本）非均衡性：![非均衡性图表](assets/recommendation-engine-optimization/recommendaton_engine_chart.png)

处理非均衡数据集的一种技术是，更改数据集，并对代表性不足的分类进行过采样，或对代表性过度的分类进行降采样。 另一种技术是，利用数据所有者对数据及其属性的确切知识来综合生成附加数据。 这位客户建立了订阅者的最小样本量阈值。 对于低于此阈值的订阅者，需要处理数据。 此项目对这两种方法都有探索。

## <a name="algorithm-selection"></a>算法选择

我们评估了三种不同的分类算法实现：rxDForest  、rxFastTrees  和 rxFastForest  。 所有这三种算法都利用了多线程和并行度。 Microsoft ML 将使用多个 CPU 或 GPU（若有）。 模型评估标准包括：

- 新模型比原始模型更准确吗？
- 生产中运行的模型的内存占用情况是多少？ 在不影响准确度的情况下，运营环境是否支持同时执行多个准实时返回预测响应的模型？
- 算法处理非均衡数据集的效果如何？ 是否需要通过生成综合数据来预处理非均衡性？

下表汇总了调查结果：

| 算法 | 说明 | 调查结果 | 说明 |
| :--------- | :------------ | :--------- | :--------------- |
| [rxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees) | 并行实现已提升决策树，以实现 FastRank 的多线程版本。 | 准确且性能速度最快。 | 没有针对非均衡数据的特殊功能。 需要提供预处理数据作为输入 |
| [rxFastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest) | 并行实现随机林，并使用 rxFastTrees 生成决策树的集成学习器。 | 使用预处理数据后比原始模型更准确。 更少占用大量内存，比 rxDForest 更快 |没有针对非均衡数据的特殊功能。 需要提供预处理数据作为输入。 |
| [rxDForest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdforest) | 并行实现随机林。 包含在 RevoScaleR 中。 可以仅在函数调用中就处理非均衡数据（删除缺失数据、为数据设置条件和处理样本分层）。 | 比原始模型更快。 与原始模型一样准确或更准确。 使用各种重新采样和综合技术来处理非均衡数据集。 内存占用情况最大。  | 内存占用情况最大是因为，它在函数内包含有条件的数据。   虽然数据处理的效果很好，但不如数据所有者提供的自定义转换效果好。 |

最后，这位客户选择了 rxFastForest  算法，并决定使用 [vtreat](https://cran.r-project.org/web/packages/vtreat/index.html) 库和添加自定义数据预处理步骤，以为代表性不足的订阅者综合生成数据，从而处理非均衡数据。

## <a name="model-deployment--web-services"></a>模型部署和 Web 服务

发布模型以供部署到运营 VM 是很容易的，[使用 mrsdeploy 将 R 模型部署为 Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/quickstart-publish-r-web-service)这篇快速入门文档中进行了说明。
在我们的方案中，在开发 VM 上创建模型后，我们就会按以下步骤操作，在运营 VM 上发布它们：

1. 使用 mrsdeploy 包中的两个身份验证函数之一，建立从开发 VM 到运营 VM 的远程登录：使用本地管理员名称和密码的 remoteLogin()，或使用 Azure Active Directory 的 remoteLoginAAD()。 “使用 mrsdeploy 登录 Machine Learning Server 或 R Server”这篇参考文章中介绍了这两个选项，它们都打开远程会话。  
2. 模型定型后，立即使用 mrsdeploy 包中的 publishService 或 updateService 函数，将模型发布到运营 VM。 对于这个项目，我们使用了多种部署方法，具体是发布新模型还是更新现有模型视所选方法而定。 为了处理这两种情况，我们实现了以下代码：

````r
# If the service does not exist, publish it and if it does exist, update it.  
# No service by this name so publish one
 api <- publishService(serviceName, code =sc_predict, model = model,  
      inputs = list(prop_data="data.frame"),  
      outputs = list(answer = "numeric"), v = "v1.0.0" )
 print("=========== Model Created =============")
} else {
# A service by this name already exists, update it  
 api <- updateService(serviceName, model = model,
     inputs = list(prop_data="data.frame"), v = "v1.0.0" )
 print("=========== Model Updated =============")
}
 ````

部署后，模型就会进行序列化，并存储在运营服务器上，可通过处于标准  或实时  模式下的 Web 服务进行使用。 每当调用的是处于标准模式下的 Web 服务时，R 和所需的任何库都会随每次调用一起加载和卸载。 相比之下，对于实时  模式，R 和库只加载一次，并可重用于后续 Web 服务调用。 由于 Web 服务调用的大部分开销是加载 R 和库，因此实时模式的模型评分延迟更低，响应时间不到 10ms。 有关标准和实时模式，请参阅[此处](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)的文档和参考示例。 虽然实时模式适用于单一预测，但也可以传入输入数据帧以用于评分。 下面这篇参考文章中对此进行了介绍：[借助 mrsdeploy 通过批处理来使用异步 Web 服务](https://docs.microsoft.com/machine-learning-server/operationalize/how-to-consume-web-service-asynchronously-batch)。

## <a name="conclusion"></a>结束语

利用 Microsoft Machine Learning Server 中内置的 MicrosoftML 和 RevoScaleR 库的并行度，我们加速了数百个订阅者的各个分类模型的开发、部署和评分。 模型准确度有所提升，定型和重新定型时间有所缩短，所有这些都只需对现有 R 基准代码进行极少更改。
复杂的是，实现基础设施以支持模型管道，并对技术组件进行正确端到端配置。 若要开始使用你自己的方法，请参阅下面的一些参考文章：

- [Machine Learning Server 文档](https://docs.microsoft.com/machine-learning-server/)
- [适用于 Machine Learning Server 的 R 教程](https://docs.microsoft.com/advanced-analytics/tutorials/sql-server-r-tutorials?view=sql-server-2017)
- [适用于 Machine Learning Server 的 R 示例](https://docs.microsoft.com/machine-learning-server/r/r-samples)
- [R 函数库参考](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)

## <a name="references"></a>参考

若对构建零售业务的其他预测解决方案感兴趣，请访问 Azure [AI 库](https://gallery.azure.ai/)的[零售部分](https://gallery.azure.ai/industries/retail)。  