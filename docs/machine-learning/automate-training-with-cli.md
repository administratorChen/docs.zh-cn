---
title: 使用 ML.NET CLI 自动进行模型训练
description: 了解如何使用 ML.NET CLI 工具通过命令行自动训练最佳模型。
author: CESARDELATORRE
ms.date: 04/17/2019
ms.custom: how-to
ms.openlocfilehash: c147464ff59563d336363eed73fc6337bdb12e85
ms.sourcegitcommit: 992f80328b51b165051c42ff5330788627abe973
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72275851"
---
# <a name="automate-model-training-with-the-mlnet-cli"></a>使用 ML.NET CLI 自动进行模型训练

在学习 ML.NET 时，ML.NET CLI 为 .NET 开发人员“普及”ML.NET。

若要单独使用 ML.NET API（不使用 ML.NET AutoML CLI），需要选择训练程序（针对特定任务的机器学习算法的实现），以及要应用到数据的数据转换集（特征工程）。 每个数据集的最佳管道各不相同，从所有选择中选择最佳算法增加了复杂性。 此外，每个算法都有一组要调整的超参数。 因此，可能会花费数周甚至数月时间进行机器学习模型优化，以尝试找到特征工程、学习算法和超参数的最佳组合。

此过程可使用 ML.NET CLI 自动化，其实现 ML.NET AutoML 智能引擎。

> [!NOTE]
> 本主题涉及目前处于预览状态的 ML.NET **CLI** 和 ML.NET **AutoML**，且材料可能会有所变化。

## <a name="what-is-the-mlnet-command-line-interface-cli"></a>什么是 ML.NET 命令行接口 (CLI)？

可以在任何命令提示符（Windows、Mac 或 Linux）上运行 ML.NET CLI，以根据训练数据集生成高质量的 ML.NET 模型和源代码。

如下图所示，生成高质量的 ML.NET 模型（序列化模型 .zip 文件）以及用于运行该模型/对该模型评分的示例 C# 代码非常简单。 此外，还会生成用于创建/训练该模型的 C# 代码，以便可以研究和迭代用于该生成的“最佳模型”的算法和设置。

![图像](media/automate-training-with-cli/cli-high-level-process.png "在 ML.NET CLI 内部工作的 AutoML 引擎")

可以从自己的数据集生成这些资产而无需自行编码，因此，即使已经了解 ML.NET，它也可以提高工作效率。

目前，ML.NET CLI 支持的 ML 任务包括：

- `binary-classification`
- `multiclass-classification`
- `regression`
- 未来：其他机器学习任务，例如 `recommendation`、`ranking`、`anomaly-detection`、`clustering`

用法示例：

```console
mlnet auto-train --task binary-classification --dataset "customer-feedback.tsv" --label-column-name Sentiment
```

![图像](media/automate-training-with-cli/cli-model-generation.gif)

可以在 *Windows PowerShell*、*macOS/Linux bash 或 *Windows CMD* 上以相同的方式运行它。 但是，表格自动填写（参数建议）不适用于 *Windows CMD*。

## <a name="output-assets-generated"></a>生成的输出资产

CLI `auto-train` 命令在输出文件夹中生成以下资产：

- 可供用于运行预测的序列化模型 .zip 文件（“最佳模型”）。
- C# 解决方案以及：
  - 用于运行生成的模型/对生成的模型评分的 C# 代码（使用该模型在最终用户应用中进行预测）。
  - 包含用于生成该模型的训练代码的 C# 代码（用于学习目的或模型重新训练）。
- 日志文件，包含有关评估的多种算法的所有迭代/扫描的信息（包括其详细配置/管道）。

前两个资产可以直接用于最终用户应用（ASP.NET Core Web 应用、服务、桌面应用等），以使用生成的 ML 模型进行预测。

第三个资产（训练代码）显示 CLI 用于训练生成的模型的 ML.NET API 代码，因此，可以在后台重新训练模型并调查和迭代 CLI 和 AutoML 选择的特定训练程序/算法及超参数。

## <a name="understanding-the-quality-of-the-model"></a>了解模型的质量

使用 CLI 工具生成“最佳模型”时，可看到适用于所针对的 ML 任务的质量指标（例如准确性和 R 平方）。

在这里，我们总结了按 ML 任务分组的指标，以便于了解自动生成的“最佳模型”的质量。

### <a name="metrics-for-binary-classification-models"></a>二元分类模型指标

以下显示 CLI 找到的排名前五的模型的二元分类 ML 任务指标列表：

![图像](media/automate-training-with-cli/cli-binary-classification-metrics.png)

准确性是分类问题的常用指标，但准确性并不总是用于选择最佳模型的最佳指标，如下文参考中所述。 在某些情况下，需要使用其他指标评估模型的质量。

若要探索和了解 CLI 输出的指标，请参阅[二元分类指标](resources/metrics.md#metrics-for-binary-classification)。

### <a name="metrics-for-multi-class-classification-models"></a>多类分类模型指标

以下显示 CLI 找到的排名前五的模型的多类分类 ML 任务指标列表：

![图像](media/automate-training-with-cli/cli-multiclass-classification-metrics.png)

若要探索和了解 CLI 输出的指标，请参阅[多类分类指标](resources/metrics.md#metrics-for-multi-class-classification)。

### <a name="metrics-for-regression-models"></a>回归模型指标

如果观测值与模型预测值之间的差异很小且无偏差，则回归模型能够很好地拟合数据。 可以使用特定指标评估回归。

将看到 CLI 找到的排名前五的最佳质量模型的类似指标列表。 在与回归 ML 任务相关的这一特定案例中：

![图像](media/automate-training-with-cli/cli-regression-metrics.png)

若要探索和了解 CLI 输出的指标，请参阅[回归指标](resources/metrics.md#metrics-for-regression)。

## <a name="see-also"></a>请参阅

- [如何安装 ML.NET CLI 工具](how-to-guides/install-ml-net-cli.md)
- [教程：使用 ML.NET CLI 自动生成二元分类器](tutorials/mlnet-cli.md)
- [ML.NET CLI 命令参考](reference/ml-net-cli-reference.md)
- [ML.NET CLI 中的遥测](resources/ml-net-cli-telemetry.md)
