---
title: Работа с watchOS размеры экрана в Xamarin
description: В этом документе описывается, как работать с различными размерами экранов watchOS. В нем описывается watchOS конструктора интерфейса, watchOS Simulator и ресурсы изображений.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117503"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Работа с watchOS размеры экрана в Xamarin

Apple Watch можно найти в двух размеров экрана:

- **38 мм**
  - логических пикселях 136 x 170 (физические пиксели 272 x 340)

- **42 мм**
  - 156 x 195 логических пикселях (физические пиксели 312 x 390).

Размер экрана необходимо обращать внимание при разработке и тестировании приложений.

## <a name="watchos-interface-designer"></a>Конструктор интерфейса watchOS

По умолчанию Visual Studio для Mac конструктора появится смотреть контроллеры интерфейса в **Any Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "Конструктор отображает смотреть контроллеры интерфейса в любой Apple Watch")

Используйте размер меню для предварительного просмотра раскадровки в одном из размеров экрана: **38 мм** или **42 мм**:

![](screen-sizes-images/screen-menu-sml.png "Выбор размера 38 мм или 42 мм")

Иногда, больший размер экрана будет отображаться содержимое, которое может быть усечен или скрытого на экране меньшего размера.
Убедитесь, что проверка в обоих размеров.


### <a name="interface-design"></a>Разработка интерфейса

Приложение должно отображаться то же содержимое на экране, независимо от размера и следует развернуть или свернуть элементов соответствующим образом. В Visual Studio для Mac конструкторе в инспекторе атрибутов следует использовать **относительно контейнера** или **размер в соответствии с содержимым** предпочтение фиксированных размеров.

![](screen-sizes-images/sizeattributepanel-sml.png "Предпочтительнее фиксированных размеров использовать относительно контейнера или размер в соответствии с содержимым")

Так как внешняя панель черного цвета заключается в окне контрольных значений, предоставляя Отбивки вокруг интерфейса не рекомендуется. Разрешить элементы rest от края экрана и позволить декоративную панель форме естественные границы вокруг приложения.


## <a name="watchos-simulator"></a>Симулятор watchOS

Когда тестированием в симуляторе можно легко переключаться между двумя размеры экрана с помощью **оборудования > устройства** меню.

![](screen-sizes-images/simulator.png "Имитатор можно переключаться между размеров два экрана, с помощью меню «устройства»")


## <a name="image-resources"></a>Ресурсы изображений

Следует использовать несколько графические активы, если одного ресурса не хорошо выглядят на разных размеров. Каталоги активов образа точечных позволяют указывать для каждого размера:

![](screen-sizes-images/images-xcassets.png "Редактора каталога ресурсов изображений")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Кроме того можно используйте код для определения размера экрана и полностью загружать различные образы:

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

Дополнительные сведения о с помощью [управления](~/ios/watchos/user-interface/image.md).



## <a name="related-links"></a>Связанные ссылки

- [Введение в watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
