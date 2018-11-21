---
title: 视觉搜索概述
author: scseely
ms.author: scseely, mazoroto
ms.date: 07/16/2018
ms.topic: article
ms.service: industry
description: 本文介绍了将电子商务基础结构从本地迁移到 Azure 的阶段。
ms.openlocfilehash: 0c80e3068a1b23bf12d2468489fdd0b67c660dfa
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654204"
---
# <a name="visual-search-overview"></a><span data-ttu-id="b0330-103">视觉搜索概述</span><span class="sxs-lookup"><span data-stu-id="b0330-103">Visual Search Overview</span></span>

## <a name="executive-summary"></a><span data-ttu-id="b0330-104">执行摘要</span><span class="sxs-lookup"><span data-stu-id="b0330-104">Executive Summary</span></span>

<span data-ttu-id="b0330-105">正如我们今天所看到的，人工智能提供了改变零售业的潜力。</span><span class="sxs-lookup"><span data-stu-id="b0330-105">Artificial Intelligence offers the potential to transform retailing as we know it today.</span></span> <span data-ttu-id="b0330-106">有理由相信零售商将开发由 AI 提供支持的客户体验体系结构。</span><span class="sxs-lookup"><span data-stu-id="b0330-106">It is reasonable to believe that retailers will develop a customer experience architecture supported by AI.</span></span> <span data-ttu-id="b0330-107">我们的某些期望是，通过 AI 增强的平台会因超个性化而带来收入增长。</span><span class="sxs-lookup"><span data-stu-id="b0330-107">Some expectations are that a platform enhanced with AI will provide a revenue bump due to hyper personalization.</span></span> <span data-ttu-id="b0330-108">数字商务继续提升客户期望、偏好和行为。</span><span class="sxs-lookup"><span data-stu-id="b0330-108">Digital commerce continues to heighten customer expectations, preferences and behavior.</span></span> <span data-ttu-id="b0330-109">诸如实时参与、相关推荐和超个性化等需求只需点击一下按钮即可提高速度和便利性。</span><span class="sxs-lookup"><span data-stu-id="b0330-109">Demands such as real-time engagement, relevant recommendations and hyper-personalization are driving speed and convenience at a click of a button.</span></span> <span data-ttu-id="b0330-110">通过自然语音、视觉等在应用程序中实现智能可实现零售方式的改进，从而在改变客户购物方式的同时提高价值。</span><span class="sxs-lookup"><span data-stu-id="b0330-110">Enabling intelligence in applications through natural speech, vision, etc. enables improvements in retail that will increase value while disrupting how customers shop.</span></span>

<span data-ttu-id="b0330-111">本文档重点介绍视觉搜索的 AI 概念，并提供一些关于其实现的关键考虑因素。</span><span class="sxs-lookup"><span data-stu-id="b0330-111">This document focuses on the AI  concept of **visual search** and offers a few key considerations on its implementation.</span></span> <span data-ttu-id="b0330-112">它提供了一个工作流示例，并将其阶段映射到相关 Azure 技术。</span><span class="sxs-lookup"><span data-stu-id="b0330-112">It provides a workflow example and maps its stages to the relevant Azure technologies.</span></span> <span data-ttu-id="b0330-113">该概念基于客户能够利用他们的移动设备拍摄的图像或 Internet 上的图像，根据体验的意向进行相关和/或类似项目的搜索。</span><span class="sxs-lookup"><span data-stu-id="b0330-113">The concept is based on a customer being able to leverage an image taken with their mobile device or located on the internet to conduct a search of relevant and/or like items, depending upon the intention of the experience.</span></span> <span data-ttu-id="b0330-114">因此，视觉搜索提高了从短信输入到具有多个元数据点的图像的速度，从而快速显示存在的可用项目。</span><span class="sxs-lookup"><span data-stu-id="b0330-114">Thus, visual search improves speed from texted entry to an image with multiple meta-data points to quickly surface applicable items available.</span></span>

## <a name="visual-search-engines"></a><span data-ttu-id="b0330-115">视觉搜索引擎</span><span class="sxs-lookup"><span data-stu-id="b0330-115">Visual Search Engines</span></span>

<span data-ttu-id="b0330-116">视觉搜索引擎使用图像作为输入来检索信息，但通常但不排除将图像作为输出。</span><span class="sxs-lookup"><span data-stu-id="b0330-116">Visual search engines retrieve information using images as input and often—but not exclusively—as output too.</span></span>

<span data-ttu-id="b0330-117">引擎在零售业中变得越来越普遍，原因很简单：</span><span class="sxs-lookup"><span data-stu-id="b0330-117">Engines are becoming more and more common in the retail industry, and for very good reasons:</span></span>

- <span data-ttu-id="b0330-118">根据 2017 年发布的 [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) 报告，大约 75% 的 Internet 用户在购买前搜索产品的图片或视频。</span><span class="sxs-lookup"><span data-stu-id="b0330-118">Around 75% of internet users search for pictures or videos of a product before making a purchase, according to an [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) report published in 2017.</span></span>
- <span data-ttu-id="b0330-119">根据 [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf)（视觉搜索公司）2015 年报告，74% 的消费者发现文本搜索效率低下。</span><span class="sxs-lookup"><span data-stu-id="b0330-119">74% of consumers also find text searches inefficient, according to [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf) (a visual search company) 2015 report.</span></span>

<span data-ttu-id="b0330-120">因此，根据 [Markets &amp;Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp) 的研究，到 2019 年，图像识别市场的价值将超过 250 亿美元。</span><span class="sxs-lookup"><span data-stu-id="b0330-120">Therefore, the image recognition market will be worth more than $25 billion by 2019, according to research by [Markets &amp; Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp).</span></span>

<span data-ttu-id="b0330-121">该技术已经与主要的电子商务品牌合作，这些品牌也为其发展做出了重大贡献。</span><span class="sxs-lookup"><span data-stu-id="b0330-121">The technology has already taken hold with major e-commerce brands, who have also contributed significantly to its development.</span></span> <span data-ttu-id="b0330-122">最突出的早期采用者可能是：</span><span class="sxs-lookup"><span data-stu-id="b0330-122">The most prominent early adopters are probably:</span></span>

- <span data-ttu-id="b0330-123">eBay 在其应用程序中使用他们的图像搜索和“在 eBay 上查找它”工具（目前这只是一种移动体验）。</span><span class="sxs-lookup"><span data-stu-id="b0330-123">eBay with their Image Search and "Find It on eBay" tools in their app (this is currently only a mobile experience).</span></span>
- <span data-ttu-id="b0330-124">Pinterest 具有其 Lens 视觉发现工具。</span><span class="sxs-lookup"><span data-stu-id="b0330-124">Pinterest with their Lens visual discovery tool.</span></span>
- <span data-ttu-id="b0330-125">Microsoft 必应视觉搜索。</span><span class="sxs-lookup"><span data-stu-id="b0330-125">Microsoft with Bing Visual Search.</span></span>

## <a name="adopt-and-adapt"></a><span data-ttu-id="b0330-126">采用和调整</span><span class="sxs-lookup"><span data-stu-id="b0330-126">Adopt and Adapt</span></span>

<span data-ttu-id="b0330-127">幸运的是，你无需大量的计算能力即可从视觉搜索中获利。</span><span class="sxs-lookup"><span data-stu-id="b0330-127">Fortunately, you don't need vast amounts of computing power to profit from visual search.</span></span> <span data-ttu-id="b0330-128">任何拥有图像目录的企业都可以利用其 Azure 服务中内置的 Microsoft 专业知识。</span><span class="sxs-lookup"><span data-stu-id="b0330-128">Any business with an image catalog can take advantage of Microsoft's AI expertise built into its Azure services.</span></span>

<span data-ttu-id="b0330-129">[必应视觉搜索](https://azure.microsoft.com/en-us/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API 提供了一种从图像提取上下文信息的方法，可用于识别，例如识别家居用品、时装、多种产品等。</span><span class="sxs-lookup"><span data-stu-id="b0330-129">[Bing Visual Search](https://azure.microsoft.com/en-us/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API provides a way to extract context information from images, identifying—for instance—home furnishings, fashion, several kinds of products, etc.</span></span>

<span data-ttu-id="b0330-130">它还将从自己的目录、具有相关购物来源的产品、相关搜索中返回视觉上类似的图像。</span><span class="sxs-lookup"><span data-stu-id="b0330-130">It will also return visually similar images out of its own catalog, products with relative shopping sources, related searches.</span></span> <span data-ttu-id="b0330-131">虽然有趣，但如果你的公司不是其中一个来源，该用途将是有限的。</span><span class="sxs-lookup"><span data-stu-id="b0330-131">While interesting, this will be of limited use if your company is not one of those sources.</span></span>

<span data-ttu-id="b0330-132">此外，必应还提供：</span><span class="sxs-lookup"><span data-stu-id="b0330-132">Bing will also provide:</span></span>

- <span data-ttu-id="b0330-133">允许浏览图像中找到的对象或概念的标记。</span><span class="sxs-lookup"><span data-stu-id="b0330-133">Tags that allow you to explore objects or concepts found in the image.</span></span>
- <span data-ttu-id="b0330-134">图像中感兴趣区域的边框（例如衣服、家具）。</span><span class="sxs-lookup"><span data-stu-id="b0330-134">Bounding boxes for regions of interest in the image (e.g. clothing, furniture items).</span></span>

<span data-ttu-id="b0330-135">你可以使用该信息将搜索空间（和时间）显著缩减到公司的产品目录中，从而将其限制为感兴趣的区域和类别中类似这些目录的对象。</span><span class="sxs-lookup"><span data-stu-id="b0330-135">You can take that information to reduce the search space (and time) into a company's product catalog significantly, restricting it to objects like those in the region and category of interest.</span></span>

## <a name="implement-your-own"></a><span data-ttu-id="b0330-136">实现你自己的视觉搜索</span><span class="sxs-lookup"><span data-stu-id="b0330-136">Implement Your Own</span></span>

<span data-ttu-id="b0330-137">实现视觉搜索时需要考虑几个关键组件：</span><span class="sxs-lookup"><span data-stu-id="b0330-137">There are a few key components to consider when implementing visual search:</span></span>

- <span data-ttu-id="b0330-138">引入并筛选图像</span><span class="sxs-lookup"><span data-stu-id="b0330-138">Ingesting and filtering images</span></span>
- <span data-ttu-id="b0330-139">存储和检索技术</span><span class="sxs-lookup"><span data-stu-id="b0330-139">Storage and retrieval techniques</span></span>
- <span data-ttu-id="b0330-140">特征化、编码或“哈希”</span><span class="sxs-lookup"><span data-stu-id="b0330-140">Featurization, encoding or "hashing"</span></span>
- <span data-ttu-id="b0330-141">相似度量值或距离和排名</span><span class="sxs-lookup"><span data-stu-id="b0330-141">Similarity measures or distances and ranking</span></span>

 ![](./assets/visual-search-use-case-overview/visual-search-pipeline.png)

<span data-ttu-id="b0330-142">图 1：视觉搜索管道示例</span><span class="sxs-lookup"><span data-stu-id="b0330-142">*Figure 1: Example of Visual Search Pipeline*</span></span>

### <a name="sourcing-the-pictures"></a><span data-ttu-id="b0330-143">溯源图片</span><span class="sxs-lookup"><span data-stu-id="b0330-143">Sourcing the Pictures</span></span>

<span data-ttu-id="b0330-144">如果你没有图片目录，则需要在公开可用的数据集上定型算法，例如 fashion [MNIST](https://www.kaggle.com/zalando-research/fashionmnist)、deep [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) 等。</span><span class="sxs-lookup"><span data-stu-id="b0330-144">If you do not own a picture catalog, you may need to train the algorithms on openly available data sets, such as fashion [MNIST](https://www.kaggle.com/zalando-research/fashionmnist), deep [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) and similar.</span></span> <span data-ttu-id="b0330-145">它们包含几类产品目录，通常用于对图像分类和搜索算法进行基准测试。</span><span class="sxs-lookup"><span data-stu-id="b0330-145">They contain several categories of products and are commonly used to benchmark image categorization and search algorithms.</span></span>

 ![](./assets/visual-search-use-case-overview/deep-fashion-dataset.png)

<span data-ttu-id="b0330-146">图 2：Deep Fashion 数据集示例</span><span class="sxs-lookup"><span data-stu-id="b0330-146">*Figure 2: An Example from The Deep Fashion Dataset*</span></span>

### <a name="filtering-the-images"></a><span data-ttu-id="b0330-147">筛选图像</span><span class="sxs-lookup"><span data-stu-id="b0330-147">Filtering the Images</span></span>

<span data-ttu-id="b0330-148">大多数基准数据集（例如前面提到的那些）已经过预处理。</span><span class="sxs-lookup"><span data-stu-id="b0330-148">Most benchmark datasets - such as those mentioned before - have already been pre-processed.</span></span>

<span data-ttu-id="b0330-149">如果你正在生成自己的图像，则至少你会希望图像都具有相同的大小，主要取决于针对输入所定型的模型。</span><span class="sxs-lookup"><span data-stu-id="b0330-149">If you are building your own, at a minimum you will want the images to all have the same size, mostly dictated by the input that your model is trained for.</span></span>

<span data-ttu-id="b0330-150">在许多情况下，最好还要标准化图像的亮度。</span><span class="sxs-lookup"><span data-stu-id="b0330-150">In many cases, it is best also to normalize the luminosity of the images.</span></span> <span data-ttu-id="b0330-151">根据搜索的详细程度，颜色也可能是冗余信息，因此减少为黑白二色将有助于缩短处理时间。</span><span class="sxs-lookup"><span data-stu-id="b0330-151">Depending on the detail level of your search, color may also be redundant information, so reducing to black and white will help with processing times.</span></span>

<span data-ttu-id="b0330-152">最后但同样重要的是，图像数据集应该在它所代表的不同类之间保持平衡。</span><span class="sxs-lookup"><span data-stu-id="b0330-152">Last but not least, the image dataset should be balanced across the different classes it represents.</span></span>

### <a name="image-database"></a><span data-ttu-id="b0330-153">图像数据库</span><span class="sxs-lookup"><span data-stu-id="b0330-153">Image Database</span></span>

<span data-ttu-id="b0330-154">数据层是体系结构的特别精细的组件。</span><span class="sxs-lookup"><span data-stu-id="b0330-154">The data layer is a particularly delicate component of your architecture.</span></span> <span data-ttu-id="b0330-155">其中包含：</span><span class="sxs-lookup"><span data-stu-id="b0330-155">It will contain:</span></span>

- <span data-ttu-id="b0330-156">映像</span><span class="sxs-lookup"><span data-stu-id="b0330-156">Images</span></span>
- <span data-ttu-id="b0330-157">有关图像的任何元数据（大小、标记、产品 SKU、说明）</span><span class="sxs-lookup"><span data-stu-id="b0330-157">Any metadata about the images (size, tags, product SKUs, description)</span></span>
- <span data-ttu-id="b0330-158">机器学习模型生成的数据（例如每个图像 4096 个元素的数值矢量）</span><span class="sxs-lookup"><span data-stu-id="b0330-158">Data generated by the machine learning model (for instance a 4096-element numerical vector  per image)</span></span>

<span data-ttu-id="b0330-159">当从不同源检索图像或使用多个机器学习模型以获得最佳性能时，数据结构将发生变化。</span><span class="sxs-lookup"><span data-stu-id="b0330-159">As you retrieve images from different sources or use several machine learning models for optimal performance, the structure of the data will change.</span></span> <span data-ttu-id="b0330-160">因此，选择能够处理半结构化数据而不是固定架构的技术或组合非常重要。</span><span class="sxs-lookup"><span data-stu-id="b0330-160">It is therefore important to choose a technology or combination that can deal with semi-structured data and no fixed schema.</span></span>

<span data-ttu-id="b0330-161">你还需要最少数量的有用数据点（例如图像标识符或密钥、产品 SKU、说明、标记字段）。</span><span class="sxs-lookup"><span data-stu-id="b0330-161">You may also want to require a minimum number of useful data points (e.g. an image identifier or key, a product sku, a description, a tag field).</span></span>

<span data-ttu-id="b0330-162">[Azure CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) 为在其上生成的应用程序提供了所需的灵活性和各种访问机制（这对目录搜索有利）。</span><span class="sxs-lookup"><span data-stu-id="b0330-162">[Azure CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) offers the required flexibility and a variety of access mechanisms for applications built on top of it (which will help with your catalog search).</span></span> <span data-ttu-id="b0330-163">但是，必须谨慎操作才能获得最佳性价比。</span><span class="sxs-lookup"><span data-stu-id="b0330-163">However, one has to be careful to drive the best price/performance.</span></span> <span data-ttu-id="b0330-164">CosmosDB 允许存储文档附件，但每个帐户有一个总限制，因而成本可能颇为高昂。</span><span class="sxs-lookup"><span data-stu-id="b0330-164">CosmosDB allows document attachments to be stored, but there is a total limit per account and it may be a costly proposition.</span></span> <span data-ttu-id="b0330-165">通常做法是将实际图像文件存储在 blob 中，并在数据库中插入指向它们的链接。</span><span class="sxs-lookup"><span data-stu-id="b0330-165">It is common practice to store the actual image files in blobs and insert a link to them in the database.</span></span> <span data-ttu-id="b0330-166">对于 CosmosDB，这意味着创建一个文档，其中包含与该图像关联的目录属性（sku、标记等）和包含图像文件 URL 的附件（例如，在 Azure blob 存储、OneDrive 上）。</span><span class="sxs-lookup"><span data-stu-id="b0330-166">In the case of CosmosDB this implies creating a document that contains the catalog properties associated to that image (sku, tag etc.) and an attachment that contains the URL of the image file (e.g. on Azure blob storage, OneDrive etc).</span></span>

 ![](./assets/visual-search-use-case-overview/cosmosdb-data-model.png)

<span data-ttu-id="b0330-167">图 3：CosmosDB 分层资源模型</span><span class="sxs-lookup"><span data-stu-id="b0330-167">*Figure 3: CosmosDB Hierarchical Resource Model*</span></span>

<span data-ttu-id="b0330-168">如果你计划利用 Cosmos DB 的全局分发，请注意它将复制文档和附件，但不会复制链接的文件。</span><span class="sxs-lookup"><span data-stu-id="b0330-168">If you plan to take advantage of the global distribution of Cosmos DB, note that it will replicate the documents and attachments, but not the linked files.</span></span> <span data-ttu-id="b0330-169">你需要考虑这些文件的内容分发网络。</span><span class="sxs-lookup"><span data-stu-id="b0330-169">You may want to consider a content distribution network for those.</span></span>

<span data-ttu-id="b0330-170">其他适用的技术是 Azure SQL 数据库（如果可以使用固定架构）和 blob 的组合，甚至可以是成本较低且可快速存储和检索的 Azure 表和 blob。</span><span class="sxs-lookup"><span data-stu-id="b0330-170">Other applicable technologies are a combination of Azure SQL Database (if fixed schema is acceptable) and blobs, or even Azure Tables and blobs for inexpensive and fast storage and retrieval.</span></span>

### <a name="feature-extraction-amp-encoding"></a><span data-ttu-id="b0330-171">功能提取 &amp; 编码</span><span class="sxs-lookup"><span data-stu-id="b0330-171">Feature Extraction &amp; Encoding</span></span>

<span data-ttu-id="b0330-172">编码过程从数据库的图片中提取显著特征，并将其中的每一个映射到稀疏“特征”向量（具有许多零的向量），它可具有数千个组件。</span><span class="sxs-lookup"><span data-stu-id="b0330-172">The encoding process extracts salient features from pictures in the database and maps each of them to a sparse "feature" vector (a vector with many zeros) that can have thousands of components.</span></span> <span data-ttu-id="b0330-173">该向量是描述图像特征的数量表征（例如边缘、形状），类似于代码。</span><span class="sxs-lookup"><span data-stu-id="b0330-173">This vector is a numerical representation of the features (e.g. edges, shapes) that characterize the picture – akin to a code.</span></span>

<span data-ttu-id="b0330-174">特征提取技术通常使用迁移学习机制。</span><span class="sxs-lookup"><span data-stu-id="b0330-174">Feature extraction techniques typically use _transfer learning mechanisms_.</span></span> <span data-ttu-id="b0330-175">当选择预定型的神经网络，通过它运行每个图像并将生成的特征向量存储回图像数据库中时，会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="b0330-175">This occurs when you select a pre-trained neural network, run each image through it and store the feature vector  produced back in your image database.</span></span> <span data-ttu-id="b0330-176">通过这种方式，你可以从定型网络的人那里“迁移”学习。</span><span class="sxs-lookup"><span data-stu-id="b0330-176">In that way, you "transfer" the learning from whoever trained the network.</span></span> <span data-ttu-id="b0330-177">Microsoft 已开发并发布了几个预定型网络，并已广泛用于图像识别任务，例如 [ResNet50](https://www.kaggle.com/keras/resnet50)。</span><span class="sxs-lookup"><span data-stu-id="b0330-177">Microsoft has developed and published several pre-trained networks that have been widely used for image recognition tasks, such as [ResNet50](https://www.kaggle.com/keras/resnet50).</span></span>

<span data-ttu-id="b0330-178">依赖于神经网络，特征向量均或多或少有些长且稀疏，因此内存和存储要求将变化。</span><span class="sxs-lookup"><span data-stu-id="b0330-178">Depending on the neural network, the feature vector will be more or less long and sparse, hence the memory and storage requirements will vary.</span></span>

<span data-ttu-id="b0330-179">此外，你可能会发现不同的网络适用于不同的类别，因此视觉搜索的实现可能实际上生成不同大小的特征向量。</span><span class="sxs-lookup"><span data-stu-id="b0330-179">Also, you may find that different networks are applicable to different categories, hence an implementation of visual search may actually generate feature vectors of varying size.</span></span>

<span data-ttu-id="b0330-180">预定型的神经网络相对易于使用，但可能不如针对图像目录定型的自定义模型效率高。</span><span class="sxs-lookup"><span data-stu-id="b0330-180">Pre-trained neural networks are relatively easy to use but may not be as efficient a custom model trained on your image catalog.</span></span> <span data-ttu-id="b0330-181">这些预定型的网络通常设计用于基准数据集的分类，而不是搜索特定的图像集合。</span><span class="sxs-lookup"><span data-stu-id="b0330-181">Those pre-trained networks are typically designed for classification of benchmark datasets rather than search on your specific collection of images.</span></span>

<span data-ttu-id="b0330-182">你可能希望修改和重新定型它们，以便它们生成类别预测和密集（即较小的非稀疏）向量，这对于限制搜索空间、降低内存和存储要求非常有用。</span><span class="sxs-lookup"><span data-stu-id="b0330-182">You may want to modify and retrain them so they produce both a category prediction and a dense (i.e. smaller, not sparse) vector, which will be very useful to restrict the search space, reduce memory and storage requirements.</span></span> <span data-ttu-id="b0330-183">可以使用二进制向量，并且通常将其称为“[语义哈希](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)”，这是从文档编码和检索技术派生的术语。</span><span class="sxs-lookup"><span data-stu-id="b0330-183">Binary vectors can be used and are often referred to as " [semantic hash](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)" – a term derived from document encoding and retrieval techniques.</span></span> <span data-ttu-id="b0330-184">二进制表示形式简化了进一步计算。</span><span class="sxs-lookup"><span data-stu-id="b0330-184">The binary representation simplifies further calculations.</span></span>

 ![](./assets/visual-search-use-case-overview/resnet-modifications.png)

<span data-ttu-id="b0330-185">图4：修改 ResNet 以用于视觉搜索 - F. Yang 等，2017 年</span><span class="sxs-lookup"><span data-stu-id="b0330-185">*Figure 4: Modifications to ResNet for Visual Search – F. Yang et al., 2017*</span></span>

<span data-ttu-id="b0330-186">无论选择预定型模型还是开发自己的模型，你仍然需要决定在何处运行模型本身的特征化和/或定型。</span><span class="sxs-lookup"><span data-stu-id="b0330-186">Whether you choose pre-trained models or to develop your own, you will still need to decide where to run the featurization and/or training of the model itself.</span></span>

<span data-ttu-id="b0330-187">Azure 提供了多种选择：VM、Azure Batch、[Batch AI](https://azure.microsoft.com/en-us/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet)Databricks 群集。</span><span class="sxs-lookup"><span data-stu-id="b0330-187">Azure offers several options: VMs, Azure Batch, [Batch AI](https://azure.microsoft.com/en-us/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet), Databricks clusters.</span></span> <span data-ttu-id="b0330-188">但是，在所有情况下，使用 GPU 都可以获得最佳性价比。</span><span class="sxs-lookup"><span data-stu-id="b0330-188">In all cases, however, the best price/performance is given by the use of GPUs.</span></span>

<span data-ttu-id="b0330-189">Microsoft 最近还宣布推出可用于快速计算的 FPGA，其成本仅为 GPU 成本的一小部分（项目 [Brainwave](https://www.microsoft.com/en-us/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet)）。</span><span class="sxs-lookup"><span data-stu-id="b0330-189">Microsoft has also recently announced the availability of FPGAs for fast computation at a fraction of the GPU cost (project [Brainwave](https://www.microsoft.com/en-us/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet)).</span></span> <span data-ttu-id="b0330-190">但是，在撰写本文时，此产品仅限于某些网络体系结构，因此你需要详细评估其性能。</span><span class="sxs-lookup"><span data-stu-id="b0330-190">However, at the time of writing, this offering is limited to certain network architectures, so you will need to evaluate their performance closely.</span></span>

### <a name="similarity-measure-or-distance"></a><span data-ttu-id="b0330-191">相似度量值或距离</span><span class="sxs-lookup"><span data-stu-id="b0330-191">Similarity Measure or Distance</span></span>

<span data-ttu-id="b0330-192">当在特征向量空间中表示图像时，找到相似性成为定义此类空间中点之间距离度量值的问题。</span><span class="sxs-lookup"><span data-stu-id="b0330-192">When the images are represented in the feature vector space, finding similarities becomes a question of defining a distance measure between points in such space.</span></span> <span data-ttu-id="b0330-193">定义距离后，你可以计算相似图像的群集和/或定义相似矩阵。</span><span class="sxs-lookup"><span data-stu-id="b0330-193">Once a distance is defined, you can compute clusters of similar images and/or define similarity matrices.</span></span> <span data-ttu-id="b0330-194">所选的距离度量值不同，结果可能会有所不同。</span><span class="sxs-lookup"><span data-stu-id="b0330-194">Depending on the distance metric selected, the results may vary.</span></span> <span data-ttu-id="b0330-195">例如，针对实数向量的最常见的欧几里德距离度量值很容易理解：它捕获距离的大小。</span><span class="sxs-lookup"><span data-stu-id="b0330-195">The most common Euclidean distance measure over real-number vectors, for instance, is easy to understand: it captures the magnitude of the distance.</span></span> <span data-ttu-id="b0330-196">然而，就计算而言，它是相当低效的。</span><span class="sxs-lookup"><span data-stu-id="b0330-196">However, it is rather inefficient in terms of computation.</span></span>

<span data-ttu-id="b0330-197">[余弦](https://en.wikipedia.org/wiki/Cosine_similarity)距离通常用于捕捉向量的方向，而不是其大小。</span><span class="sxs-lookup"><span data-stu-id="b0330-197">[Cosine](https://en.wikipedia.org/wiki/Cosine_similarity) distance is often used to capture the orientation of the vector, rather than its magnitude.</span></span>

<span data-ttu-id="b0330-198">诸如[汉明](https://en.wikipedia.org/wiki/Hamming_distance)距离二进制表示形式的替代方案就降低了准确度以提高效率和速度。</span><span class="sxs-lookup"><span data-stu-id="b0330-198">Alternatives such as [Hamming](https://en.wikipedia.org/wiki/Hamming_distance) distance over binary representations trade some accuracy for efficiency and speed.</span></span>

<span data-ttu-id="b0330-199">向量大小和距离度量值的组合将决定搜索的计算密集程度和内存密集程度。</span><span class="sxs-lookup"><span data-stu-id="b0330-199">The combination of vector size and distance measure will determine how computationally intensive and memory intensive the search will be.</span></span>

### <a name="search-amp-ranking"></a><span data-ttu-id="b0330-200">搜索 &amp; 排名</span><span class="sxs-lookup"><span data-stu-id="b0330-200">Search &amp; Ranking</span></span>

<span data-ttu-id="b0330-201">定义相似性后，我们需要设计一种有效方法来检索最接近的 N 个项目以确定作为输入传递的那个项目，然后返回标识符列表。</span><span class="sxs-lookup"><span data-stu-id="b0330-201">Once similarity is defined, we need to devise an efficient method to retrieve the closest N items to the one passed as input, then return a list of identifiers.</span></span> <span data-ttu-id="b0330-202">这也称为“图像排名”。</span><span class="sxs-lookup"><span data-stu-id="b0330-202">This is also known as "image ranking".</span></span> <span data-ttu-id="b0330-203">在大型数据集上，禁止计算每个距离的时间，因此我们使用近似最近邻算法。</span><span class="sxs-lookup"><span data-stu-id="b0330-203">On a large data set, the time to compute every distance is prohibitive, so we use approximate nearest-neighbor algorithms.</span></span> <span data-ttu-id="b0330-204">针对该算法存在几个开放源代码库，因此你不必从头开始编写代码。</span><span class="sxs-lookup"><span data-stu-id="b0330-204">Several open source libraries exist for those, so you won't have to code them from scratch.</span></span>

<span data-ttu-id="b0330-205">最后，内存和计算要求将决定定型模型部署技术选择以及高可用性。</span><span class="sxs-lookup"><span data-stu-id="b0330-205">Finally, memory and computation requirements will determine the choice of deployment technology for the trained model, as well high availability.</span></span> <span data-ttu-id="b0330-206">通常情况下，搜索空间将被分区，并且排名算法的若干实例将并行运行。</span><span class="sxs-lookup"><span data-stu-id="b0330-206">Typically, the search space will be partitioned, and several instances of the ranking algorithm will run in parallel.</span></span> <span data-ttu-id="b0330-207">允许可伸缩性和可用性的一个选项是 [Azure Kubernetes](https://azure.microsoft.com/en-us/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) 集群。</span><span class="sxs-lookup"><span data-stu-id="b0330-207">One option that allows for scalability and availability is [Azure Kubernetes](https://azure.microsoft.com/en-us/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) clusters.</span></span> <span data-ttu-id="b0330-208">在这种情况下，建议跨多个容器（每个处理一个搜索空间的分区）和数个节点（针对高可用性）部署排名模型。</span><span class="sxs-lookup"><span data-stu-id="b0330-208">In that case it is advisable to deploy the ranking model across several containers (handling a partition of the search space each) and several nodes (for high availability).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0330-209">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b0330-209">Next steps</span></span>

<span data-ttu-id="b0330-210">实现视觉搜索的过程不一定非常复杂。</span><span class="sxs-lookup"><span data-stu-id="b0330-210">Implementing visual search need not be complex.</span></span> <span data-ttu-id="b0330-211">你可以使用必应或通过 Azure 服务生成自己的服务，同时借鉴 Microsoft AI 研究和工具。</span><span class="sxs-lookup"><span data-stu-id="b0330-211">You can use Bing or build your own with Azure services, while benefiting from Microsoft's AI research and tools.</span></span>

### <a name="trial"></a><span data-ttu-id="b0330-212">试用</span><span class="sxs-lookup"><span data-stu-id="b0330-212">Trial</span></span>

- <span data-ttu-id="b0330-213">试用[视觉搜索 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)</span><span class="sxs-lookup"><span data-stu-id="b0330-213">Try out the [Visual Search API Testing Console](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)</span></span>

### <a name="develop"></a><span data-ttu-id="b0330-214">开发</span><span class="sxs-lookup"><span data-stu-id="b0330-214">Develop</span></span>

- <span data-ttu-id="b0330-215">要开始创建自定义服务，请参阅[必应视觉搜索 API 概述](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)</span><span class="sxs-lookup"><span data-stu-id="b0330-215">To begin creating a customized service, see [Bing Visual Search API Overview](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)</span></span>
- <span data-ttu-id="b0330-216">要创建第一个请求，请参阅快速入门：[C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)</span><span class="sxs-lookup"><span data-stu-id="b0330-216">To create your first request, see the quickstarts: [C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)</span></span>
- <span data-ttu-id="b0330-217">自行熟悉[视觉搜索 API 参考](https://aka.ms/bingvisualsearchreferencedoc)。</span><span class="sxs-lookup"><span data-stu-id="b0330-217">Familiarize yourself with the [Visual Search API Reference](https://aka.ms/bingvisualsearchreferencedoc).</span></span>

### <a name="background"></a><span data-ttu-id="b0330-218">背景</span><span class="sxs-lookup"><span data-stu-id="b0330-218">Background</span></span>

- <span data-ttu-id="b0330-219">[深度学习图像分割](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet)：Microsoft 文章介绍了从背景分离图像的过程</span><span class="sxs-lookup"><span data-stu-id="b0330-219">[Deep Learning Image Segmentation](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet): Microsoft paper describes the process of separating images from backgrounds</span></span>
- <span data-ttu-id="b0330-220">[Ebay 视觉搜索](https://arxiv.org/abs/1706.03154)：康奈尔大学研究</span><span class="sxs-lookup"><span data-stu-id="b0330-220">[Visual Search at Ebay](https://arxiv.org/abs/1706.03154): Cornell University research</span></span>
- <span data-ttu-id="b0330-221">[Pinterest 视觉发现](https://arxiv.org/abs/1702.04680)：康奈尔大学研究</span><span class="sxs-lookup"><span data-stu-id="b0330-221">[Visual Discovery at Pinterest](https://arxiv.org/abs/1702.04680) Cornell University research</span></span>
- <span data-ttu-id="b0330-222">[语义哈希](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)：多伦多大学研究</span><span class="sxs-lookup"><span data-stu-id="b0330-222">[Semantic Hashing](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf) University of Toronto research</span></span>

<span data-ttu-id="b0330-223">本文由 Giovanni Marchetti 和 Mariya Zorotovich 撰写。</span><span class="sxs-lookup"><span data-stu-id="b0330-223">_This article was authored by Giovanni Marchetti and Mariya Zorotovich._</span></span>