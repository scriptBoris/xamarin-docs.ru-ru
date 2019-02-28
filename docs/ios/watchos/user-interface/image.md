---
title: watchOS элементов управления Image в Xamarin
description: В этом документе описывается использование элементов управления image в приложении watchOS, созданных с помощью Xamarin. В нем описывается элемент WKInterfaceImage setimage-метод, добавление изображений к расширение просмотра, анимации и многое другое.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a2b8c99156963ae167aecd29a618d0feeffbdc7
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321133"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS элементов управления Image в Xamarin

предоставляет watchOS [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) управления для отображения изображений и простой анимации. Некоторые элементы управления могут также иметь фонового изображения (например, кнопки, группы и контроллеры интерфейса).

![](image-images/image-walkway.png "Apple Watch отображение рисунка") ![](image-images/image-animation.png "Apple Watch с простой анимации")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Используйте образы каталог активов для добавления изображения Watch комплект приложений.
Только **@2x** версии являются обязательными, так как все смотреть устройства имеют отображает Retina.

![](image-images/asset-universal-sml.png "Только 2 x версии являются обязательными, так как все смотреть устройства имеют отображает Retina")

Рекомендуется убедиться, что сами являются оптимальным размером область отображения контрольных значений. *Избегайте* с помощью неправильно размера изображения (особенно большими) и масштабирования, чтобы отобразить их в watch.

Размеры Watch Kit (38 мм и 42 мм) в изображении каталога активов позволяет указать различные образы для каждого размера экрана.

![](image-images/asset-watch-sml.png "Можно использовать размеры Watch Kit, 38 мм и 42 мм изображения каталог активов для указания различных изображений для каждого размера экрана")


## <a name="images-on-the-watch"></a>Образы в Watch

Наиболее эффективный способ отображения изображений — *включать их в проекте приложения watch* и отобразить их с помощью `SetImage(string imageName)` метод.

Например [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) пример имеет ряд образы, добавленные в каталог ресурсов в проекте приложения watch:

![](image-images/asset-whale-sml.png "В примере WatchKitCatalog имеет ряд образы, добавленные в каталог ресурсов в проекте приложения watch")

Их можно эффективно загружать и отображаются на контрольные значения с помощью `SetImage` с параметром имя строки:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Фоновые изображения

Эта же логика применяется для `SetBackgroundImage (string imageName)` на `Button`, `Group`, и `InterfaceController` классы. Наилучшей производительности достигается путем сохранения изображений в приложении для Apple watch сам.


## <a name="images-in-the-watch-extension"></a>Образы в расширении контрольных значений

В дополнение к загрузке изображений, которые хранятся в самом приложении Контрольные значения, вы можете отправлять образы из пакета расширения в приложении для Apple watch для отображения (или можно загрузить образы из удаленного расположения и отображать).

Чтобы загрузить изображения из расширения Контрольные значения, создайте `UIImage` экземпляров и затем вызвать `SetImage` с `UIImage` объекта.

Например [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) пример содержит образ с именем **Bumblebee** в проекте расширения контрольных значений:

![](image-images/asset-bumblebee-sml.png "В примере WatchKitCatalog имеется образ с именем Bumblebee в проекте расширения контрольных значений")

Следующий код приведет к:

- изображение, загружаемая в память, и
- отображается в watch.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Анимации

Для анимации набор изображений, они должны начинаются с одинаковым префиксом и иметь числовой суффикс.

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) пример имеет ряд нумерованных образов в проекте приложения watch с **шины** префикс:

![](image-images/asset-bus-animation-sml.png "В примере WatchKitCatalog имеется ряд нумерованных образов в проекте приложения watch с префиксом шины")

Для отображения этих изображений в качестве анимации, сначала загрузить образ с помощью `SetImage` с именем префикса и затем вызовите метод `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Вызовите `StopAnimating` на изображение, чтобы остановить цикл анимации:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Приложение. Кэширование изображений (watchOS 1)

> [!IMPORTANT]
> приложения для watchOS 3 Запустите полностью на устройстве. Следующие сведения являются watchOS 1 только для приложений.

Если приложение многократно использует изображение, которое хранится в модуле (или загрузки), существует возможность кэшировать изображение в хранилище Контрольные значения, для повышения производительности для последующих отображается.

Используйте `WKInterfaceDevice`s `AddCachedImage` метод, чтобы передать образ в часы, а затем использовать `SetImage` с параметром имени образа как строку, чтобы отобразить ее:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Вы можете запросить содержимое в кэш образов в коде с помощью `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Управление кэшем

Кэш размером около 20 МБ. Находится между перезагрузками приложения, и при ее заполнении отвечает для очистки файлов с помощью `RemoveCachedImage` или `RemoveAllCachedImages` методы `WKInterfaceDevice.CurrentDevice` объекта.



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple doc образа](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
