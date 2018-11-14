---
title: ARKit 2 в Xamarin.iOS
description: В этом документе описываются обновления для ARKit в iOS 12. Рассматривается использование объектов ссылки и изображения для обнаружения, включает в себя код текстурирования окружающей среды и обсуждает часто встречающиеся проблемы в программировании ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 4d5319005b28c5afa0906c44cfa59f0cad40de76
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617622"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 в Xamarin.iOS

ARKit значительно значительно развился до сегодняшнего дня последнего года в iOS 11. В первую очередь теперь обнаруживаются вертикальной и горизонтальной плоскости, что значительно повышает практичности опыт внутренний режиме дополненной реальности. Кроме того существуют новые возможности:

* Распознавание изображения ссылку и объекты как соединения между реального мира и цифровые
* Новый режим освещения, которое имитирует реальные освещения
* Возможность совместного использования и сохранения сред AR
* Новый формат файлов, предпочтительный для хранения содержимого AR

## <a name="recognizing-reference-objects"></a>Распознавание ссылаться на объекты

Один компонент showcase ARKit 2 является возможность распознавать изображения ссылку и объекты. Эталонные образы можно загружать из файлов изображений в normal ([рассказано ниже](#more-tracking-configurations)), но ссылки, должны просматриваться объектов, с помощью разработчиков [ `ARObjectScanningConfiguration` ](https://developer.xamarin.com/api/type/ARKit.ARObjectScanningConfiguration/).

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>Пример приложения: сканирование и распознавание трехмерных объектов

[Сканирование и обнаружение трехмерные объекты](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) пример представляет собой из [проекта Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) , в котором описывается:

* Приложения управления состояния с помощью [ `NSNotification` ](https://developer.xamarin.com/api/type/Foundation.NSNotification/) объектов
* Пользовательские визуализации
* Сложные жесты
* Объект сканирования
* Хранение [`ARReferenceObject`](https://developer.xamarin.com/api/type/ARKit.ARReferenceObject/)

Сканирование ссылочный объект является интенсивное и процессора и старые устройства часто будет сложно обеспечить стабильную отслеживания.

### <a name="state-management-using-nsnotification-objects"></a>Управление состоянием с помощью объектов NSNotification

Это приложение использует это конечный автомат, переходы между следующими состояниями:

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

Кроме того использует внедренный набор состояний и переходит в `AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

Приложение использует реактивного архитектуру, которая отправляет уведомления переход состояния для [ `NSNotificationCenter` ](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/) и подписывается на эти уведомления. Настройка выглядит как следующий фрагмент кода из `ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Типичное уведомление обработчик будет обновить пользовательский Интерфейс и возможности изменять состояние приложения, такие как этот обработчик, который обновляет по мере сканирования объекта:

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Наконец `Enter{State}` методы предназначены для изменения модели и UX в зависимости от нового состояния:

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Пользовательские визуализации

Показано приложение, низкого уровня «точки cloud» объекта внутри ограничивающего проецируются на обнаруженных горизонтальной плоскости.

Это облако точка доступна разработчикам в [ `ARFrame.RawFeaturePoints` ](https://developer.xamarin.com/api/property/ARKit.ARFrame.RawFeaturePoints/) свойство. Визуализация точки облаке эффективно может быть непростую проблему. Итерация по точки, затем и поместить новый узел SceneKit для каждой точки бы kill частота кадров. Кроме того Если выполнена асинхронно, может произойти задержка. Образец поддерживает производительность благодаря стратегии трех частей:

* С помощью небезопасного кода для ПИН-код, данные в поместите и интерпретировать данные как необработанный буфер байтов.
* Преобразование, необработанный буфер, в [ `SCNGeometrySource` ](https://developer.xamarin.com/api/type/SceneKit.SCNGeometrySource/) и создания «шаблон» [ `SCNGeometryElement` ](https://developer.xamarin.com/api/type/SceneKit.SCNGeometryElement/) объекта.
* Быстро «стыковки» необработанные данные и шаблон с помощью [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](https://developer.xamarin.com/api/member/SceneKit.SCNGeometry.Create/p/SceneKit.SCNGeometrySource[]/SceneKit.SCNGeometryElement[]/)

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

Результат выглядит следующим образом:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Сложные жесты

Пользователь может масштабировать, повернуть и перетащите ограничивающий прямоугольник вокруг целевого объекта. Есть две интересные вещи в распознавателей связанный жест.

Во-первых все распознавателей жестов активировать только в том случае, после достиг порогового значения; Например палец переместил так много точек или поворот превышает некоторые угол. Вам достаточно временно накапливаться перемещения, пока пороговое значение превышено, а затем применить его постепенно:

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

Второй интересно, что необходимо сделать в связи с помощью жестов является то, что ограничивающий прямоугольник перемещается по отношению к обнаружил реальных плоскости. Этот аспект рассматривается в [этой записи блога Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Другие новые возможности ARKit 2

### <a name="more-tracking-configurations"></a>Дополнительные конфигурации отслеживания

Теперь можно использовать любой из следующих как основу для смешанной реальностью:

* Только устройства акселерометр ([`AROrientationTrackingConfiguration`](https://developer.xamarin.com/api/type/ARKit.AROrientationTrackingConfiguration/), iOS 11)
* Грани ([`ARFaceTrackingConfiguration`](https://developer.xamarin.com/api/type/ARKit.ARFaceTrackingConfiguration/), iOS 11)
* Ссылок на изображения ([`ARImageTrackingConfiguration`](https://developer.xamarin.com/api/type/ARKit.ARImageTrackingConfiguration/), iOS 12)
* Сканирование трехмерные объекты ([`ARObjectScanningConfiguration`](https://developer.xamarin.com/api/type/ARKit.ARObjectScanningConfiguration/), iOS 12)
* Visual инерционного odometry ([`ARWorldTrackingConfiguration`](https://developer.xamarin.com/api/type/ARKit.ARWorldTrackingConfiguration/), улучшенные возможности iOS 12)

`AROrientationTrackingConfiguration`, рассматриваемые в [этой записи блога и F# пример](https://github.com/lobrien/FSharp_Face_AR), ограничены и предоставляет слишком ограничивать возможности смешанной реальности, как только расположений цифровые объекты по отношению к движения устройства, не пытаясь связать устройство и экрана в реальном мире.

`ARImageTrackingConfiguration` Позволяет распознавать реальных двумерные изображения (картин, логотипы и т.д.) и использовать их для цифрового изображения привязки:

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Существует две интересные аспекты этой конфигурации:

* Он является эффективным и может использоваться с потенциально большое количество исходных образов
* Цифровых изображений, привязанный к образа, даже если это изображение перемещается в реальном мире (например, если крышку книги, распознается, он будет отслеживать книги, он извлекается в готовых laid вниз и т. д.).

`ARObjectScanningConfiguration` , Которые обсуждались [ранее](#recognizing-reference-objects) и представляет собой конфигурацию, ориентированные на разработчика для сканирования трехмерные объекты. Это средство предоставляет процессора и с большим объемом батареи и не должны использоваться в пользовательские приложения. Образец [сканирование и обнаружение трехмерные объекты](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) демонстрируется использование этой конфигурации.

Конфигурацию окончательный отслеживания, `ARWorldTrackingConfiguration` , — рабочая лошадка большинство функций, смешанной реальности. Эта конфигурация использует «visual инерционного odometry» для связи цифровые реальных «функциональных точек». Цифровой геометрического или спрайтов привязанные относительно плоскостей горизонтальные и вертикальные реальных или относительно обнаружил `ARReferenceObject` экземпляров. В этой конфигурации мира расположена в исходное положение камеры в пространстве с оси z, выровненным по тяжести и цифровые объекты «останутся в силе» относительно объектов в реальном мире.

### <a name="environmental-texturing"></a>Текстурирования окружающей среды

ARKit 2 поддерживает «окружающей среды текстурирования», использующий записанного изображениях оценка освещения и даже применения зеркального отражения для блестящей объектов. Кубической карты окружающей среды — это результат динамически и после камеры пользователя во всех направлениях, может привести к замечательную реалистичный стиль работы:

![среды изображения текстур demo](images/arkit_env_texturing.png)

Для использования среды текстурирования:

* Ваш [ `SCNMaterial` ](https://developer.xamarin.com/api/type/SceneKit.SCNMaterial/) необходимо использовать объекты [ `SCNLightingModel.PhysicallyBased` ](https://developer.xamarin.com/api/property/SceneKit.SCNLightingModel.PhysicallyBased/) и присвойте ей значение в диапазоне от 0 до 1 для [ `Metalness.Contents` ](https://developer.xamarin.com/api/property/SceneKit.SCNMaterial.Metalness/) и [ `Roughness.Contents` ](https://developer.xamarin.com/api/property/SceneKit.SCNMaterialProperty.Contents/) и
* Необходимо задать конфигурацию отслеживания [ `EnvironmentTexturing` ](https://developer.xamarin.com/api/property/ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing/)  =  [AREnvironmentTexturing.Automatic "](https://developer.xamarin.com/api/field/ARKit.AREnvironmentTexturing.Automatic/) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Несмотря на то, что вполне Светящийся текстура, которая отображается в предыдущем фрагменте кода является fun в выборке, окружающей среды текстурирования, имеет смысл использовать с ограничения, иначе она активировать ответ «совершаемые Долина» (текстуры — это только Оценка основана на какие камеры запись).


### <a name="shared-and-persistent-ar-experiences"></a>Общие и возможности постоянных AR

Является другим основным дополнением к ARKit 2 [ `ARWorldMap` ](https://developer.xamarin.com/api/type/ARKit.ARWorldMap/) класс, который позволяет совместно использовать или сохранения данных отслеживания мира. Получить текущую карту мира с [ `ARSession.GetCurrentWorldMapAsync` ](https://developer.xamarin.com/api/member/ARKit.ARSession.GetCurrentWorldMapAsync()/) или [ `GetCurrentWorldMap(Action<ARWorldMap,NSError>` ](https://developer.xamarin.com/api/member/ARKit.ARSession.GetCurrentWorldMap/p/System.Action%7BARKit.ARWorldMap,Foundation.NSError%7D/) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Совместное использование и восстановление карте мира:

1. Загрузить данные из файла
2. Распаковывать его в `ARWorldMap` объекта,
3. Использовать его в качестве значения для [ `ARWorldTrackingConfiguration.InitialWorldMap` ](https://developer.xamarin.com/api/property/ARKit.ARWorldTrackingConfiguration.InitialWorldMap/) свойство:

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

`ARWorldMap` Только содержит невидимые данные отслеживания world и [ `ARAnchor` ](https://developer.xamarin.com/api/type/ARKit.ARAnchor/) объектов, как и _не_ содержат цифровых активов. Для совместного использования, геометрического или изображения, необходимо разработать подходящую стратегию, подходящие для вашего сценария использования (возможно, хранения и передачи только местоположение и ориентацию геометрии и применять его в static `SCNGeometry` или, возможно, хранения и передачи Сериализованные объекты). Преимущество `ARWorldMap` — что ресурсы, после помещения относительно общего `ARAnchor`, будет постоянно отображаться между устройствами или сеансов.

### <a name="universal-scene-description-file-format"></a>Универсальный формат файла описание сцены

Функция последний заголовок ARKit 2 является внедрение компании Apple Pixar [Universal Description сцены](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) формат файла. Этот формат заменяет формата в файл Collada DAE как предпочтительный формат для совместного использования и хранения ARKit активы. Поддержка для визуализации активы встроена в iOS 12 и Mojave. USDZ файл имеет расширение несжатые и и незашифрованные ZIP-архив, содержащий файлы долл. США. Pixar [предоставляет средства для работы с файлами долл. США](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , но еще не поддержки гораздо независимых производителей.

## <a name="arkit-programming-tips"></a>Советы по программированию ARKit

### <a name="manual-resource-management"></a>Управление ресурсами вручную

В ARKit очень важно вручную управлять ресурсами. Не только это допускает высокой частоты кадров, фактически является _необходимые_ не путать «замораживание экран». Платформа ARKit — отложенная о предоставлении новый кадр камеры ([`ARSession.CurrentFrame`](https://developer.xamarin.com/api/property/ARKit.ARSession.CurrentFrame/)). До установки текущего [ `ARFrame` ](https://developer.xamarin.com/api/type/ARKit.ARFrame/) имел `Dispose()` вызывался, ARKit не предоставит новый кадр! В результате видео к «зависанию», несмотря на то, что остальная часть приложения отвечает. Следует всегда обращаться к `ARSession.CurrentFrame` с `using` блокировать или вручную вызовите `Dispose()` на нем.

Все объекты, производные от `NSObject` являются `IDisposable` и `NSObject` реализует [удаляемости](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), поэтому обычно необходимо следовать [этот шаблон для реализации `Dispose` на производный Класс](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Управление матрицы преобразования

В любом приложении 3D будет иметь дело с матрицами преобразования 4 x 4, компактно, описывающие способы перемещения, поворота и наклона объекта в трехмерном пространстве. Это SceneKit, [ `SCNMatrix4` ](https://developer.xamarin.com/api/type/SceneKit.SCNMatrix4/) объектов.  

[ `SCNNode.Transform` ](https://developer.xamarin.com/api/property/SceneKit.SCNNode.Transform/) Возвращает `SCNMatrix4` матрицу преобразования для [ `SCNNode` ](https://developer.xamarin.com/api/type/SceneKit.SCNNode/) _как поддерживаемый_ построчный `simdfloat4x4` типа. Таким образом например:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Как вы видите, положение кодируется в нижней строке первые три элемента.

В Xamarin, общий тип для манипулирования матриц преобразования имеет `NVector4`, который по соглашению, интерпретируется как основных столбцов. Т. е позиция трансляции/компонент должен M14, M24, M34, не M41, M42, M43:

![строкам и столбцам](images/arkit_row_vs_column.png)

Последовательный с выбором интерпретации матрицы является жизненно важным для правильной работы. Так как трехмерные преобразования матрицы 4 x 4, ошибок согласованности не будет создавать любые виды исключений во время компиляции или даже во время выполнения — это просто операции будет работать непредвиденным образом. Если ваш SceneKit / ARKit объекты по-видимому "зависнуть", летать немедленно или нарушение синхронизации, матрица неверное преобразование — многообещающими. Решение является простым: [ `NMatrix4.Transpose` ](https://developer.xamarin.com/api/member/OpenTK.NMatrix4.Transpose) выполнит преобразование на месте элементов.

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — сканирование и распознавание трехмерных объектов](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [Новые возможности в 2 ARKit (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Основные сведения о ARKit отслеживания и обнаружения (WWDC 2018 г.)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Общие сведения о ARKit в Xamarin.iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
