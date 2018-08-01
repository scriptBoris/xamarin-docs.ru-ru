---
title: watchOS элементы управления изображением в Xamarin
description: В этом документе описывается использование элементов управления image в приложении watchOS, созданных с помощью Xamarin. В нем описывается элемент управления WKInterfaceImage, SetImage-метод, добавление изображений в расширении watch, анимации и многое другое.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: eb58c587f737a5991a21f0efe9964353a8ab0399
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791255"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS элементы управления изображением в Xamarin

предоставляет watchOS [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/) управления для отображения изображений и простых анимаций. Некоторые элементы управления могут также иметь фоновое изображение (например, кнопки, группы и контроллеры интерфейс).

![](image-images/image-walkway.png "Рисунок отображение Apple Watch") ![ ] (image-images/image-animation.png "Apple Watch с простая анимация")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Добавление изображений в комплект средств для наблюдения за приложениями с помощью средства каталога изображений.
Только **@2x** версии являются обязательными, так как все смотреть отображает Сетчатка устройства.

![](image-images/asset-universal-sml.png "Требуется, только 2 версии x, поскольку все смотреть отображает Сетчатка устройства")

Рекомендуется убедиться, что сами являются оптимальный размер отображения контрольных значений. *Избегайте* использование неправильного размера изображений (особенно большими) и масштабирования для их отображения на часах.

Размеры комплект средств для наблюдения за (38 до 42 мм) в изображении каталога активов можно использовать для указания различных изображений для каждого размера экрана.

![](image-images/asset-watch-sml.png "Комплект средств для наблюдения за размеры 38 до 42 мм каталога образ ОС используется для указания различных изображений для каждого размера экрана")


## <a name="images-on-the-watch"></a>Образы в Watch

Наиболее эффективный способ отображения изображений — *включить их в проект приложения Контрольное значение* и отобразить их с помощью `SetImage(string imageName)` метод.

Например [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) образец имеет ряд образы, добавленные в каталоге активов в проекте приложения контрольных значений:

![](image-images/asset-whale-sml.png "В образце WatchKitCatalog имеет ряд образы, добавленные в каталоге активов в проекте приложения Контрольное значение")

Они могут эффективно загружено и отображено на контрольные значения с помощью `SetImage` с параметром name строки:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Фоновые изображения

Тот же принцип применяется для `SetBackgroundImage (string imageName)` на `Button`, `Group`, и `InterfaceController` классы. Наилучшей производительности достигается путем сохранения изображений в само приложение контрольных значений.


## <a name="images-in-the-watch-extension"></a>Образы в расширении Watch

Помимо загрузки изображения, хранимые в состав самого приложения Контрольные значения, можно отправить изображения из пакета расширения приложения контрольных значений для отображения (или может загружать изображения из удаленного расположения и отображать).

Чтобы загрузить изображения из расширения Контрольные значения, создайте `UIImage` экземпляров, а затем вызвать `SetImage` с `UIImage` объекта.

Например [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) образец содержит изображение с именем **Bumblebee** в проекте расширения контрольных значений:

![](image-images/asset-bumblebee-sml.png "Образец WatchKitCatalog содержит изображение с именем Bumblebee в проекте расширения Контрольное значение")

В результате появляется следующий код:

- изображения, загружаемая в память, и
- отображается на часах.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Анимации

Чтобы анимировать набор изображений, они должны начинаются с тем же префиксом и иметь числовой суффикс.

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) образец имеет несколько нумерованных изображений в проект приложения Контрольные значения с помощью **шины** префикс:

![](image-images/asset-bus-animation-sml.png "В образце WatchKitCatalog имеет ряд нумерованной изображения в проекте приложения Контрольные значения с префиксом шины")

Чтобы отобразить эти изображения в виде анимации, сначала загрузить образ с помощью `SetImage` с именем префикса, а затем вызовите метод `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Вызовите `StopAnimating` для элемента управления изображения, чтобы остановить цикл анимации:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Приложение: Кэширования изображений (watchOS 1)

> [!IMPORTANT]
> watchOS 3 приложения выполняются только на устройство. Следующие сведения являются watchOS 1 только для приложений.

Если приложение многократно использует изображение, которое хранится в расширении (или загрузки), можно кэшировать изображение в хранилище Контрольные значения, чтобы повысить производительность для последующих отображается.

Используйте `WKInterfaceDevice`s `AddCachedImage` способ передачи образа в часы, а затем используйте `SetImage` с параметром name изображение как строку для отображения:

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

Вы можете запрашивать содержимое кэша образов в коде с помощью `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Управление кэшем

Кэш примерно 20 МБ. Он сохраняется после перезагрузки приложения и при ее заполнении вашей обязанностью является необходимость очистки файлов с помощью `RemoveCachedImage` или `RemoveAllCachedImages` методы `WKInterfaceDevice.CurrentDevice` объекта.



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Изображение doc Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
