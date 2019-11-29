---
title: 面向制造业的按需高性能 Azure 计算服务
author: ercenk
ms.author: ercenk
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 制造业中所需的强力计算的概述。
ms.openlocfilehash: fe5200a726b2a65efaed2bc7a8de01e97766d425
ms.sourcegitcommit: 2714a77488c413f01beb169a18acab45663bcfd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308522"
---
# <a name="on-demand-scalable-high-power-compute"></a>按需、可缩放、强力计算

## <a name="introduction"></a>介绍

客户要求产品具有以下属性：轻型、强大、安全、可持续和自定义。 因此，设计阶段已变得日益复杂。 在该阶段中，计算机用于执行可视化、分析、模拟和优化操作。 这些任务将变得更为复杂并耗尽计算资源。 此外，产品的联系越来越紧密，并生成大量需要进行处理和分析的数据。

这一切反映了一个需求：大量计算资源、按需。

在本文中，我们将介绍工程和制造过程中需要大量计算能力的一些已知区域，并探索 Microsoft Azure 平台如何提高帮助。

## <a name="cloud-design-workstations"></a>云设计工作站

产品设计师在产品开发生命周期的设计和规划阶段中使用各种软件工具。 CAD 工具在设计师的工作站上需要强大的图形功能，而这些工作站的费用较高。 这些增强型工作站通常在设计师的办公室内，尝试将其转换为实际位置。

随着云解决方案的日益普及和新功能的问世，云工作站的想法开始变得更为可行。 使用云上托管的工作站允许设计师从任何位置进行访问。 并且允许组织将成本模型从资本支出更改为运营支出。

### <a name="remote-desktop-protocol"></a>远程桌面协议

Microsoft 的远程桌面协议 (RDP) 长期以来仅支持 TCP。 TCP 引入的开销多于 UDP。 从 RDP 8.0 开始，UDP 可用于运行 Microsoft 远程桌面服务的服务器。 为了便于使用，虚拟机 (VM) 必须有足够的硬件资源，即：CPU、内存和最至关重要的图形处理单元 (GPU)。 （GPU 可以说是高性能云工作站的最重要组件。）Windows Server 2016 提供了多个用于访问基础图形功能的选项。 默认 RDS GPU 解决方案（也称为 Windows 高级光栅化平台 (WARP)）是足以应对知识工作者方案的解决方案，但提供用于云工作站方案的资源不足。 RemoteFX vGPU 是为远程连接引入的一项 RemoteFX 功能，可用于每个服务器具有较高用户密度的方案，允许激增 GPU 使用率。 但是，当该使用 GPU 的强大功能时，需要离散设备分配 (DDA) 才能充分利用 GPU 的强大功能。

NV 系列 VM 可用于作为 Azure N 系列产品的一部分的单个或多个 NVDIA GPU。 这些 VM 已针对远程可视化效果和 VDI 方案进行了优化，使用 OpenGL 和 DirectX 之类的框架。 增至 4 个 GPU 就可以预配工作站，从而通过 Azure 上的 DDA 充分利用 GPU。

非常值得一提的是 Azure 平台的可编程性。 它为 VM 提供了多个选项。 例如，可以按需预配工作站。 也可以通过高级存储上的 Azure 磁盘或 Azure 文件将远程计算机的状态保留在本地上。 这些选项使你能够控制成本。 XenDesktop 和 XenApp 解决方案中 Microsoft 与 Citrix 的合作伙伴关系也是桌面虚拟化解决方案的另一种替代方法。

## <a name="analysis-and-simulation"></a>分析和模拟

对计算机上的物理系统进行分析和模拟已有很长一段时间。 有限元素分析 (FEA) 是一种用于解决许多分析问题的数字方法。 FEA 需要大量计算能力来执行大型矩阵计算。 随着从 2D 到 3D 的转换以及向 FEA 网格添加粒度，FEA 模型的解决方案中涉及的矩阵数量呈指数级增长。 这需要按需部署计算能力。
解决问题代码可以并行运行来充分利用资源的可伸缩性，这很重要。

解决模拟问题需要大规模计算资源。 高性能计算 (HPC) 是大规模计算的类。 HPC 需要较低的后端网络延迟，借助远程直接内存访问 (RDMA) 功能可加快并行计算。 Azure 平台提供了为高性能计算构建的 VM。 它们提供与 DDR4 内存配对的专用处理器，并且允许在 Linux 和 Windows 安装上有效地运行计算密集型解决方案。 它们提供多种大小。 请参阅[高性能计算 VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk)。
若要了解 Azure 如何采用其他方式支持 HPC，请参阅大计算：[HPC 和批处理](https://azure.microsoft.com/solutions/big-compute/?WT.mc_id=computeinmanufacturing-docs-ercenk)。

Azure 平台支持扩展和缩减解决方案。用于模拟的常见软件包之一是来自 CD-adapco 的 STAR-CCM+。 演示 STAR-CCM+ 运行“Le Mans 100 million cell”计算流体动力学 (CFD) 模型的[已发布研究](https://azure.microsoft.com/blog/availability-of-star-ccm-on-microsoft-azure/?WT.mc_id=computeinmanufacturing-docs-ercenk)提供了平台可伸缩性的概览。 由于在运行模拟时添加了更多内核，下图演示了观察到的可伸缩性：

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/34f1873b-4db5-4c62-b963-8bdf3966cf60.png](assets/bigcompute-assets/starccm.png)

另一个常用的工程分析软件包是 ANSYS CFD。 它使工程师能够执行多物理场分析，包括流体作用力、热效应、结构完整性和电磁辐射。 [已发布的研究](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk)演示 Azure 上的解决方案的可伸缩性，其中显示类似结果。

![https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/77129585-f25c-4c29-b22b-80c627d03daa.png](assets/bigcompute-assets/fluent.png)

可以通过使用所有云解决方案的 [HPC 和 GPU VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json?WT.mc_id=computeinmanufacturing-docs-ercenk) 系列在 Azure 虚拟机或[虚拟机规模集 (VMSS)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 上部署需要并行执行的软件包，而不是投资本地计算群集。

### <a name="burst-to-azure"></a>迸发到 Azure

如果本地群集可用，则另一种选择是将其扩展到 Azure，从而卸载峰值工作负载（也称为迸发到 Azure）。 为此，需要使用其中一个支持 Azure 的本地工作负载管理器（例如 [Alces Flight Compute](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview?WT.mc_id=computeinmanufacturing-docs-ercenk)、[TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)、[IBM Spectrum Symphony 和 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/?WT.mc_id=computeinmanufacturing-docs-ercenk)、[PBS Pro](http://pbspro.org/)、[Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx?WT.mc_id=computeinmanufacturing-docs-ercenk)）。

另一种选择是 Azure Batch，它是一种用于有效地运行大规模并行和 HPC 批处理作业的服务。 Azure Batch 允许使用消息传递接口 (MPI) API 的作业。 Batch 支持具有 [HPC](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc?WT.mc_id=computeinmanufacturing-docs-ercenk) 和 [GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) 优化的 VM 系列的 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx?WT.mc_id=computeinmanufacturing-docs-ercenk) 和 Intel MPI。 Microsoft 还获得了 [Cycle Computing](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/?WT.mc_id=computeinmanufacturing-docs-ercenk)，其中包含为在 Azure 上运行群集而提供更高抽象级别的解决方案。 另一种选择是在 Azure 上运行可无缝访问补充 Azure 服务（如 [Azure 存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage?WT.mc_id=computeinmanufacturing-docs-ercenk)和 [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview?WT.mc_id=computeinmanufacturing-docs-ercenk)）的 [Cray 超级计算机](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure)。

## <a name="generative-design"></a>生成设计

设计过程始终是迭代的。 设计师以目标设计的一组约束和参数开始并循环访问多种设计替代方法，最终确定满足约束的方法。
但是，当计算能力几乎为无限时，可以评估数千个  甚至数万个  设计替代方法，而不只是几个。 此过程以参数模型开始，并将其用于 CAD 工具。 随着云平台上的计算资源日益增多，该行业将进入下一阶段。 生成设计是用于描述向软件工具提供参数和约束的设计过程的术语。 该工具随后会生成设计替代方法，从而创建解决方案的多种排列。
生成设计有以下几个方法：拓扑优化、点阵优化、图面优化和窗体合成。 这些方法的详细信息不在本文的讨论范围内。 但是，这些方法中的常见模式是需要具有对计算密集型环境的访问权限。

生成设计的起点是定义算法必须循环访问的设计参数，以及合理的增量和值范围。 算法随后会为这些参数的每种有效组合创建设计替代方法。 这样可以生成大量设计替代方法。 创建这些替代方法需要大量计算资源。
此外，还必须为每个设计替代方法运行所有模拟和分析任务。 最终结果是需要大规模计算环境。

Azure 中用于通过 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 和 [VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 按需增大计算需求的多个选项是这些工作负载的自然目标。

## <a name="machine-learning-ml"></a>机器学习 (ML)

我们可以在非常简单的级别通用化 ML 系统，如下所示：鉴于一个数据点或一组数据点，系统将返回相关的结果。 这样，ML 系统便用于解决问题，例如：

- 鉴于过去的房价和房屋的属性，即将上市的指定房屋的预测价格是多少？

- 鉴于来自各种传感器的读数和计算机过去的失败案例，计算机在下一阶段失败的可能性有多大？

- 给定一组图像，哪一张是家猫？

- 给定石油管道的视频源，受损部分是否有大量凹痕？

使用人工智能 (AI) 和机器学习 (ML) 添加高级分析功能首先会开发模型，其过程类似如下。

![](assets/bigcompute-assets/aipipeline.png)

[选择算法](https://docs.microsoft.com/azure/machine-learning/studio/algorithm-choice?WT.mc_id=computeinmanufacturing-docs-ercenk)取决于数据的大小、质量和性质，以及所需答案的类型。 基于输入大小、所选算法和计算环境，此步骤通常需要大计算密集型资源，并且可能需要不同的时间才能完成。 下图来自[技术文章](https://blogs.technet.microsoft.com/machinelearning/2017/07/25/lessons-learned-benchmarking-fast-machine-learning-algorithms/?WT.mc_id=computeinmanufacturing-docs-ercenk)，用于对 ML 算法定型进行基准测试；鉴于不同的算法、数据集大小和计算目标（GPU 或 CPU），它显示了完成定型周期的时间。

![](assets/bigcompute-assets/vmsizes.png)

决策的主要驱动因素是业务问题。 如果该问题需要使用合适的算法处理大型数据集，则关键因素是用于定型算法的云规模计算资源。
[Azure Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=computeinmanufacturing-docs-ercenk) 是一种用于大规模并行定型 AI 模型的服务。

借助 Azure Batch AI，数据科学家可以在工作站上使用 [Azure Data Science Virtual Machine (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 或 [Azure Deep Learning Virtual Machine (DLVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/deep-learning-dsvm-overview?WT.mc_id=computeinmanufacturing-docs-ercenk) 开发解决方案并向群集推送定型。 DSVM 和 DLVM 是特别配置的 VM 映像，其中包含预安装的一组丰富工具和示例。

![](assets/bigcompute-assets/azurebatchai.png)

## <a name="conclusion"></a>结束语

制造业需要大量数学计算，使用对高端硬件组件（包括图形处理单元 (GPU)）的访问。 托管资源的平台的可伸缩性和弹性至关重要。 若要控制成本，必须在提供最佳速度时满足需求。

Microsoft Azure 平台提供了一系列用于满足这些需求的选项。 你可以从头开始，控制每个资源及其各个方面来生成你自己的解决方案。 或者，可以寻找一个 Microsoft 合作伙伴来促进解决方案创建。 我们的合作伙伴知道如何充分利用 Azure 的强大功能。

## <a name="next-steps"></a>后续步骤

- 通过部署 [NV 系列 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu?WT.mc_id=computeinmanufacturing-docs-ercenk) 来设置云工作站

- 查看用于部署满足设计需求的工具来充分利用 Azure HPC 功能的[选项](https://docs.microsoft.com/azure/virtual-machines/linux/high-performance-computing?WT.mc_id=computeinmanufacturing-docs-ercenk)

- 了解 [Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=computeinmanufacturing-docs-ercenk)的可能性
