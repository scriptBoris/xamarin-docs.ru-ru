---
title: Framework концепции в Xamarin.iOS
description: В этом документе описывается использование iOS 11 Vision Framework в Xamarin.iOS. В частности, здесь рассматриваются обнаружения прямоугольник и обнаружение лиц.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2017
ms.openlocfilehash: 4746de2f351e866fd72946b204f97e997c3e88c4
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350668"
---
# <a name="vision-framework-in-xamarinios"></a>Framework концепции в Xamarin.iOS

Framework концепции добавлен ряд возможностей для iOS 11, в том числе обработки нового образа:

- [Обнаружение прямоугольника](#rectangles)
- [Обнаружение лиц](#faces)
- Машинное обучение анализ образов (подробно [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Определение штрихкода
- Анализ выравнивание изображения
- Обнаружение текста
- Период обнаружения
- Обнаружение объектов и отслеживание

![Нельзя было сфотографировать с обнаружено три прямоугольника](vision-images/found-rectangles-tiny.png) ![Нельзя было сфотографировать с двумя обнаруженные лица:](vision-images/xamarin-home-faces-tiny.png)

Прямоугольник обнаружения и обнаружения лиц рассматриваются более подробно ниже.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Обнаружение прямоугольника

[VisionRects пример](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) показано, как обработка изображения и рисовать прямоугольники обнаруженных на нем.

### <a name="1-initialize-the-vision-request"></a>1. Инициализации запроса компьютерного зрения

В `ViewDidLoad`, создание `VNDetectRectanglesRequest` , ссылающийся на `HandleRectangles` метод, который будет вызываться в конце каждого запроса:

`MaximumObservations` Свойства также должен быть установлен, в противном случае он будет по умолчанию 1 и возвращается только один результат.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Начать обработку компьютерного зрения

Следующий код запускает обработку запроса. В **VisionRects** образец, этот код запускается после пользователь выбрал изображения:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Этот обработчик передает `ciImage` в Framework концепции `VNDetectRectanglesRequest` , созданного на шаге 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Обрабатывать результаты обработки компьютерного зрения

После завершения обнаружения прямоугольник платформа выполняет `HandleRectangles` метод, который сводку, показанный ниже:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Отобразить результаты

`OverlayRectangles` Метод в **VisionRectangles** образец содержит три функции:

- Подготовка к просмотру исходного изображения
- Рисование прямоугольника, чтобы указать, где была обнаружена каждого из них, и
- Добавление текстовую метку для каждого прямоугольника с помощью CoreGraphics.

Представление [источника образца](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) для конкретный метод CoreGraphics.

![Нельзя было сфотографировать с обнаружено три прямоугольника](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Дальнейшая обработка

Прямоугольник в обнаружении часто только первый шаг в цепочке операций, таких как с [в этом примере CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), где прямоугольников передаются модели CoreML проанализировать рукописных цифр.


<a name="faces" />

## <a name="face-detection"></a>Обнаружение лиц

[Пример VisionFaces](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) работает таким же образом, чтобы **VisionRectangles** пример, с использованием другой класс запроса компьютерного зрения.

### <a name="1-initialize-the-vision-request"></a>1. Инициализации запроса компьютерного зрения

В `ViewDidLoad`, создание `VNDetectFaceRectanglesRequest` , ссылающийся на `HandleRectangles` метод, который будет вызываться в конце каждого запроса.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Начать обработку компьютерного зрения

Следующий код запускает обработку запроса. В **VisionFaces** примера, этот код запускается после пользователь выбрал изображения:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Этот обработчик передает `ciImage` в Framework концепции `VNDetectFaceRectanglesRequest` , созданного на шаге 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Обрабатывать результаты обработки компьютерного зрения

После завершения обнаружения лиц, обработчик выполняет `HandleRectangles` метод, который выполняет обработку ошибок и отображает границы обнаруженных лиц, а также вызывает `OverlayRectangles` для рисования ограничивающие прямоугольники на исходное изображение:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Отобразить результаты

`OverlayRectangles` Метод в **VisionFaces** образец содержит три функции:

- Подготовка к просмотру исходного изображения
- Рисование прямоугольника на каждом лице обнаружена, и
- Добавление текстовую метку для каждого лица, с помощью CoreGraphics.

Представление [источника образца](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) для конкретный метод CoreGraphics.

![Нельзя было сфотографировать с двумя обнаруженные лица:](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Дальнейшая обработка

Платформа Vision включает дополнительные возможности для обнаружения лиц функции, такие как глаза и рот. Используйте `VNDetectFaceLandmarksRequest` тип, который будет возвращать `VNFaceObservation` результаты, как и в шаге 3 выше, но с дополнительными `VNFaceLandmark` данных.


## <a name="related-links"></a>Связанные ссылки

- [Прямоугольники Vision (пример)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Концепция лиц (пример)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Усовершенствования в образ Core - фильтры, исходного состояния системы, концепции и более (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/510/)
