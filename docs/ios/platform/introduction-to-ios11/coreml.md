---
title: Общие сведения о CoreML в Xamarin.iOS
description: В этом документе описывается CoreML, позволяющий машинного обучения в iOS. В этом документе описывается, как приступить к работе с CoreML и способы его использования с помощью платформы компьютерного зрения.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: 13178d4530e3214c6cf31c1018b21815ccd2227f
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350695"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Общие сведения о CoreML в Xamarin.iOS

CoreML привносит машинного обучения для iOS-приложений можно воспользоваться преимуществами моделей обученной машинного обучения для выполнения всех видов задач, от проблем для распознавания изображений.

В данном разделе рассматриваются следующие вопросы:

- [Приступая к работе с CoreML](#coreml)
- [С помощью CoreML с помощью платформы компьютерного зрения](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Приступая к работе с CoreML

Эти шаги описывают способы добавления CoreML в проект iOS. Ссылаться на [Mars Habitat Pricer образец](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) для практического примера.

![Снимок экрана прогнозирования цены Habitat режима MARS](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Добавьте в проект модели CoreML

Добавление модели CoreML (файл с **.mlmodel** расширение) для **ресурсы** каталог проекта. 

В свойствах файла модели его **действие при сборке** присваивается **CoreMLModel**. Это означает, что он будет скомпилирован в **.mlmodelc** файл при построении приложения.

### <a name="2-load-the-model"></a>2. Загрузить модель

Загрузка модели с помощью `MLModel.Create` статический метод:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Задайте параметры

Параметры модели передаются в нее с помощью класс контейнера, который реализует `IMLFeatureProvider`.

Классы поставщика функции ведут себя как словарь строки и `MLFeatureValue`s, где каждое значение функция может быть простая строка или число, массив или данных или буфер пикселя, содержащий изображение.

Ниже приведен код для поставщика функцию одним значением:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

С помощью классов следующим образом, входные параметры можно указать способом, который понятен CoreML. Имена функций (таких как `myParam` в примере кода) должен соответствовать модели ожидает.

### <a name="4-run-the-model"></a>4. Запустить модель

С помощью модели необходимо, поставщик функций для создания экземпляра и параметры набор, затем, `GetPrediction` вызвать метод:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Извлечение результатов

Результат прогноза `outFeatures` также является экземпляром `IMLFeatureProvider`; выходные данные значения может осуществляться с использованием `GetFeatureValue` с именем каждого параметра выходные данные (такие как `theResult`), как показано в этом примере:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>С помощью CoreML с помощью платформы компьютерного зрения

CoreML может также использоваться в сочетании с помощью платформы концепции для выполнения операций в образе, таких как распознавание фигур, идентификация объектов и других задач.

Следующие шаги описывают, как CoreML и концепции используются совместно в [CoreMLVision пример](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). Пример объединяет [распознавания прямоугольники](~/ios/platform/introduction-to-ios11/vision.md#rectangles) из .NET framework концепции с _MNINSTClassifier_ модели CoreML для идентификации рукописных цифр на фотографиях.

![Распознавание изображений, номер 3](coreml-images/vision3.png) ![Распознавание изображений, номер 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Создание модели CoreML компьютерного зрения

Модели CoreML _MNISTClassifier_ загружается и затем помещается в `VNCoreMLModel` которого делает модели доступными для задач компьютерного зрения. Этот код также создает две концепции запросов: сначала для поиска прямоугольники, изображения, а затем для обработки прямоугольника с помощью модели CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

По-прежнему необходимо реализовать класс `HandleRectangles` и `HandleClassification` методов для запросов концепции, показано в шагах 3 и 4 ниже.

### <a name="2-start-the-vision-processing"></a>2. Начать обработку компьютерного зрения

Следующий код запускает обработку запроса. В **CoreMLVision** образец, этот код запускается после пользователь выбрал изображения:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Этот обработчик передает `ciImage` в Framework концепции `VNDetectRectanglesRequest` , созданного на шаге 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Обрабатывать результаты обработки компьютерного зрения

После завершения обнаружения прямоугольник выполняет `HandleRectangles` метод, который обрезает его, чтобы извлечь первый прямоугольник, преобразует прямоугольник изображение в оттенки серого и передает его модели CoreML для классификации.

`request` Параметр, переданный в этот метод содержит сведения о запросе концепции и с помощью `GetResults<VNRectangleObservation>()` метод, он возвращает список прямоугольников, найденные в образе. Первый прямоугольник `observations[0]` извлекается и передается модели CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

`ClassificationRequest` Был инициализирован на шаге 1, чтобы использовать `HandleClassification` метода, определенного на следующем шаге.

### <a name="4-handle-the-coreml"></a>4. Дескриптор CoreML

`request` Параметр, переданный этому методу содержит сведения о запросе CoreML и с помощью `GetResults<VNClassificationObservation>()` метод, он возвращает список возможных результатов, упорядоченные по достоверности (самый высокий достоверности первого):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Примеры

Существует три примера CoreML, чтобы повторить:

* [Пример прогнозирования цены Habitat Mars](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) имеет простые числовые входы и выходы.

* [Vision и CoreML пример](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) принимает параметр, изображения и основан на идеологии компьютерного зрения для идентификации square регионов в образе, которые передаются в модели CoreML, распознает одиночными цифрами.

* Наконец [пример распознавания изображений CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) использует CoreML для идентификации компонентов в фотографию. По умолчанию он использует меньшее **SqueezeNet** модель (5 МБ), но оно было записано, чтобы вы можете скачать и включить большее **VGG16** модели (553 МБ). Дополнительные сведения см. в разделе [файле readme](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Связанные ссылки

- [Машинное обучение (Apple)](https://developer.apple.com/machine-learning/)
- [Пример CoreML (режим Mars Habitat) (пример)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML и визуальных (номер распознавание) (пример)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Распознавание изображений CoreML (пример)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML методологиях Azure Custom (пример)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Знакомство с CoreML (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/703/)
