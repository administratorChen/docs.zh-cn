---
title: 使用交叉验证训练和评估机器学习模型
description: 了解如何使用交叉验证来训练和评估机器学习模型
ms.date: 05/03/2019
author: luisquintanilla
ms.author: luquinta
ms.custom: mvc,how-to
ms.openlocfilehash: a15dfbfcd563cf9df9c25779a5854a9f556523d1
ms.sourcegitcommit: ca2ca60e6f5ea327f164be7ce26d9599e0f85fe4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65065560"
---
# <a name="train-and-evaluate-a-machine-learning-model-using-cross-validation"></a><span data-ttu-id="90dbc-103">使用交叉验证训练和评估机器学习模型</span><span class="sxs-lookup"><span data-stu-id="90dbc-103">Train and evaluate a machine learning model using cross validation</span></span>

<span data-ttu-id="90dbc-104">了解如何使用交叉验证在 ML.NET 中生成更强大的机器学习模型。</span><span class="sxs-lookup"><span data-stu-id="90dbc-104">Learn how to use cross validation to build more robust machine learning models in ML.NET.</span></span> 

<span data-ttu-id="90dbc-105">交叉验证是一种训练和模型评估技术，可将数据拆分为多个分区，并利用这些分区训练多个算法。</span><span class="sxs-lookup"><span data-stu-id="90dbc-105">Cross-validation is a training and model evaluation technique that splits the data into several partitions and trains multiple algorithms on these partitions.</span></span> <span data-ttu-id="90dbc-106">此技术通过保留来自训练过程的数据来提高模型的可靠性。</span><span class="sxs-lookup"><span data-stu-id="90dbc-106">This technique improves the robustness of the model by holding out data from the training process.</span></span> <span data-ttu-id="90dbc-107">除提高不可见观测的性能之外，在数据受限的环境中，它还可用作使用较小数据集训练模型的有效工具。</span><span class="sxs-lookup"><span data-stu-id="90dbc-107">In addition to improving performance on unseen observations, in data-constrained environments it can be an effective tool for training models with a smaller dataset.</span></span>

## <a name="the-data-and-data-model"></a><span data-ttu-id="90dbc-108">数据和数据模型</span><span class="sxs-lookup"><span data-stu-id="90dbc-108">The data and data model</span></span>

<span data-ttu-id="90dbc-109">给定来自具有以下格式的文件的数据：</span><span class="sxs-lookup"><span data-stu-id="90dbc-109">Given data from a file that has the following format:</span></span>

```text
Size (Sq. ft.), HistoricalPrice1 ($), HistoricalPrice2 ($), HistoricalPrice3 ($), Current Price ($)
620.00, 148330.32, 140913.81, 136686.39, 146105.37
550.00, 557033.46, 529181.78, 513306.33, 548677.95
1127.00, 479320.99, 455354.94, 441694.30, 472131.18
1120.00, 47504.98, 45129.73, 43775.84, 46792.41
```

<span data-ttu-id="90dbc-110">数据可以通过 `HousingData` 等类进行建模：</span><span class="sxs-lookup"><span data-stu-id="90dbc-110">The data can be modeled by a class like `HousingData`:</span></span>

```csharp
public class HousingData
{
    [LoadColumn(0)]
    public float Size { get; set; }
 
    [LoadColumn(1, 3)]
    [VectorType(3)]
    public float[] HistoricalPrices { get; set; }

    [LoadColumn(4)]
    [ColumnName("Label")]
    public float CurrentPrice { get; set; }
}
```

<span data-ttu-id="90dbc-111">将数据加载到 [`IDataView`](xref:Microsoft.ML.IDataView) 中。</span><span class="sxs-lookup"><span data-stu-id="90dbc-111">Load the data in into an [`IDataView`](xref:Microsoft.ML.IDataView).</span></span>

## <a name="prepare-the-data"></a><span data-ttu-id="90dbc-112">准备数据</span><span class="sxs-lookup"><span data-stu-id="90dbc-112">Prepare the data</span></span>

<span data-ttu-id="90dbc-113">在使用数据生成机器学习模型之前预处理数据。</span><span class="sxs-lookup"><span data-stu-id="90dbc-113">Pre-process the data before using it to build the machine learning model.</span></span> <span data-ttu-id="90dbc-114">在此示例中，`Size` 和 `HistoricalPrices` 列合并为一个特征向量，其使用 [`Concatenate`](xref:Microsoft.ML.TransformExtensionsCatalog.Concatenate*) 方法输出到名为 `Features` 的新列。</span><span class="sxs-lookup"><span data-stu-id="90dbc-114">In this sample, the `Size` and `HistoricalPrices` columns are combined into a single feature vector,  which is output to a new column called `Features` using the [`Concatenate`](xref:Microsoft.ML.TransformExtensionsCatalog.Concatenate*) method.</span></span> <span data-ttu-id="90dbc-115">除了将数据转换为 ML.NET 算法所期望的格式之外，连接列还通过对连接的列应用一次操作（而不是对每个单独的列应用操作），优化了管道中的后续操作。</span><span class="sxs-lookup"><span data-stu-id="90dbc-115">In addition to getting the data into the format expected by ML.NET algorithms, concatenating columns optimizes subsequent operations in the pipeline by applying the operation once for the concatenated column instead of each of the separate columns.</span></span> 

<span data-ttu-id="90dbc-116">将列合并为单个向量后，[`NormalizeMinMax`](xref:Microsoft.ML.NormalizationCatalog.NormalizeMinMax*) 将应用于 `Features` 列，以使 `Size` 和 `HistoricalPrices` 处于 0-1 之间的同一范围内。</span><span class="sxs-lookup"><span data-stu-id="90dbc-116">Once the columns are combined into a single vector, [`NormalizeMinMax`](xref:Microsoft.ML.NormalizationCatalog.NormalizeMinMax*) is applied to the `Features` column to get `Size` and `HistoricalPrices` in the same range between 0-1.</span></span> 

```csharp
// Define data prep estimator
IEstimator<ITransformer> dataPrepEstimator = 
    mlContext.Transforms.Concatenate("Features", new string[] { "Size", "HistoricalPrices" })
        .Append(mlContext.Transforms.NormalizeMinMax("Features"));

// Create data prep transformer
ITransformer dataPrepTransformer = dataPrepEstimator.Fit(data);

// Transform data
IDataView transformedData = dataPrepTransformer.Transform(data);
```

## <a name="train-model-with-cross-validation"></a><span data-ttu-id="90dbc-117">使用交叉验证训练模型</span><span class="sxs-lookup"><span data-stu-id="90dbc-117">Train model with cross validation</span></span>

<span data-ttu-id="90dbc-118">预处理数据后，即可开始训练模型。</span><span class="sxs-lookup"><span data-stu-id="90dbc-118">Once the data has been pre-processed, it's time to train the model.</span></span> <span data-ttu-id="90dbc-119">首先，选择最符合要执行的机器学习任务要求的算法。</span><span class="sxs-lookup"><span data-stu-id="90dbc-119">First, select the algorithm that most closely aligns with the machine learning task to be performed.</span></span> <span data-ttu-id="90dbc-120">因为预测值是数字连续值，所以任务是回归任务。</span><span class="sxs-lookup"><span data-stu-id="90dbc-120">Because the predicted value is a numerically continuous value, the task is regression.</span></span> <span data-ttu-id="90dbc-121">ML.NET 实现的其中一个回归算法是 [`StochasticDualCoordinateAscentCoordinator`](xref:Microsoft.ML.Trainers.SdcaRegressionTrainer) 算法。</span><span class="sxs-lookup"><span data-stu-id="90dbc-121">One of the regression algorithms implemented by ML.NET is the [`StochasticDualCoordinateAscentCoordinator`](xref:Microsoft.ML.Trainers.SdcaRegressionTrainer) algorithm.</span></span> <span data-ttu-id="90dbc-122">若要使用交叉验证来训练模型，请使用 [`CrossValidate`](xref:Microsoft.ML.RegressionCatalog.CrossValidate*) 方法。</span><span class="sxs-lookup"><span data-stu-id="90dbc-122">To train the model with cross-validation use the [`CrossValidate`](xref:Microsoft.ML.RegressionCatalog.CrossValidate*) method.</span></span> 

> [!NOTE]
> <span data-ttu-id="90dbc-123">尽管此示例使用线性回归模型，但 CrossValidate 适用于 ML.NET 中除异常情况检测之外的所有其他机器学习任务。</span><span class="sxs-lookup"><span data-stu-id="90dbc-123">Although this sample uses a linear regression model, CrossValidate is applicable to all other machine learning tasks in ML.NET except Anomaly Detection.</span></span>

```csharp
// Define StochasticDualCoordinateAscent algorithm estimator
IEstimator<ITransformer> sdcaEstimator = mlContext.Regression.Trainers.Sdca();

// Apply 5-fold cross validation
var cvResults = mlContext.Regression.CrossValidate(transformedData, sdcaEstimator, numberOfFolds: 5);
```

<span data-ttu-id="90dbc-124">[`CrossValidate`](xref:Microsoft.ML.RegressionCatalog.CrossValidate*) 执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="90dbc-124">[`CrossValidate`](xref:Microsoft.ML.RegressionCatalog.CrossValidate*) performs the following operations:</span></span>

1. <span data-ttu-id="90dbc-125">将数据分为多个分区，数量为 `numberOfFolds` 参数中指定的值。</span><span class="sxs-lookup"><span data-stu-id="90dbc-125">Partitions the data into a number of partitions equal to the value specified in the `numberOfFolds` parameter.</span></span> <span data-ttu-id="90dbc-126">每个分区的结果是一个 [`TrainTestData`](xref:Microsoft.ML.DataOperationsCatalog.TrainTestData) 对象。</span><span class="sxs-lookup"><span data-stu-id="90dbc-126">The result of each partition is a [`TrainTestData`](xref:Microsoft.ML.DataOperationsCatalog.TrainTestData) object.</span></span>
1. <span data-ttu-id="90dbc-127">使用训练数据集上的指定机器学习算法估算器在每个分区上训练模型。</span><span class="sxs-lookup"><span data-stu-id="90dbc-127">A model is trained on each of the partitions using the specified machine learning algorithm estimator on the training data set.</span></span>
1. <span data-ttu-id="90dbc-128">每个模型的性能在测试数据集上使用 [`Evaluate`](xref:Microsoft.ML.RegressionCatalog.Evaluate*) 方法进行评估。</span><span class="sxs-lookup"><span data-stu-id="90dbc-128">Each model's performance is evaluated using the [`Evaluate`](xref:Microsoft.ML.RegressionCatalog.Evaluate*) method on the test data set.</span></span> 
1. <span data-ttu-id="90dbc-129">为每个模型返回模型及其指标。</span><span class="sxs-lookup"><span data-stu-id="90dbc-129">The model along with its metrics are returned for each of the models.</span></span>

<span data-ttu-id="90dbc-130">`cvResults` 中存储的结果是 [`CrossValidationResult`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1) 对象的集合。</span><span class="sxs-lookup"><span data-stu-id="90dbc-130">The result stored in `cvResults` is a collection of [`CrossValidationResult`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1) objects.</span></span> <span data-ttu-id="90dbc-131">此对象包括经过训练的模型以及可分别从 [`Model`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1.Model) 和 [`Metrics`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1.Metrics) 属性访问的指标。</span><span class="sxs-lookup"><span data-stu-id="90dbc-131">This object includes the trained model as well as metrics which are both accessible form the [`Model`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1.Model) and [`Metrics`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult`1.Metrics) properties respectively.</span></span> <span data-ttu-id="90dbc-132">在此示例中，`Model` 属性为 [`ITransformer`](xref:Microsoft.ML.ITransformer) 类型，`Metrics` 属性为 [`RegressionMetrics`](xref:Microsoft.ML.Data.RegressionMetrics) 类型。</span><span class="sxs-lookup"><span data-stu-id="90dbc-132">In this sample, the `Model` property is of type [`ITransformer`](xref:Microsoft.ML.ITransformer) and the `Metrics` property is of type [`RegressionMetrics`](xref:Microsoft.ML.Data.RegressionMetrics).</span></span> 

## <a name="extract-metrics"></a><span data-ttu-id="90dbc-133">提取指标</span><span class="sxs-lookup"><span data-stu-id="90dbc-133">Extract metrics</span></span>

<span data-ttu-id="90dbc-134">可以通过单个 [`CrossValidationResult`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult%601) 对象的 `Metrics` 属性访问不同的经过训练的模型的指标。</span><span class="sxs-lookup"><span data-stu-id="90dbc-134">Metrics for the different trained models can be accessed through the `Metrics` property of the individual [`CrossValidationResult`](xref:Microsoft.ML.TrainCatalogBase.CrossValidationResult%601) object.</span></span> <span data-ttu-id="90dbc-135">在本例中，通过变量 `rSquared` 访问和存储 [R 平方指标](https://en.wikipedia.org/wiki/Coefficient_of_determination)。</span><span class="sxs-lookup"><span data-stu-id="90dbc-135">In this case, the [R-Squared metric](https://en.wikipedia.org/wiki/Coefficient_of_determination) is accessed and stored in the variable `rSquared`.</span></span> 

```csharp
IEnumerable<double> rSquared = 
    cvResults
        .Select(fold => fold.Metrics.RSquared);
```

<span data-ttu-id="90dbc-136">如果检查 `rSquared` 变量的内容，则输出应该是五个 0-1 之间的值，其中最接近 1 的值为最佳值。</span><span class="sxs-lookup"><span data-stu-id="90dbc-136">If you inspect the contents of the `rSquared` variable, the output should be five values ranging from 0-1 where closer to 1 means best.</span></span>

## <a name="select-the-best-performing-model"></a><span data-ttu-id="90dbc-137">选择性能最好的模型</span><span class="sxs-lookup"><span data-stu-id="90dbc-137">Select the best performing model</span></span>

<span data-ttu-id="90dbc-138">使用 R 平方等指标按性能最好到性能最差的顺序选择模型。</span><span class="sxs-lookup"><span data-stu-id="90dbc-138">Using metrics like R-Squared, select the models from best to worst performing.</span></span> <span data-ttu-id="90dbc-139">然后，选择性能最好的模型来进行预测或执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="90dbc-139">Then, select the top model to make predictions or perform additional operations with.</span></span>

```csharp
// Select all models
ITransformer[] models =
    cvResults
        .OrderByDescending(fold => fold.Metrics.RSquared)
        .Select(fold => fold.Model)
        .ToArray();

// Get Top Model
ITransformer topModel = models[0];
```