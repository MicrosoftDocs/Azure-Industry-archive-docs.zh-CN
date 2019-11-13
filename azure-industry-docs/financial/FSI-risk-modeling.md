---
title: 手机银行欺诈解决方案指南
description: 说明如何在 2 秒内检测到欺诈性事务
author: mauiguitar
ms.author: sihiga
ms.service: industry
ms.topic: overview
ms.date: 10/31/2019
ms.openlocfilehash: c5ea4384d02548e4d681b1c13fd81066a955d6a2
ms.sourcegitcommit: f42a60539bec2a7769b42b6574f09eed4d1b6c79
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750527"
---
# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a><span data-ttu-id="99c9e-103">通过 Azure 和 R 启用金融服务风险生命周期</span><span class="sxs-lookup"><span data-stu-id="99c9e-103">Enabling the financial services risk lifecycle with Azure and R</span></span>


<span data-ttu-id="99c9e-104">风险计算在关键金融服务操作生命周期的几个阶段至关重要。</span><span class="sxs-lookup"><span data-stu-id="99c9e-104">Risk calculations are pivotal at several stages in the lifecycle of key financial services operations.</span></span> <span data-ttu-id="99c9e-105">例如，简单的保险产品管理生命周期可能类似于下图。</span><span class="sxs-lookup"><span data-stu-id="99c9e-105">For example, a simplified form of the insurance product management lifecycle might look something like the diagram below.</span></span> <span data-ttu-id="99c9e-106">风险计算各方面以蓝色文字显示。</span><span class="sxs-lookup"><span data-stu-id="99c9e-106">The risk calculation aspects are shown in blue text.</span></span>

![](./assets/fsi-risk-modeling/image1.png)

<span data-ttu-id="99c9e-107">资本市场公司的情形可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="99c9e-107">A scenario in a capital markets firm might look like this:</span></span>

![](./assets/fsi-risk-modeling/image2.png)

<span data-ttu-id="99c9e-108">通过这些流程，风险建模的共同需求包括：</span><span class="sxs-lookup"><span data-stu-id="99c9e-108">Through these processes there are common needs around risk modelling including:</span></span>

1. <span data-ttu-id="99c9e-109">风险分析师、保险公司的精算师或资本市场公司的金融工程师需要进行临时风险相关试验。</span><span class="sxs-lookup"><span data-stu-id="99c9e-109">The need for ad-hoc risk-related experimentation by risk analysts; actuaries in an insurance firm or quants in a capital markets firm.</span></span>
    <span data-ttu-id="99c9e-110">这些分析师通常使用所在领域中的热门代码和建模工具：R 和 Python。</span><span class="sxs-lookup"><span data-stu-id="99c9e-110">These analysts typically work with code and modelling tools popular in their domain: R and Python.</span></span> <span data-ttu-id="99c9e-111">许多大学课程体系在数学金融和 MBA 课程中包含 R 或 Python 培训。</span><span class="sxs-lookup"><span data-stu-id="99c9e-111">Many university curriculums include training in R or Python in mathematical finance and in MBA courses.</span></span>
    <span data-ttu-id="99c9e-112">这两种语言都提供了广泛的开源库，支持热门的风险计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-112">Both languages offer a wide range of open source libraries which support popular risk calculations.</span></span> <span data-ttu-id="99c9e-113">除了适当的工具之外，分析师通常还需要具有：</span><span class="sxs-lookup"><span data-stu-id="99c9e-113">Along with appropriate tooling, analysts often require access to:</span></span>

    <span data-ttu-id="99c9e-114">a.</span><span class="sxs-lookup"><span data-stu-id="99c9e-114">a.</span></span>  <span data-ttu-id="99c9e-115">准确的市场定价数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-115">Accurate market pricing data.</span></span>

    <span data-ttu-id="99c9e-116">b.</span><span class="sxs-lookup"><span data-stu-id="99c9e-116">b.</span></span>  <span data-ttu-id="99c9e-117">现有保单和索赔数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-117">Existing policy and claims data.</span></span>

    <span data-ttu-id="99c9e-118">c.</span><span class="sxs-lookup"><span data-stu-id="99c9e-118">c.</span></span>  <span data-ttu-id="99c9e-119">现有市场定位数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-119">Existing market position data.</span></span>

    <span data-ttu-id="99c9e-120">d.</span><span class="sxs-lookup"><span data-stu-id="99c9e-120">d.</span></span>  <span data-ttu-id="99c9e-121">其他外部数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-121">Other external data.</span></span> <span data-ttu-id="99c9e-122">来源包括结构化数据，如死亡率表和具有竞争力的定价数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-122">Sources include structured data such as mortality tables and competitive pricing data.</span></span> <span data-ttu-id="99c9e-123">也可以使用诸如天气、新闻和其他一些不够传统的来源。</span><span class="sxs-lookup"><span data-stu-id="99c9e-123">Less traditional sources such as weather, news and others may also be used.</span></span>

    <span data-ttu-id="99c9e-124">e.</span><span class="sxs-lookup"><span data-stu-id="99c9e-124">e.</span></span>  <span data-ttu-id="99c9e-125">计算能力，用于实现快速交互式数据调查。</span><span class="sxs-lookup"><span data-stu-id="99c9e-125">Computational capacity to enable quick interactive data investigations.</span></span>

2. <span data-ttu-id="99c9e-126">他们还可以利用临时机器学习算法来确定定价或市场策略。</span><span class="sxs-lookup"><span data-stu-id="99c9e-126">They may also make use of ad-hoc machine learning algorithms for  pricing or determining market strategy.</span></span>

3. <span data-ttu-id="99c9e-127">需要可视化和呈现数据以用于产品规划、交易策略和类似讨论。</span><span class="sxs-lookup"><span data-stu-id="99c9e-127">The need to visualize and present data for use in product planning,  trading strategy, and similar discussions.</span></span>

4. <span data-ttu-id="99c9e-128">快速执行由分析师配置的定义模型，用于定价、估值和市场风险。</span><span class="sxs-lookup"><span data-stu-id="99c9e-128">The rapid execution of defined models, configured by the analysts, for pricing, valuations, and market risk.</span></span> <span data-ttu-id="99c9e-129">估值结合使用专用风险建模、市场风险工具和自定义代码。</span><span class="sxs-lookup"><span data-stu-id="99c9e-129">The valuations use a combination of dedicated risk modelling, market risk tools, and custom code.</span></span> <span data-ttu-id="99c9e-130">批量执行分析，每晚、每周、每月、每季度和每年的计算变化都会产生工作负荷峰值。</span><span class="sxs-lookup"><span data-stu-id="99c9e-130">The analysis is executed in a batch with varying nightly, weekly, monthly, quarterly, and annual calculations generating spikes in workloads.</span></span>

5. <span data-ttu-id="99c9e-131">将数据与其他企业范围的风险度量指标整合为综合风险报告。</span><span class="sxs-lookup"><span data-stu-id="99c9e-131">The integration of data with other enterprise wide risk measures for consolidated risk reporting.</span></span> <span data-ttu-id="99c9e-132">在较大的组织中，较低级别的风险评估可能会转移到企业风险建模和报告工具中。</span><span class="sxs-lookup"><span data-stu-id="99c9e-132">In larger organizations lower level risk estimates may be transferred to an enterprise risk modelling and reporting tool.</span></span>

6. <span data-ttu-id="99c9e-133">必须在规定的时间间隔内按定义的格式报告结果，以满足投资者和监管要求。</span><span class="sxs-lookup"><span data-stu-id="99c9e-133">Results must be reported in a defined format at the required  interval to meet investor and regulatory requirements.</span></span>

<span data-ttu-id="99c9e-134">Microsoft 通过 [Azure 市场](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely)中的 Azure 服务和合作伙伴产品/服务的组合为上述各项考虑因素提供支持。</span><span class="sxs-lookup"><span data-stu-id="99c9e-134">Microsoft supports the above concerns through a combination of Azure services and partner offerings in the [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="99c9e-135">在本文中，我们将实际举例说明如何使用 R 执行临时试验。我们首先解释如何在单个计算机上运行试验，然后展示如何在 [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) 上运行相同的试验，最后，演示如何在我们的建模中利用外部服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-135">In this article, we show practical examples of how to perform ad-hoc experimentation using R. We begin by explaining how to run the experiment on a single machine, then show how to run the same experiment on [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely), and close by showing how to take advantage of external services in our modelling.</span></span> <span data-ttu-id="99c9e-136">有关在 Azure 上执行已定义模型的选项和注意事项，在以下关于[银行业](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)和[保险业](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)的文章中进行了说明。</span><span class="sxs-lookup"><span data-stu-id="99c9e-136">Options and considerations for the execution of defined models on Azure are described in these articles focused on [banking](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely) and [insurance](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span>

## <a name="analyst-modelling-in-r"></a><span data-ttu-id="99c9e-137">分析师通过 R 建模</span><span class="sxs-lookup"><span data-stu-id="99c9e-137">Analyst Modelling in R</span></span>

<span data-ttu-id="99c9e-138">我们首先看一下分析师在具有代表性的简化资本市场情形中如何使用 R。</span><span class="sxs-lookup"><span data-stu-id="99c9e-138">Let's start by looking at how R may be used by an analyst in a simplified, representative capital markets scenario.</span></span> <span data-ttu-id="99c9e-139">可以通过引用现有的 R 库进行计算的方式，或通过从头开始编写代码的方式来进行构建。</span><span class="sxs-lookup"><span data-stu-id="99c9e-139">You can build this either by referencing an existing R library for the calculation or by writing code from scratch.</span></span> <span data-ttu-id="99c9e-140">在我们的示例中，还必须获取外部定价数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-140">In our example, we also must fetch external pricing data.</span></span> <span data-ttu-id="99c9e-141">为使示例简明扼要，我们计算了股权远期合同的潜在风险暴露 (PFE)。</span><span class="sxs-lookup"><span data-stu-id="99c9e-141">To keep the example simple but illustrative, we calculate the potential future exposure (PFE) of an equity stock forward contract.</span></span>
<span data-ttu-id="99c9e-142">此示例避免了复杂衍生工具等复杂的定量建模技术，侧重于单一风险因素，专注于风险生命周期。</span><span class="sxs-lookup"><span data-stu-id="99c9e-142">This example avoids complex quantitative modelling techniques for instruments like complex derivatives and focuses on a single risk factor to concentrate on the risk life cycle.</span></span> <span data-ttu-id="99c9e-143">示例将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="99c9e-143">Our example does the following:</span></span>

1. <span data-ttu-id="99c9e-144">选择感兴趣的工具。</span><span class="sxs-lookup"><span data-stu-id="99c9e-144">Select an instrument of interest.</span></span>

2. <span data-ttu-id="99c9e-145">该工具的源历史价格。</span><span class="sxs-lookup"><span data-stu-id="99c9e-145">Source historic prices for the instrument.</span></span>

3. <span data-ttu-id="99c9e-146">通过简单的蒙特卡罗 (MC) 方法对股权价格建模，其中用到几何布朗运动 (GBM)：</span><span class="sxs-lookup"><span data-stu-id="99c9e-146">Model equity price by simple Monte Carlo (MC) calculation, which  uses Geometric Brownian Motion (GBM):</span></span>

    <span data-ttu-id="99c9e-147">a.</span><span class="sxs-lookup"><span data-stu-id="99c9e-147">a.</span></span>  <span data-ttu-id="99c9e-148">估计预期收益 μ (mu) 和波动率 σ (theta)。</span><span class="sxs-lookup"><span data-stu-id="99c9e-148">Estimate expected return μ (mu) and volatility σ (theta).</span></span>

    <span data-ttu-id="99c9e-149">b.</span><span class="sxs-lookup"><span data-stu-id="99c9e-149">b.</span></span>  <span data-ttu-id="99c9e-150">将模型校准为历史数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-150">Calibrate the model to historic data.</span></span>

4. <span data-ttu-id="99c9e-151">使各种路径可视化以传达结果。</span><span class="sxs-lookup"><span data-stu-id="99c9e-151">Visualize the various paths to communicate the results.</span></span>

5. <span data-ttu-id="99c9e-152">绘制最大值（0，股票价值）以证明 PFE 的含义，与风险值 (VaR) 之间的差异</span><span class="sxs-lookup"><span data-stu-id="99c9e-152">Plot max(0,Stock Value) to demonstrate the meaning of PFE, the  difference to Value at Risk (VaR)</span></span>

    <span data-ttu-id="99c9e-153">a.</span><span class="sxs-lookup"><span data-stu-id="99c9e-153">a.</span></span>  <span data-ttu-id="99c9e-154">具体公式：PFE = 股价 (T) -- 远期合同价格 K</span><span class="sxs-lookup"><span data-stu-id="99c9e-154">To clarify: PFE = Share Price (T) -- Forward Contract Price K</span></span>

6. <span data-ttu-id="99c9e-155">取 0.95 分位数来获得模拟期间每个时间步长/时间终点的 PFE 值</span><span class="sxs-lookup"><span data-stu-id="99c9e-155">Take the 0.95 Quantile to get the PFE value at each time step / end  of simulation period</span></span>

<span data-ttu-id="99c9e-156">我们将根据 MSFT 股票来计算股权远期的潜在风险暴露。</span><span class="sxs-lookup"><span data-stu-id="99c9e-156">We will calculate the potential future exposure for an equity forward based on MSFT stock.</span></span> <span data-ttu-id="99c9e-157">如上所述，要为股票价格建模，需要 MSFT 股票的历史价格，以便将模型校准为历史数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-157">As mentioned above, to model the stock prices, historic prices for the MSFT stock are required so we can calibrate the model to historical data.</span></span> <span data-ttu-id="99c9e-158">有很多方法可以获得历史股票价格。</span><span class="sxs-lookup"><span data-stu-id="99c9e-158">There are many ways to acquire historical stock prices.</span></span> <span data-ttu-id="99c9e-159">示例使用了来自外部服务提供商 [Quandl](https://www.quandl.com/) 的免费版股票价格服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-159">In our example, we use a free version of a stock price service from an external service provider, [Quandl](https://www.quandl.com/).</span></span>


> <span data-ttu-id="99c9e-160">注意：该示例使用了可用于了解概念的 [WIKI 价格数据集](https://www.quandl.com/databases/WIKIP)。</span><span class="sxs-lookup"><span data-stu-id="99c9e-160">Note: The example uses the [WIKI Prices dataset](https://www.quandl.com/databases/WIKIP) which can be used for learning concepts.</span></span> <span data-ttu-id="99c9e-161">对于美国股权的生产使用情况，Quandl 建议使用[美股盘后数据数据集](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)。</span><span class="sxs-lookup"><span data-stu-id="99c9e-161">For production usage of US based equities, Quandl recommends using the [End of Day US Stock Prices dataset](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices).</span></span>

<span data-ttu-id="99c9e-162">要处理数据并定义与股权相关的风险，需要完成以下事项：</span><span class="sxs-lookup"><span data-stu-id="99c9e-162">To process the data and define the risk associated with the equity, we need to do the following things:</span></span>

1. <span data-ttu-id="99c9e-163">从股权中检索历史数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-163">Retrieve history data from the equity.</span></span>

2. <span data-ttu-id="99c9e-164">根据历史数据确定预期收益 μ 和波动率 σ。</span><span class="sxs-lookup"><span data-stu-id="99c9e-164">Determine the expected return μ and volatility σ from the historic  data.</span></span>

3. <span data-ttu-id="99c9e-165">使用一些模拟数据对基础股票价格进行建模。</span><span class="sxs-lookup"><span data-stu-id="99c9e-165">Model the underlying stock prices using some simulation.</span></span>

4. <span data-ttu-id="99c9e-166">运行模型</span><span class="sxs-lookup"><span data-stu-id="99c9e-166">Run the model</span></span>

5. <span data-ttu-id="99c9e-167">确定股权的潜在风险暴露。</span><span class="sxs-lookup"><span data-stu-id="99c9e-167">Determine the exposure of the equity in the future.</span></span>

<span data-ttu-id="99c9e-168">首先，通过 Quandl 服务检索股票，并绘制过去 180 天的收盘价历史记录。</span><span class="sxs-lookup"><span data-stu-id="99c9e-168">We start by retrieving the stock from the Quandl service and plotting the closing price history over the last 180 days.</span></span>

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

<span data-ttu-id="99c9e-169">我们使用现有数据校准 GBM 模型。</span><span class="sxs-lookup"><span data-stu-id="99c9e-169">With the data in hand, we calibrate the GBM model.</span></span>

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

<span data-ttu-id="99c9e-170">接下来，我们为基础股票价格建模。</span><span class="sxs-lookup"><span data-stu-id="99c9e-170">Next, we model the underlying stock prices.</span></span> <span data-ttu-id="99c9e-171">可以从头开始实施离散型 GBM 流程，也可以使用提供此功能的众多 R 包之一。</span><span class="sxs-lookup"><span data-stu-id="99c9e-171">We can either implement the discrete GBM process from scratch or utilize one of many R packages which provide this functionality.</span></span> <span data-ttu-id="99c9e-172">我们使用 R 包 [sde  （随机微分方程的模拟和推断）](https://cran.r-project.org/web/packages/sde/index.html)，它提供了可解决此问题的一种方法。</span><span class="sxs-lookup"><span data-stu-id="99c9e-172">We use the R package [*sde* (Simulation and Inference for Stochastic Differential Equations)](https://cran.r-project.org/web/packages/sde/index.html) which provides a method of solving this problem.</span></span> <span data-ttu-id="99c9e-173">GBM 方法需要一组参数，这些参数要么根据历史数据进行校准，要么是既定的模拟参数。</span><span class="sxs-lookup"><span data-stu-id="99c9e-173">The GBM method requires a set of parameters which are either calibrated to historic data or given as simulation parameters.</span></span> <span data-ttu-id="99c9e-174">我们使用了历史数据，在开始模拟时提供 μ、σ 和股票价格 (P0)。</span><span class="sxs-lookup"><span data-stu-id="99c9e-174">We use the historic data, providing μ, σ and the stock prices at the beginning of the simulation (P0).</span></span>

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

<span data-ttu-id="99c9e-175">现在准备开始蒙特卡罗模拟，用于模拟某些模拟路径的潜在暴露。</span><span class="sxs-lookup"><span data-stu-id="99c9e-175">We are now ready to start a Monte Carlo simulation to model the potential exposure for some number of simulation paths.</span></span> <span data-ttu-id="99c9e-176">我们将模拟限制为 50 个蒙特卡罗路径和 256 个时间步长。</span><span class="sxs-lookup"><span data-stu-id="99c9e-176">We will limit the simulation to 50 Monte Carlo paths and 256 time steps.</span></span> <span data-ttu-id="99c9e-177">为了准备扩展模拟并利用 R 中的并行化功能，蒙特卡罗模拟循环使用 foreach 语句。</span><span class="sxs-lookup"><span data-stu-id="99c9e-177">In preparation for scaling out the simulation and taking advantage of parallelization in R, the Monte Carlo simulation loop uses a foreach statement.</span></span>

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

<span data-ttu-id="99c9e-178">现在模拟了基础 MSFT 股票的价格。</span><span class="sxs-lookup"><span data-stu-id="99c9e-178">We have now simulated the price of the underlying MSFT stock.</span></span> <span data-ttu-id="99c9e-179">为计算股权远期的风险暴露，我们减去溢价并将风险暴露限制为仅为正值。</span><span class="sxs-lookup"><span data-stu-id="99c9e-179">To calculate the exposure of the equity forward, we subtract the premium and limit the exposure to only positive values.</span></span>

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

<span data-ttu-id="99c9e-180">接下来的两张图片显示了模拟的结果。</span><span class="sxs-lookup"><span data-stu-id="99c9e-180">The next two pictures show the result of the simulation.</span></span> <span data-ttu-id="99c9e-181">第一张图显示了 50 条路径的基础股票价格的蒙特卡罗模拟。</span><span class="sxs-lookup"><span data-stu-id="99c9e-181">The first picture shows the Monte Carlo simulation of the underlying stock price for 50 paths.</span></span> <span data-ttu-id="99c9e-182">第二张图显示了在减去股权远期溢价并将风险暴露限制在正值之后，股权远期的潜在信贷风险。</span><span class="sxs-lookup"><span data-stu-id="99c9e-182">The second picture illustrates the underlying Credit Exposure for the equity forward after subtracting the premium of the equity forward and limiting the exposure to positive values.</span></span>


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| <span data-ttu-id="99c9e-183">图 1 - 50 个蒙特卡罗路径</span><span class="sxs-lookup"><span data-stu-id="99c9e-183">Figure 1 - 50 Monte Carlo Paths</span></span> | <span data-ttu-id="99c9e-184">图 2 - 股权远期的信贷风险</span><span class="sxs-lookup"><span data-stu-id="99c9e-184">Figure 2 - Credit Exposure for Equity Forward</span></span> |


<span data-ttu-id="99c9e-185">最后一步，通过以下代码计算 1 个月的 0.95 分位数 PFE。</span><span class="sxs-lookup"><span data-stu-id="99c9e-185">In the last step, the 1-Month 0.95 Quantile PFE is calculated by the following code.</span></span>

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

<span data-ttu-id="99c9e-186">图 3 MSFT 股权远期的潜在风险暴露</span><span class="sxs-lookup"><span data-stu-id="99c9e-186">Figure 3 Potential Future Exposure for MSFT Equity Forward</span></span>

## <a name="using-azure-batch-with-r"></a><span data-ttu-id="99c9e-187">将 Azure Batch 与 R 结合使用</span><span class="sxs-lookup"><span data-stu-id="99c9e-187">Using Azure Batch with R</span></span> 

<span data-ttu-id="99c9e-188">上述 R 解决方案可以与 Azure Batch 结合，并利用云进行风险计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-188">The R solution described above can be connected to Azure Batch and leverage the cloud for risk calculations.</span></span> <span data-ttu-id="99c9e-189">对于像我们这样的并行计算而言，几乎不需要额外的工作量。</span><span class="sxs-lookup"><span data-stu-id="99c9e-189">This takes little extra effort for a parallel calculation such as ours.</span></span> <span data-ttu-id="99c9e-190">[使用 Azure Batch 运行并行 R 模拟](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)教程详细介绍了如何将 R 连接到 Azure Batch。</span><span class="sxs-lookup"><span data-stu-id="99c9e-190">The tutorial, [Run a parallel R simulation with Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely), provides detailed information on connecting R to Azure Batch.</span></span> <span data-ttu-id="99c9e-191">下面介绍连接到 Azure Batch 的过程的代码和摘要，以及如何在简化的 PFE 计算中利用云的扩展。</span><span class="sxs-lookup"><span data-stu-id="99c9e-191">Below we show the code and summary of the process to connect to Azure Batch and how to take advantage of the extension to the cloud in a simplified PFE calculation.</span></span>

<span data-ttu-id="99c9e-192">此示例处理前面描述的相同模型。</span><span class="sxs-lookup"><span data-stu-id="99c9e-192">This example tackles the same model described earlier.</span></span> <span data-ttu-id="99c9e-193">正如我们之前看到的，这种计算可以在个人计算机上运行。</span><span class="sxs-lookup"><span data-stu-id="99c9e-193">As we have seen before, this calculation can run on our personal computer.</span></span> <span data-ttu-id="99c9e-194">增加蒙特卡罗路径的数量或使用更小的时间步长将导致更长的执行时间。</span><span class="sxs-lookup"><span data-stu-id="99c9e-194">Increases to the number of Monte Carlo paths or use of smaller time steps will result in much longer execution times.</span></span> <span data-ttu-id="99c9e-195">几乎所有的 R 代码都将保持不变。</span><span class="sxs-lookup"><span data-stu-id="99c9e-195">Almost all of the R code will remain unchanged.</span></span> <span data-ttu-id="99c9e-196">我们将在本节中突出显示差异。</span><span class="sxs-lookup"><span data-stu-id="99c9e-196">We will highlight the differences in this section.</span></span>

<span data-ttu-id="99c9e-197">蒙特卡罗模拟的每条路径都在 Azure 中运行。</span><span class="sxs-lookup"><span data-stu-id="99c9e-197">Each path of the Monte Carlo simulation runs in Azure.</span></span> <span data-ttu-id="99c9e-198">我们之所以能够这样做，是因为每条路径都独立于其他路径，让我们进行“令人尴尬的并行”计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-198">We can do this because each path is independent of the others, giving us an "embarrassingly parallel" calculation.</span></span>

<span data-ttu-id="99c9e-199">要使用 Azure Batch，首先定义基础集群并在代码中引用它，然后才能在计算中使用集群。</span><span class="sxs-lookup"><span data-stu-id="99c9e-199">To use Azure Batch, we define the underlying cluster and reference it in the code before the cluster can be used in the calculations.</span></span> <span data-ttu-id="99c9e-200">要运行计算，我们使用以下 cluster.json 定义：</span><span class="sxs-lookup"><span data-stu-id="99c9e-200">To run the calculations, we use the following cluster.json definition:</span></span>

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

<span data-ttu-id="99c9e-201">通过此群集定义，以下 R 代码使用群集：</span><span class="sxs-lookup"><span data-stu-id="99c9e-201">With this cluster definition, the following R code makes use of the cluster:</span></span>
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

<span data-ttu-id="99c9e-202">最后，我们更新之前的 foreach 语句以使用 doAzureParallel 包。</span><span class="sxs-lookup"><span data-stu-id="99c9e-202">Finally, we update the foreach statement from earlier to use the doAzureParallel package.</span></span> <span data-ttu-id="99c9e-203">这是一个小幅改动，添加对 sde 包的引用并将 %do% 更改为 %dopar%：</span><span class="sxs-lookup"><span data-stu-id="99c9e-203">It's a minor change, adding a reference to the sde package and changing the %do% to %dopar%:</span></span>

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

<span data-ttu-id="99c9e-204">每个蒙特卡罗模拟都作为任务提交给 Azure Batch。</span><span class="sxs-lookup"><span data-stu-id="99c9e-204">Each Monte Carlo simulation is submitted as a task to Azure Batch.</span></span> <span data-ttu-id="99c9e-205">这些任务在云中执行。</span><span class="sxs-lookup"><span data-stu-id="99c9e-205">The task executes in the cloud.</span></span> <span data-ttu-id="99c9e-206">结果在被发送回分析师工作平台之前进行合并。</span><span class="sxs-lookup"><span data-stu-id="99c9e-206">Results are merged before being sent back to the analyst workbench.</span></span> <span data-ttu-id="99c9e-207">繁重的工作和计算在云中执行，以充分利用请求的计算所需的扩展和基本基础结构。</span><span class="sxs-lookup"><span data-stu-id="99c9e-207">The heavy lifting and computations execute in the cloud to take full advantage of scaling and the underlying infrastructure required by the requested calculations.</span></span>

<span data-ttu-id="99c9e-208">计算完成后，可以通过调用以下单个指令轻松关闭其他资源：</span><span class="sxs-lookup"><span data-stu-id="99c9e-208">After the calculations have finished, the additional resources can easily be shut-down by invoking the following a single instruction:</span></span>

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a><span data-ttu-id="99c9e-209">使用 SaaS 产品/服务</span><span class="sxs-lookup"><span data-stu-id="99c9e-209">Use a SaaS offering</span></span>

<span data-ttu-id="99c9e-210">前两个示例显示了如何利用本地和云基础结构来开发适当的估值模型。</span><span class="sxs-lookup"><span data-stu-id="99c9e-210">The first two examples show how to utilize local and cloud infrastructure for developing an adequate valuation model.</span></span> <span data-ttu-id="99c9e-211">这种范式已经开始转变。</span><span class="sxs-lookup"><span data-stu-id="99c9e-211">This paradigm has begun to shift.</span></span> <span data-ttu-id="99c9e-212">与本地基础结构转变为基于云的 IaaS 和 PaaS 服务的方式相同，相关风险数据的建模正在转变为面向服务的流程。</span><span class="sxs-lookup"><span data-stu-id="99c9e-212">In the same way that on-premises infrastructure has transformed into cloud-based IaaS and PaaS services, the modelling of relevant risk figures is transforming into a service-oriented process.</span></span>
<span data-ttu-id="99c9e-213">如今的分析师面临两大挑战：</span><span class="sxs-lookup"><span data-stu-id="99c9e-213">Today's analysts face two major challenges:</span></span>

1. <span data-ttu-id="99c9e-214">监管机构要求使用不断提高的计算能力来增加建模要求。</span><span class="sxs-lookup"><span data-stu-id="99c9e-214">The regulatory requirements use increasing compute capacity to add to modeling requirements.</span></span> <span data-ttu-id="99c9e-215">监管机构更频繁地要求提供最新的风险数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-215">The regulators are asking for more frequent and up-to date risk figures.</span></span>

2.  <span data-ttu-id="99c9e-216">现有的风险基础结构随着时间的推移有机地增长，并且在以敏捷方式实现新要求和更高级的风险建模时带来了挑战。</span><span class="sxs-lookup"><span data-stu-id="99c9e-216">The existing risk infrastructure has grown organically with time and creates challenges when implementing new requirements and more advanced risk modeling in an agile manner.</span></span>

<span data-ttu-id="99c9e-217">基于云的服务可以提供所需的功能并支持风险分析。</span><span class="sxs-lookup"><span data-stu-id="99c9e-217">Cloud-based services can deliver the required functionality and support risk analysis.</span></span> <span data-ttu-id="99c9e-218">这种方法具有一些优点：</span><span class="sxs-lookup"><span data-stu-id="99c9e-218">This approach has some advantages:</span></span>

-  <span data-ttu-id="99c9e-219">监管机构要求的最常见风险计算必须由受法规约束的每个人去实现。</span><span class="sxs-lookup"><span data-stu-id="99c9e-219">The most common risk calculations required by the regulator must be implemented by everyone under the regulation.</span></span> <span data-ttu-id="99c9e-220">通过利用专业服务提供商的服务，分析师可以从符合监管机构要求的即用型风险计算中受益。</span><span class="sxs-lookup"><span data-stu-id="99c9e-220">By utilizing services from a specialized service provider, the analyst benefits from ready to use, regulator-compliant risk calculations.</span></span> <span data-ttu-id="99c9e-221">此类服务可能包括市场风险计算、交易对手风险计算、X 值调整 (XVA)，甚至交易账户根本审查 (FRTB) 计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-221">Such services may include market risk calculations, counterparty risk calculations, X-Value Adjustment (XVA), and even Fundamental Review of Trading Book (FRTB) calculations.</span></span>

- <span data-ttu-id="99c9e-222">这些服务通过 Web 服务公开其接口。</span><span class="sxs-lookup"><span data-stu-id="99c9e-222">These services expose their interfaces through web services.</span></span> <span data-ttu-id="99c9e-223">这些其他服务可以增强现有的风险基础结构。</span><span class="sxs-lookup"><span data-stu-id="99c9e-223">The existing risk infrastructure can be enhanced by these other services.</span></span>

<span data-ttu-id="99c9e-224">在示例中，我们想要为 FRTB 计算调用基于云的服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-224">In our example, we want to invoke a cloud-based service for FRTB calculations.</span></span> <span data-ttu-id="99c9e-225">其中一些可以在 [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) 上找到。</span><span class="sxs-lookup"><span data-stu-id="99c9e-225">Several of these can be found on [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="99c9e-226">在本文中，我们选择了 [Vector Risk](http://www.vectorrisk.com/) 的试用选项。</span><span class="sxs-lookup"><span data-stu-id="99c9e-226">For this article we chose a trial option from [Vector Risk](http://www.vectorrisk.com/).</span></span> <span data-ttu-id="99c9e-227">我们将继续修改系统。</span><span class="sxs-lookup"><span data-stu-id="99c9e-227">We will continue to modify our system.</span></span> <span data-ttu-id="99c9e-228">这次使用服务来计算感兴趣的风险数据。</span><span class="sxs-lookup"><span data-stu-id="99c9e-228">This time, we use a service to calculate the risk figure of interest.</span></span> <span data-ttu-id="99c9e-229">此过程包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="99c9e-229">This process consists of the following steps:</span></span>

1. <span data-ttu-id="99c9e-230">使用正确的参数调用相关的风险服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-230">Call the relevant risk service and with the right parameters.</span></span>

2. <span data-ttu-id="99c9e-231">等待服务完成计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-231">Wait until the service finishes the calculation.</span></span>

3. <span data-ttu-id="99c9e-232">检索结果并将结果合并到风险分析中。</span><span class="sxs-lookup"><span data-stu-id="99c9e-232">Retrieve and incorporate the results into the risk analysis.</span></span>

<span data-ttu-id="99c9e-233">转换为 R 代码，我们的 R 代码能够通过从准备好的输入模板定义所需的输入值得以增强。</span><span class="sxs-lookup"><span data-stu-id="99c9e-233">Translated into R code, our R code can be enhanced by the definition of the required input values from a prepared input template.</span></span>

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


<span data-ttu-id="99c9e-234">接下来，需要调用 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-234">Next, we need to call the web service.</span></span> <span data-ttu-id="99c9e-235">本例中调用 StartCreditExposure 方法来触发计算。</span><span class="sxs-lookup"><span data-stu-id="99c9e-235">In this case, we call the StartCreditExposure method to trigger the calculation.</span></span> <span data-ttu-id="99c9e-236">我们将 API 的终结点存储在名为“endpoint”  的变量中。</span><span class="sxs-lookup"><span data-stu-id="99c9e-236">We store the endpoint for the API in a variable named *endpoint*.</span></span>

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

<span data-ttu-id="99c9e-237">计算完成后，检索结果。</span><span class="sxs-lookup"><span data-stu-id="99c9e-237">Once the calculations have finished, we retrieve the results.</span></span>

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

<span data-ttu-id="99c9e-238">这使分析师可以继续处理收到的结果。</span><span class="sxs-lookup"><span data-stu-id="99c9e-238">This leaves the analyst to continue with the results received.</span></span> <span data-ttu-id="99c9e-239">从结果中提取相关风险数据并进行绘制。</span><span class="sxs-lookup"><span data-stu-id="99c9e-239">The relevant risk figures of interest are extracted from the results and plotted.</span></span>

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


<span data-ttu-id="99c9e-240">结果图如下所示：</span><span class="sxs-lookup"><span data-stu-id="99c9e-240">The resulting plots look like this:</span></span>

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| <span data-ttu-id="99c9e-241">图 4 - MSFT 股权远期的信贷风险 -</span><span class="sxs-lookup"><span data-stu-id="99c9e-241">Figure 4 - Credit Exposure for MSFT Equity Forward -</span></span> <br/><span data-ttu-id="99c9e-242">使用基于云的风险引擎进行计算</span><span class="sxs-lookup"><span data-stu-id="99c9e-242">Calculated with a Cloud Based Risk Engine</span></span> | <span data-ttu-id="99c9e-243">图 5 - MSFT 股权远期的潜在风险暴露 -</span><span class="sxs-lookup"><span data-stu-id="99c9e-243">Figure 5 - Potential Future Exposure for MSFT Equity Forward -</span></span> <br/> <span data-ttu-id="99c9e-244">使用基于云的风险引擎进行计算</span><span class="sxs-lookup"><span data-stu-id="99c9e-244">Calculated with a Cloud  Based Risk Engine</span></span> |



## <a name="next-steps"></a><span data-ttu-id="99c9e-245">后续步骤</span><span class="sxs-lookup"><span data-stu-id="99c9e-245">Next Steps</span></span>

<span data-ttu-id="99c9e-246">通过计算基础结构和基于 SaaS 的风险分析服务灵活地访问云，可以提高在资本市场和保险行业工作的风险分析师的速度和敏捷性。</span><span class="sxs-lookup"><span data-stu-id="99c9e-246">Flexible access to the cloud through compute infrastructure and SaaS-based risk analysis services can deliver improvements in speed and agility for risk analysts working in capital markets and insurance.</span></span> <span data-ttu-id="99c9e-247">本文举例说明了如何借助风险分析师熟知的工具来使用 Azure 和其他服务。</span><span class="sxs-lookup"><span data-stu-id="99c9e-247">In this article we worked through an example which illustrates how to use Azure and other services using tools risk analysts know.</span></span> <span data-ttu-id="99c9e-248">在创建和增强风险模型时，请尝试利用 Azure 的功能。</span><span class="sxs-lookup"><span data-stu-id="99c9e-248">Try taking advantage of Azure's capabilities as you create and enhance your risk models.</span></span>

### <a name="tutorials"></a><span data-ttu-id="99c9e-249">教程</span><span class="sxs-lookup"><span data-stu-id="99c9e-249">Tutorials</span></span>

- <span data-ttu-id="99c9e-250">R 开发人员：[使用 Azure Batch 运行并行 R 模拟](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span><span class="sxs-lookup"><span data-stu-id="99c9e-250">R Developers: [Run a parallel R simulation with Azure   Batch](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span></span>

- [<span data-ttu-id="99c9e-251">基本 R 命令和 RevoScaleR 函数：25 个常见示例</span><span class="sxs-lookup"><span data-stu-id="99c9e-251">Basic R commands and RevoScaleR functions: 25 common   examples</span></span>](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely)

- [<span data-ttu-id="99c9e-252">使用 RevoScaleR 可视化和分析数据</span><span class="sxs-lookup"><span data-stu-id="99c9e-252">Visualize and analyze data using   RevoScaleR</span></span>](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely)

- [<span data-ttu-id="99c9e-253">HDInsight 上的 ML 服务和开放源代码 R 功能简介</span><span class="sxs-lookup"><span data-stu-id="99c9e-253">Introduction to ML Services and open-source R capabilities on   HDInsight</span></span>](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

<span data-ttu-id="99c9e-254">_本文由 Darko Mocelj 博士和 Rupert Nicolay 共同撰写。_</span><span class="sxs-lookup"><span data-stu-id="99c9e-254">_This article was authored by Dr. Darko Mocelj and Rupert Nicolay._</span></span>