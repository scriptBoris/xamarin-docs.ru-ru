---
title: Образ Core в Xamarin.iOS
description: Образ основных компонентов — это новая платформа, представленные в iOS 5, чтобы предоставить обработка изображений и видео расширения функциональности в реальном времени. В этой статье рассматриваются эти возможности за счет образцы Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d71f14c26865b71eca991910df4a68f2540f9715
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115413"
---
# <a name="core-image-in-xamarinios"></a>Образ Core в Xamarin.iOS

_Образ основных компонентов — это новая платформа, представленные в iOS 5, чтобы предоставить обработка изображений и видео расширения функциональности в реальном времени. В этой статье рассматриваются эти возможности за счет образцы Xamarin.iOS._

Образ основных компонентов — это новая платформа, впервые появился в iOS 5, который предоставляет ряд встроенных фильтров и эффекты для применения к изображения и видео, включая обнаружение лиц.

Этот документ содержит простые примеры:

-  Обнаружение лиц.
-  Применение фильтров к изображению
-  Список доступных фильтров.


Эти примеры должны помогут приступить к работе, включение функции образ основных компонентов в приложения Xamarin.iOS.

## <a name="requirements"></a>Требования

Необходимо использовать последнюю версию Xcode.

## <a name="face-detection"></a>Обнаружение лиц

Делает функция обнаружения лиц образ основных компонентов, просто надпись — он пытается определить лиц на фотографии и возвращает координаты любого лица, которые оно распознает. Эти сведения позволяют подсчитать число людей в образ, рисовать индикаторы на изображение (например) для «теги» людей на фотографиях), или что-нибудь еще можно представить.

Этот код из CoreImage\SampleCode.cs показано, как создать и использовать обнаружение лиц на внедренного изображения:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

Функции массив будет заполняться `CIFaceFeature` объектов (Если обнаружены любого лица). Существует `CIFaceFeature` для каждой грани. `CIFaceFeature` имеет следующие свойства:

-  HasMouthPosition — был ли обнаружен рот для этого лица.
-  HasLeftEyePosition — был ли обнаружен левой глаза для этого лица.
-  HasRightEyePosition — был ли обнаружен правой глаза для этого лица. 
-  MouthPosition — координаты рот для этого лица.
-  LeftEyePosition — координаты левого глаза для этого лица.
-  RightEyePosition — координаты правой глаза для этого лица.


Координаты для все эти свойства имеют их происхождение в нижнем левом углу — в отличие от UIKit, в котором используется в качестве источника левого верхнего. При использовании координаты на `CIFaceFeature` убедитесь, что «отражение» их. В этом представлении очень простой пользовательский образ в CoreImage\CoreImageViewController.cs демонстрирует способ рисования на изображении «лиц треугольником» (Примечание `FlipForBottomOrigin` метод):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

Затем в файле SampleCode.cs изображений и возможности назначаются перед перерисовке изображения:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

На снимке экрана показан пример выходных данных: расположение обнаруженных лиц компоненты отображаются в UITextView и на исходный образ, используя CoreGraphics.

Из-за, как работает распознавание лиц, она иногда обнаруживает операции помимо человеческих лиц (например, эти toy кодеры!).

## <a name="filters"></a>Фильтры

Существует более чем 50 различных встроенные фильтры, и framework является расширяемой, таким образом, можно реализовать новые фильтры.

## <a name="using-filters"></a>С помощью фильтров

Применение фильтра изображение имеет четыре отдельных этапов: загрузка образа, создании фильтра, применяя фильтр и сохранение (или отображение) результат.

Во-первых, загрузите изображение в `CIImage` объекта.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

Во-вторых создайте класс фильтра и задайте его свойства.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

В-третьих, доступ к `OutputImage` свойство и вызвать `CreateCGImage` метод для подготовки к просмотру окончательный результат.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Наконец назначьте образ в представление, чтобы увидеть результат. В реальном приложении полученный образ может быть сохранен в файловой системе, фотоальбома, твит или по электронной почте.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Эти снимки экрана Показать результат `CISepia` и `CIHueAdjust` фильтры, которые демонстрируются в CoreImage.zip пример кода.

См. в разделе [настроить контракт и яркость изображения рецепт](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) пример `CIColorControls` фильтра.

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Список фильтров и их свойств

Этот код из CoreImage\SampleCode.cs выводит полный список встроенных фильтров и их параметрах.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

[Ссылки на класс CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) описание 50 встроенные фильтры и их свойств. С помощью кода выше вы можете запрашивать классов filter, включая значения по умолчанию для параметров и минимальное и максимальное допустимые значения (что может использоваться для проверки входных данных перед применением фильтра).

Список категорий выходные данные будут выглядеть в симуляторе – вы можете просмотреть список, чтобы увидеть все фильтры и их параметрах.

 [![](introduction-to-coreimage-images/coreimage05.png "Список категорий выходные данные будут выглядеть в симуляторе")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Каждый фильтр в списке был предоставлен как класс в Xamarin.iOS, поэтому можно также изучить API Xamarin.iOS.CoreImage в сборку веб-приложения или с помощью автозаполнения в Visual Studio для Mac или Visual Studio. 

## <a name="summary"></a>Сводка

В этой статье показано, как использовать некоторые из новых возможностей платформы iOS 5 образ основных компонентов обнаружение и применения фильтров к изображению. Существует множество фильтров другой образ, доступные в платформе для использования.

## <a name="related-links"></a>Связанные ссылки

- [Основной образ (пример)](https://developer.xamarin.com/samples/CoreImage/)
- [Настройка контракта и яркость рецепт образа](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [С помощью фильтров изображения Core](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Ссылки на класс CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
