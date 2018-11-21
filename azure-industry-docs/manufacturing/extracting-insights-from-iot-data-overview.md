---
title: 概述：从 IoT 数据中提取可操作见解
description: 使用 Azure 服务从 IoT 数据中提取见解。 概述了本解决方案指南。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 09/26/2018
ms.openlocfilehash: 9528983a06602bd469a4d651bda8478aa758152b
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654114"
---
# <a name="extracting-actionable-insights-from-iot-data"></a><span data-ttu-id="e6e30-104">从 IoT 数据中提取可操作见解</span><span class="sxs-lookup"><span data-stu-id="e6e30-104">Extracting actionable insights from IoT data</span></span>

<span data-ttu-id="e6e30-105">制造商正考虑建造着眼于使用数字工具改进生产和工艺的现代化工厂车间。</span><span class="sxs-lookup"><span data-stu-id="e6e30-105">Manufacturers are looking at a modern factory floor with an eye to improving production and processes using digital tools.</span></span> <span data-ttu-id="e6e30-106">最大共同点是物联网 (IoT)。</span><span class="sxs-lookup"><span data-stu-id="e6e30-106">And the greatest common denominator is the Internet of Things (IoT).</span></span> <span data-ttu-id="e6e30-107">机器发出数据到现在已有一段时间了。</span><span class="sxs-lookup"><span data-stu-id="e6e30-107">Machines have been emitting data for some time now.</span></span> <span data-ttu-id="e6e30-108">毫无疑问，新机器将会提供更多数据。</span><span class="sxs-lookup"><span data-stu-id="e6e30-108">New machines will doubtlessly be providing even more data.</span></span>
<span data-ttu-id="e6e30-109">不过，获取数据只是迈出了第一步。</span><span class="sxs-lookup"><span data-stu-id="e6e30-109">But having the data is just the first step.</span></span> <span data-ttu-id="e6e30-110">本解决方案指南介绍了如何根据数据执行预测性维护等操作。</span><span class="sxs-lookup"><span data-stu-id="e6e30-110">Our solution guide provides a path to using the data for actions such as predictive maintenance.</span></span>

<span data-ttu-id="e6e30-111">简而言之，解决方案以下列三个主要步骤为重点：内容（数据）、见解（通过高级分析）和操作。</span><span class="sxs-lookup"><span data-stu-id="e6e30-111">In brief, the solution focuses on three major steps: things (data), insights (via advanced analytics) and actions.</span></span>

![从内容到见解再到操作。](assets/extracting-insights-from-iot/things-insights-actions.png)

<span data-ttu-id="e6e30-113">获取数据是很好，但数据只是原材料。</span><span class="sxs-lookup"><span data-stu-id="e6e30-113">Having data is great, but data is just raw materials.</span></span> <span data-ttu-id="e6e30-114">通过分析得出见解可指导你如何操作。</span><span class="sxs-lookup"><span data-stu-id="e6e30-114">Insights through analysis guides your actions.</span></span>

![Lambda 架构](assets/extracting-insights-from-iot/lambda-architecture.png)

<span data-ttu-id="e6e30-116">此体系结构非常灵活，可便于以高吞吐量引入和分析数据。</span><span class="sxs-lookup"><span data-stu-id="e6e30-116">The architecture is flexible and lets you ingest and analyze data at high throughput.</span></span> <span data-ttu-id="e6e30-117">随着你的需求增长，还可以纵向扩展和缩减组件，从而满足需求或节省成本。</span><span class="sxs-lookup"><span data-stu-id="e6e30-117">As your needs grow, the components can also scale up and down to meet demand or save cost.</span></span> <span data-ttu-id="e6e30-118">本指南还建议了如何实现体系结构；例如，使用 Azure 事件中心引入数据，以及对分析客户端使用 Power BI。</span><span class="sxs-lookup"><span data-stu-id="e6e30-118">The guide also shows you recommendations for implementing the architecture—for example, using Azure Event Hubs to ingest, and Power BI for an analytics client.</span></span>

[<span data-ttu-id="e6e30-119">从 IoT 数据中提取可操作见解</span><span class="sxs-lookup"><span data-stu-id="e6e30-119">Extracting actionable insights from IoT data</span></span>](./extracting-insights-from-iot-data.md)
