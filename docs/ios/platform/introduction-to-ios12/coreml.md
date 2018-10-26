---
title: Core ML 2 в Xamarin.iOS
description: В этом документе описываются обновления для Core ML доступны как часть iOS 12. В частности он рассматривает улучшения производительности, связанные с помощью новых API-интерфейса прогнозирования пакетной службы.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 79215d84307c03c0d34e6314b1c8b07e4f83fdd0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131977"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 в Xamarin.iOS

ML лежит на iOS, macOS, tvOS и watchOS технологии машинного обучения. Она позволяет приложениям, делая прогнозов, основанных на модели машинного обучения.

В iOS 12 Core ML включает пакетной обработки API. Этот API позволяет более эффективно Core ML и обеспечивает повышение производительности в сценариях, где используется модель, чтобы сделать последовательность прогнозов.

## <a name="sample-app-marshabitatcoremltimer"></a>Пример приложения: MarsHabitatCoreMLTimer

Чтобы продемонстрировать пакетных прогнозов с помощью Core ML, взгляните на [MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer) примера приложения. В этом образце используется модель Core ML обучаться для прогнозирования затрат на создание habitat на режим Mars, на основе различных входных данных: число панелей Солнечной, число greenhouses и число акр.

Фрагменты кода в этом документе поступать из этого примера.

## <a name="generate-sample-data"></a>Создание демонстрационных данных

В `ViewController`, пример приложения `ViewDidLoad` вызовы метода `LoadMLModel`, который загружает модели включены Core ML:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Затем пример приложения создает 100 000 `MarsHabitatPricerInput` объектов для использования в качестве входных данных для последовательного Core ML прогнозов. Каждый созданный образец имеет число панелей Солнечной, число greenhouses и число акр случайное значение.

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

Коснитесь любой из трех кнопок приложения выполняет две последовательности прогнозов: один с помощью `for` цикла, а другой — с помощью нового пакета `GetPredictions` метод впервые появился в iOS 12:

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for - цикл

`for` Версии цикла теста наивно перебор указанное число входов, вызвав [ `GetPrediction` ](https://developer.xamarin.com/api/member/CoreML.MLModel.GetPrediction/) для каждого и отменяя результат. Метод времени, время, необходимое для создания прогнозов:

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions (новый пакет API)

Создает в пакетной версии теста `MLArrayBatchProvider` объект из входного массива (так как это обязательный входной параметр для `GetPredictions` метод), создает [`MLPredictionOptions`](https://developer.xamarin.com/api/type/CoreML.MLPredictionOptions/)
Объект, который предотвращает прогноза вычислений из ограниченных для ЦП и использует `GetPredictions` API, чтобы получить прогнозы, снова отменяя результат:

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>Результаты

В симуляторе и устройства `GetPredictions` быстрее, чем Core ML прогнозы на основе цикл завершения.

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [Новые возможности в Core ML, часть 1 (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Новые возможности в Core ML, часть 2 (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Введение в Core ML в Xamarin.iOS](https://docs.microsoft.com/en-us/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Работа с моделями Core ML](https://developer.apple.com/machine-learning/build-run-models/)
