---
ms.openlocfilehash: cff221055e76d7334793782d19eadd0960712a1f
ms.sourcegitcommit: 461c520509d53bae1021eebf9733a98edbf71e4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716850"
---
# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a>通过 Azure 和 R 启用金融服务风险生命周期


风险计算在关键金融服务操作生命周期的几个阶段至关重要。 例如，简单的保险产品管理生命周期可能类似于下图。 风险计算各方面以蓝色文字显示。

![](./assets/fsi-risk-modeling/image1.png)

资本市场公司的情形可能如下所示：

![](./assets/fsi-risk-modeling/image2.png)

通过这些流程，风险建模的共同需求包括：

1.  风险分析师、保险公司的精算师或资本市场公司的金融工程师需要进行临时风险相关试验。
    这些分析师通常使用所在领域中的热门代码和建模工具：R 和 Python。 许多大学课程体系在数学金融和 MBA 课程中包含 R 或 Python 培训。
    这两种语言都提供了广泛的开源库，支持热门的风险计算。 除了适当的工具之外，分析师通常还需要具有：

    a.  准确的市场定价数据。

    b.  现有保单和索赔数据。

    c.  现有市场定位数据。

    d.  其他外部数据。 来源包括结构化数据，如死亡率表和具有竞争力的定价数据。 也可以使用诸如天气、新闻和其他一些不够传统的来源。

    e.  计算能力，用于实现快速交互式数据调查。

2.  他们还可以利用临时机器学习算法来确定定价或市场策略。

3.  需要可视化和呈现数据以用于产品规划、交易策略和类似讨论。

4.  快速执行由分析师配置的定义模型，用于定价、估值和市场风险。 估值结合使用专用风险建模、市场风险工具和自定义代码。 批量执行分析，每晚、每周、每月、每季度和每年的计算变化都会产生工作负荷峰值。

5.  将数据与其他企业范围的风险度量指标整合为综合风险报告。 在较大的组织中，较低级别的风险评估可能会转移到企业风险建模和报告工具中。

6.  必须在规定的时间间隔内按定义的格式报告结果，以满足投资者和监管要求。

Microsoft 通过 [Azure 市场](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely)中的 Azure 服务和合作伙伴产品/服务的组合为上述各项考虑因素提供支持。 在本文中，我们将实际举例说明如何使用 R 执行临时试验。我们首先解释如何在单个计算机上运行试验，然后展示如何在 [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) 上运行相同的试验，最后，演示如何在我们的建模中利用外部服务。 有关在 Azure 上执行已定义模型的选项和注意事项，在以下关于[银行业](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)和[保险业](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)的文章中进行了说明。

## <a name="analyst-modelling-in-r"></a>分析师通过 R 建模 

我们首先看一下分析师在具有代表性的简化资本市场情形中如何使用 R。 可以通过引用现有的 R 库进行计算的方式，或通过从头开始编写代码的方式来进行构建。 在我们的示例中，还必须获取外部定价数据。 为使示例简明扼要，我们计算了股权远期合同的潜在风险暴露 (PFE)。
此示例避免了复杂衍生工具等复杂的定量建模技术，侧重于单一风险因素，专注于风险生命周期。 示例将执行以下操作：

1.  选择感兴趣的工具。

2.  该工具的源历史价格。

3.  通过简单的蒙特卡罗 (MC) 方法对股权价格建模，其中用到几何布朗运动 (GBM)：

    a.  估计预期收益 μ (mu) 和波动率 σ (theta)。

    b.  将模型校准为历史数据。

4.  使各种路径可视化以传达结果。

5.  绘制最大值（0，股票价值）以证明 PFE 的含义，与风险值 (VaR) 之间的差异

    a.  具体公式：PFE = 股价 (T) -- 远期合同价格 K

6.  取 0.95 分位数来获得模拟期间每个时间步长/时间终点的 PFE 值

我们将根据 MSFT 股票来计算股权远期的潜在风险暴露。 如上所述，要为股票价格建模，需要 MSFT 股票的历史价格，以便将模型校准为历史数据。 有很多方法可以获得历史股票价格。 示例使用了来自外部服务提供商 [Quandl](https://www.quandl.com/) 的免费版股票价格服务。


> 注意：该示例使用了可用于了解概念的 [WIKI 价格数据集](https://www.quandl.com/databases/WIKIP)。 对于美国股权的生产使用情况，Quandl 建议使用[美股盘后数据数据集](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)。

要处理数据并定义与股权相关的风险，需要完成以下事项：

1.  从股权中检索历史数据。

2.  根据历史数据确定预期收益 μ 和波动率 σ。

3.  使用一些模拟数据对基础股票价格进行建模。

4.  运行模型

5.  确定股权的潜在风险暴露。

首先，通过 Quandl 服务检索股票，并绘制过去 180 天的收盘价历史记录。

````R
# Lubridate package must be installed
if (!require(lubridate)) install.packages('lubridate')
library(lubridate)

# Quandl package must be installed
if (!require(Quandl)) install.packages('Quandl')
library(Quandl)

# Get your API key from quandl.com
quandl_api = "enter your key here"

# Add the key to the Quandl keychain
Quandl.api_key(quandl_api)

quandl_get <-
    function(sym, start_date = "2018-01-01") {
        require(devtools)
        require(Quandl)
        # Retrieve the Open, High, Low, Close and Volume Column for a given Symbol
        # Column Indices can be deduced from this sample call
        # data <- Quandl(c("WIKI/MSFT"), rows = 1)

        tryCatch(Quandl(c(
        paste0("WIKI/", sym, ".8"),    # Column 8 : Open
        paste0("WIKI/", sym, ".9"),    # Column 9 : High
        paste0("WIKI/", sym, ".10"),   # Column 10: Low
        paste0("WIKI/", sym, ".11"),   # Column 11: Close
        paste0("WIKI/", sym, ".12")),  # Column 12: Volume
        start_date = start_date,
        type = "raw"
        ))
    }

# Define the Equity Forward
instrument.name <- "MSFT"
instrument.premium <- 100.1
instrument.pfeQuantile <- 0.95

# Get the stock price for the last 180 days
instrument.startDate <- today() - days(180)

# Get the quotes for an equity and transform them to a data frame
df_instrument.timeSeries <- quandl_get(instrument.name,start_date = instrument.startDate)

#Rename the columns
colnames(df_instrument.timeSeries) <- c()
colnames(df_instrument.timeSeries) <- c("Date","Open","High","Low","Close","Volume")

# Plot the closing price history to get a better feeling for the data
plot(df_instrument.timeSeries$Date, df_instrument.timeSeries$Close)
````

我们使用现有数据校准 GBM 模型。

````R
# Code inspired by the book Computational Finance, An Introductory Course with R by 
#    A. Arratia.

# Calculate the daily return in order to estimate sigma and mu in the Wiener Process
df_instrument.dailyReturns <- c(diff(log(df_instrument.timeSeries$Close)), NA)

# Estimate the mean of std deviation of the log returns to estimate the parameters of the Wiener Process

estimateGBM_Parameters <- function(logReturns,dt = 1/252) {

    # Volatility
    sigma_hat = sqrt(var(logReturns)) / sqrt(dt)

    # Drift
    mu_hat = mean(logReturns) / dt + sigma_hat**2 / 2.0

    # Return the parameters
    parameter.list <- list("mu" = mu_hat,"sigma" = sigma_hat)

    return(parameter.list)
}

# Calibrate the model to historic data
GBM_Parameters <- estimateGBM_Parameters(df_instrument.dailyReturns[1:length(df_instrument.dailyReturns) - 1])
````

接下来，我们为基础股票价格建模。 可以从头开始实施离散型 GBM 流程，也可以使用提供此功能的众多 R 包之一。 我们使用 R 包 [sde  （随机微分方程的模拟和推断）](https://cran.r-project.org/web/packages/sde/index.html)，它提供了可解决此问题的一种方法。 GBM 方法需要一组参数，这些参数要么根据历史数据进行校准，要么是既定的模拟参数。 我们使用了历史数据，在开始模拟时提供 μ、σ 和股票价格 (P0)。

````R
if (!require(sde)) install.packages('sde')
library(sde)

sigma <-  GBM_Parameters$sigma
mu <- GBM_Parameters$mu
P0 <- tail(df_instrument.timeSeries$Close, 1)

# Calculate the PFE looking one month into the future
T <- 1 / 12

# Consider nt MC paths
nt=50

# Divide the time interval T into n discrete time steps
n = 2 ^ 8 

dt <- T / n
t <- seq(0,T,by=dt)
````

现在准备开始蒙特卡罗模拟，用于模拟某些模拟路径的潜在暴露。 我们将模拟限制为 50 个蒙特卡罗路径和 256 个时间步长。 为了准备扩展模拟并利用 R 中的并行化功能，蒙特卡罗模拟循环使用 foreach 语句。

````R
# Track the start time of the simulation
start_s <- Sys.time()

# Instead of a simple for loop to execute a simulation per MC path, call the
# simulation with the foreach package
# in order to demonstrate the similarity to the AzureBatch way to call the method.

library(foreach)
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package
exposure_mc <- foreach (i=1:nt, .combine = rbind ) %do%  GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()

# Track the end time of the simulation
end_s <- Sys.time()

# Duration of the simulation

difftime(end_s, start_s) 
````

现在模拟了基础 MSFT 股票的价格。 为计算股权远期的风险暴露，我们减去溢价并将风险暴露限制为仅为正值。

````R
# Calculate the total Exposure as V_i(t) - K, put it to zero for negative exposures
pfe_mc <- pmax(exposure_mc - instrument.premium ,0)

ymax <- max(pfe_mc)
ymin <- min(pfe_mc)
plot(t, pfe_mc[1,], t = 'l', ylim = c(ymin, ymax), col = 1, ylab="Credit Exposure in USD", xlab="time t in Years")
for (i in 2:nt) {
    lines(t, pfe_mc[i,], t = 'l', ylim = c(ymin, ymax), col = i)
}
````

接下来的两张图片显示了模拟的结果。 第一张图显示了 50 条路径的基础股票价格的蒙特卡罗模拟。 第二张图显示了在减去股权远期溢价并将风险暴露限制在正值之后，股权远期的潜在信贷风险。


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| 图 1 - 50 个蒙特卡罗路径 | 图 2 - 股权远期的信贷风险 |


最后一步，通过以下代码计算 1 个月的 0.95 分位数 PFE。

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

图 3 MSFT 股权远期的潜在风险暴露

## <a name="using-azure-batch-with-r"></a>将 Azure Batch 与 R 结合使用 

上述 R 解决方案可以与 Azure Batch 结合，并利用云进行风险计算。 对于像我们这样的并行计算而言，几乎不需要额外的工作量。 [使用 Azure Batch 运行并行 R 模拟](https://docs.microsoft.com/en-us/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)教程详细介绍了如何将 R 连接到 Azure Batch。 下面介绍连接到 Azure Batch 的过程的代码和摘要，以及如何在简化的 PFE 计算中利用云的扩展。

此示例处理前面描述的相同模型。 正如我们之前看到的，这种计算可以在个人计算机上运行。 增加蒙特卡罗路径的数量或使用更小的时间步长将导致更长的执行时间。 几乎所有的 R 代码都将保持不变。 我们将在本节中突出显示差异。

蒙特卡罗模拟的每条路径都在 Azure 中运行。 我们之所以能够这样做，是因为每条路径都独立于其他路径，让我们进行“令人尴尬的并行”计算。

要使用 Azure Batch，首先定义基础集群并在代码中引用它，然后才能在计算中使用集群。 要运行计算，我们使用以下 cluster.json 定义：

````JavaScript
{
  "name": "myMCPool",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 1,
      "max": 1
    },
    "lowPriorityNodes": {
      "min": 3,
      "max": 3
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": [],
  "subnetId": ""
}
````

通过此群集定义，以下 R 代码使用群集：
````R
# Define the cloud burst environment
library(doAzureParallel)

# set your credentials
setCredentials("credentials.json")

# Create your cluster if not exist
cluster <- makeCluster("cluster.json")

# register your parallel backend
registerDoAzureParallel(cluster)

# check that your workers are up
getDoParWorkers()
````

最后，我们更新之前的 foreach 语句以使用 doAzureParallel 包。 这是一个小幅改动，添加对 sde 包的引用并将 %do% 更改为 %dopar%：

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

每个蒙特卡罗模拟都作为任务提交给 Azure Batch。 这些任务在云中执行。 结果在被发送回分析师工作平台之前进行合并。 繁重的工作和计算在云中执行，以充分利用请求的计算所需的扩展和基本基础结构。

计算完成后，可以通过调用以下单个指令轻松关闭其他资源：

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a>使用 SaaS 产品/服务

前两个示例显示了如何利用本地和云基础结构来开发适当的估值模型。 这种范式已经开始转变。 与本地基础结构转变为基于云的 IaaS 和 PaaS 服务的方式相同，相关风险数据的建模正在转变为面向服务的流程。
如今的分析师面临两大挑战：

1.  监管机构要求使用不断提高的计算能力来增加建模要求。 监管机构更频繁地要求提供最新的风险数据。

2.  现有的风险基础结构随着时间的推移有机地增长，并且在以敏捷方式实现新要求和更高级的风险建模时带来了挑战。

基于云的服务可以提供所需的功能并支持风险分析。 这种方法具有一些优点：

-   监管机构要求的最常见风险计算必须由受法规约束的每个人去实现。 通过利用专业服务提供商的服务，分析师可以从符合监管机构要求的即用型风险计算中受益。 此类服务可能包括市场风险计算、交易对手风险计算、X 值调整 (XVA)，甚至交易账户根本审查 (FRTB) 计算。

-   这些服务通过 Web 服务公开其接口。 这些其他服务可以增强现有的风险基础结构。

在示例中，我们想要为 FRTB 计算调用基于云的服务。 其中一些可以在 [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 上找到。 在本文中，我们选择了 [Vector Risk](http://www.vectorrisk.com/) 的试用选项。 我们将继续修改系统。 这次使用服务来计算感兴趣的风险数据。 此过程包括以下步骤：

1.  使用正确的参数调用相关的风险服务。

2.  等待服务完成计算。

3.  检索结果并将结果合并到风险分析中。

转换为 R 代码，我们的 R 代码能够通过从准备好的输入模板定义所需的输入值得以增强。

````R
Template <- readLines('RequiredInputData.json')
data <- list(
# drilldown setup
  timeSteps = seq(0, n, by = 1),
  paths = as.integer(seq(0, nt, length.out = min(nt, 100))),
# calc setup
  calcDate = instrument.startDate,
  npaths = nt,
  price = P0,
  vol = sigma,
  drift = mu,
  premium = instrument.premium,
  maturityDate = today()
  )
body <- whisker.render(template, data)
````


接下来，需要调用 Web 服务。 本例中调用 StartCreditExposure 方法来触发计算。 我们将 API 的终结点存储在名为“endpoint”  的变量中。

````R
# make the call
result <- POST( paste(endpoint, "StartCreditExposure", sep = ""),  
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
                body = body, encode = "raw"
               )

result <- content(result)
````

计算完成后，检索结果。

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

这使分析师可以继续处理收到的结果。 从结果中提取相关风险数据并进行绘制。

````R
if (!is.null(result$error)) {
    cat(result$error$message)
} else {
    # plot PFE
    result <- result$getCreditExposureResultsResponse$getCreditExposureResultsResult
    df <- do.call(rbind, result$exposures)
    df <- as.data.frame(df)
    df <- subset(df, term <= n)   
}

plot(as.numeric(df$term[df$statistic == 'PFE']) / 365, df$result[df$statistic == 'PFE'], type = "p", xlab = ("time t in Years"), ylab = ("Potential Future Exposure in USD"), ylim = range(c(df$result[df$statistic == 'PFE'], df$result[df$statistic == 'PFE'])), col = "red")
````


结果图如下所示：

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| 图 4 - MSFT 股权远期的信贷风险 - <br/>使用基于云的风险引擎进行计算 | 图 5 - MSFT 股权远期的潜在风险暴露 - <br/> 使用基于云的风险引擎进行计算 |



## <a name="next-steps"></a>后续步骤

通过计算基础结构和基于 SaaS 的风险分析服务灵活地访问云，可以提高在资本市场和保险行业工作的风险分析师的速度和敏捷性。 本文举例说明了如何借助风险分析师熟知的工具来使用 Azure 和其他服务。 在创建和增强风险模型时，请尝试利用 Azure 的功能。

### <a name="tutorials"></a>教程


-   R 开发人员：[使用 Azure Batch 运行并行 R 模拟](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [基本 R 命令和 RevoScaleR 函数：25 个常见示例](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [使用 RevoScaleR 可视化和分析数据](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [HDInsight 上的 ML Services 和开放源代码 R 功能简介](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

_本文由 Darko Mocelj 博士和 Rupert Nicolay 共同撰写。_