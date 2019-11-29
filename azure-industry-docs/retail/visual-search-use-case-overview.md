---
title: 在零售业中基于 CosmosDB 进行视觉搜索的入门指南
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 本文介绍了将电子商务基础结构从本地迁移到 Azure 的阶段。
ms.openlocfilehash: b43ea305e11ac32da58e4d0521d79f90d5c23d85
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308484"
---
# <a name="visual-search-overview"></a>视觉搜索概述

## <a name="executive-summary"></a>执行摘要

正如我们今天所看到的，人工智能提供了改变零售业的潜力。 有理由相信零售商将开发由 AI 提供支持的客户体验体系结构。 我们的某些期望是，通过 AI 增强的平台会因超个性化而带来收入增长。 数字商务继续提升客户期望、偏好和行为。 诸如实时参与、相关推荐和超个性化等需求只需点击一下按钮即可提高速度和便利性。 通过自然语音、视觉等在应用程序中实现智能可实现零售方式的改进，从而在改变客户购物方式的同时提高价值。

本文档重点介绍  视觉搜索的 AI 概念，并提供一些关于其实现的关键考虑因素。 它提供了一个工作流示例，并将其阶段映射到相关 Azure 技术。 该概念基于客户能够利用他们的移动设备拍摄的图像或 Internet 上的图像，根据体验的意向进行相关和/或类似项目的搜索。 因此，视觉搜索提高了从短信输入到具有多个元数据点的图像的速度，从而快速显示存在的可用项目。

## <a name="visual-search-engines"></a>视觉搜索引擎

视觉搜索引擎使用图像作为输入来检索信息，但通常但不排除将图像作为输出。

引擎在零售业中变得越来越普遍，原因很简单：

- 根据 2017 年发布的 [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) 报告，大约 75% 的 Internet 用户在购买前搜索产品的图片或视频。
- 根据 [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf)（视觉搜索公司）2015 年报告，74% 的消费者发现文本搜索效率低下。

因此，根据 [Markets &amp;Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp) 的研究，到 2019 年，图像识别市场的价值将超过 250 亿美元。

该技术已经与主要的电子商务品牌合作，这些品牌也为其发展做出了重大贡献。 最突出的早期采用者可能是：

- eBay 在其应用程序中使用他们的图像搜索和“在 eBay 上查找它”工具（目前这只是一种移动体验）。
- Pinterest 具有其 Lens 视觉发现工具。
- Microsoft 必应视觉搜索。

## <a name="adopt-and-adapt"></a>采用和调整

幸运的是，你无需大量的计算能力即可从视觉搜索中获利。 任何拥有图像目录的企业都可以利用其 Azure 服务中内置的 Microsoft 专业知识。

[必应视觉搜索](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API 提供了一种从图像提取上下文信息的方法，可用于识别，例如识别家居用品、时装、多种产品等。

它还将从自己的目录、具有相关购物来源的产品、相关搜索中返回视觉上类似的图像。 虽然有趣，但如果你的公司不是其中一个来源，该用途将是有限的。

此外，必应还提供：

- 允许浏览图像中找到的对象或概念的标记。
- 图像中感兴趣区域的边框（例如衣服、家具）。

你可以使用该信息将搜索空间（和时间）显著缩减到公司的产品目录中，从而将其限制为感兴趣的区域和类别中类似这些目录的对象。

## <a name="implement-your-own"></a>实现你自己的视觉搜索

实现视觉搜索时需要考虑几个关键组件：

- 引入并筛选图像
- 存储和检索技术
- 特征化、编码或“哈希”
- 相似度量值或距离和排名

 ![](./assets/visual-search-use-case-overview/visual-search-pipeline.png)

图 1：  视觉搜索管道示例

### <a name="sourcing-the-pictures"></a>溯源图片

如果你没有图片目录，则需要在公开可用的数据集上定型算法，例如 fashion [MNIST](https://www.kaggle.com/zalando-research/fashionmnist)、deep [fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) 等。 它们包含几类产品目录，通常用于对图像分类和搜索算法进行基准测试。

 ![](./assets/visual-search-use-case-overview/deep-fashion-dataset.png)

图 2：  Deep Fashion 数据集示例

### <a name="filtering-the-images"></a>筛选图像

大多数基准数据集（例如前面提到的那些）已经过预处理。

如果你正在生成自己的图像，则至少你会希望图像都具有相同的大小，主要取决于针对输入所定型的模型。

在许多情况下，最好还要标准化图像的亮度。 根据搜索的详细程度，颜色也可能是冗余信息，因此减少为黑白二色将有助于缩短处理时间。

最后但同样重要的是，图像数据集应该在它所代表的不同类之间保持平衡。

### <a name="image-database"></a>图像数据库

数据层是体系结构的特别精细的组件。 其中包含：

- 映像
- 有关图像的任何元数据（大小、标记、产品 SKU、说明）
- 机器学习模型生成的数据（例如每个图像 4096 个元素的数值矢量）

当从不同源检索图像或使用多个机器学习模型以获得最佳性能时，数据结构将发生变化。 因此，选择能够处理半结构化数据而不是固定架构的技术或组合非常重要。

你还需要最少数量的有用数据点（例如图像标识符或密钥、产品 SKU、说明、标记字段）。

[Azure CosmosDB](https://azure.microsoft.com/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) 为在其上生成的应用程序提供了所需的灵活性和各种访问机制（这对目录搜索有利）。 但是，必须谨慎操作才能获得最佳性价比。 CosmosDB 允许存储文档附件，但每个帐户有一个总限制，因而成本可能颇为高昂。 通常做法是将实际图像文件存储在 blob 中，并在数据库中插入指向它们的链接。 对于 CosmosDB，这意味着创建一个文档，其中包含与该图像关联的目录属性（sku、标记等）和包含图像文件 URL 的附件（例如，在 Azure blob 存储、OneDrive 上）。

 ![](./assets/visual-search-use-case-overview/cosmosdb-data-model.png)

图 3：  CosmosDB 分层资源模型

如果你计划利用 Cosmos DB 的全局分发，请注意它将复制文档和附件，但不会复制链接的文件。 你需要考虑这些文件的内容分发网络。

其他适用的技术是 Azure SQL 数据库（如果可以使用固定架构）和 blob 的组合，甚至可以是成本较低且可快速存储和检索的 Azure 表和 blob。

### <a name="feature-extraction-amp-encoding"></a>功能提取 &amp; 编码

编码过程从数据库的图片中提取显著特征，并将其中的每一个映射到稀疏“特征”向量（具有许多零的向量），它可具有数千个组件。 该向量是描述图像特征的数量表征（例如边缘、形状），类似于代码。

特征提取技术通常使用  迁移学习机制。 当选择预定型的神经网络，通过它运行每个图像并将生成的特征向量存储回图像数据库中时，会发生这种情况。 通过这种方式，你可以从定型网络的人那里“迁移”学习。 Microsoft 已开发并发布了几个预定型网络，并已广泛用于图像识别任务，例如 [ResNet50](https://www.kaggle.com/keras/resnet50)。

依赖于神经网络，特征向量均或多或少有些长且稀疏，因此内存和存储要求将变化。

此外，你可能会发现不同的网络适用于不同的类别，因此视觉搜索的实现可能实际上生成不同大小的特征向量。

预定型的神经网络相对易于使用，但可能不如针对图像目录定型的自定义模型效率高。 这些预定型的网络通常设计用于基准数据集的分类，而不是搜索特定的图像集合。

你可能希望修改和重新定型它们，以便它们生成类别预测和密集（即较小的非稀疏）向量，这对于限制搜索空间、降低内存和存储要求非常有用。 可以使用二进制向量，并且通常将其称为“[语义哈希](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)”，这是从文档编码和检索技术派生的术语。 二进制表示形式简化了进一步计算。

 ![](./assets/visual-search-use-case-overview/resnet-modifications.png)

图 4：  修改 ResNet 以用于视觉搜索 - F. Yang 等，2017 年

无论选择预定型模型还是开发自己的模型，你仍然需要决定在何处运行模型本身的特征化和/或定型。

Azure 提供了多种选择：VM、Azure Batch、[Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet)、Databricks 群集。 但是，在所有情况下，使用 GPU 都可以获得最佳性价比。

Microsoft 最近还宣布推出可用于快速计算的 FPGA，其成本仅为 GPU 成本的一小部分（项目 [Brainwave](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet)）。 但是，在撰写本文时，此产品仅限于某些网络体系结构，因此你需要详细评估其性能。

### <a name="similarity-measure-or-distance"></a>相似度量值或距离

当在特征向量空间中表示图像时，找到相似性成为定义此类空间中点之间距离度量值的问题。 定义距离后，你可以计算相似图像的群集和/或定义相似矩阵。 所选的距离度量值不同，结果可能会有所不同。 例如，针对实数向量的最常见的欧几里德距离度量值很容易理解：它捕获距离的大小。 然而，就计算而言，它是相当低效的。

[余弦](https://en.wikipedia.org/wiki/Cosine_similarity)距离通常用于捕捉向量的方向，而不是其大小。

诸如[汉明](https://en.wikipedia.org/wiki/Hamming_distance)距离二进制表示形式的替代方案就降低了准确度以提高效率和速度。

向量大小和距离度量值的组合将决定搜索的计算密集程度和内存密集程度。

### <a name="search-amp-ranking"></a>搜索 &amp; 排名

定义相似性后，我们需要设计一种有效方法来检索最接近的 N 个项目以确定作为输入传递的那个项目，然后返回标识符列表。 这也称为“图像排名”。 在大型数据集上，禁止计算每个距离的时间，因此我们使用近似最近邻算法。 针对该算法存在几个开放源代码库，因此你不必从头开始编写代码。

最后，内存和计算要求将决定定型模型部署技术选择以及高可用性。 通常情况下，搜索空间将被分区，并且排名算法的若干实例将并行运行。 允许可伸缩性和可用性的一个选项是 [Azure Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) 集群。 在这种情况下，建议跨多个容器（每个处理一个搜索空间的分区）和数个节点（针对高可用性）部署排名模型。

## <a name="next-steps"></a>后续步骤

实现视觉搜索的过程不一定非常复杂。 你可以使用必应或通过 Azure 服务生成自己的服务，同时借鉴 Microsoft AI 研究和工具。

### <a name="trial"></a>试用

- 试用[视觉搜索 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)

### <a name="develop"></a>开发

- 要开始创建自定义服务，请参阅[必应视觉搜索 API 概述](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)
- 若要创建第一个请求，请参阅快速入门：[C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)
- 自行熟悉[视觉搜索 API 参考](https://aka.ms/bingvisualsearchreferencedoc)。

### <a name="background"></a>背景

- [深度学习图像分割](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet)：Microsoft 文章介绍了从背景分离图像的过程
- [Ebay 视觉搜索](https://arxiv.org/abs/1706.03154)：康奈尔大学研究
- [Pinterest 视觉发现](https://arxiv.org/abs/1702.04680)：康奈尔大学研究
- [语义哈希](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)：多伦多大学研究

 本文由 Giovanni Marchetti 和 Mariya Zorotovich 撰写。