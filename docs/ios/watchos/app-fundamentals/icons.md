---
title: Работа с watchOS значки в Xamarin
description: В этом документе описываются различные значки, необходимую для приложения watchOS, а также как настроить решение, чтобы включить эти значки.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 435af10484827826d53b767c2738e3945e0bae42
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121377"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Работа с watchOS значки в Xamarin

Apple Watch решений требуется два набора значков:

* Значки приложений iOS, которые будут отображаться на устройстве iPhone.
* Apple Watch значки, которые будут отображаться в круге в меню контрольных значений и на экранах уведомлений. Значок приложения также отображается в [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) приложение iOS.

## <a name="apple-watch-icons"></a>Значки Apple Watch

| | | |
|-|-|-|
|Значок приложения iOS|Откроется на iPhone и начнется в родительское приложение|![значок приложения iOS](icons-images/icon-ios.png)|
|Значок приложения Watch|Отображается на начальном экране Apple Watch|![значок приложения watchOS](icons-images/icon-home.png)|
||Отображается в уведомлениях Контрольные значения|![значок уведомления watchOS](icons-images/notification-icon.png)|
||Отображается в [Apple Watch приложение iOS](~/ios/watchos/app-fundamentals/settings.md)|![значок iOS приложения Watch](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Настройка решения

Чтобы убедиться, что ваше приложение iOS и приложение watch Показывать правильное имя и значок, выполните следующие действия для каждого проекта:

### <a name="ios-app"></a>Приложение iOS

Ссылаться на [руководство значки приложения iOS](~/ios/app-fundamentals/images-icons/app-icons.md) чтобы ваше приложение iOS значки настроены правильно.

#### <a name="infoplist"></a>Info.plist

Строка, которая появляется рядом с приложение для Apple watch в [приложение "Параметры" Apple Watch](~/ios/watchos/app-fundamentals/settings.md) настраивается в **Info.plist приложения iOS**.

Убедитесь, что ваш **Info.plist** имеет `CFBundleName` ключом и значением (Примечание: этот режим отличается `CFBundleDisplayName`, может иметь оба):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Приложение Apple Watch

Один раз в [родительское приложение](~/ios/watchos/app-fundamentals/parent-app.md) значки настроена, необходимо добавить каталог активов значок приложения в приложении для Apple watch.

1. Щелкните правой кнопкой мыши на проекте приложения Watch и выберите **файл > Добавить > новый файл... > iOS > каталог активов** добавляемый каталог ресурсов проекта.

 ![](icons-images/newasset.png "Добавьте каталог ресурсов в проект")

2. Дважды щелкните **AppIcon.appiconset/Contents.json** файла

  ![](icons-images/xcassets-iconset-sml.png "Содержимое AppIcon")

3. Добавьте все изображения watchOS, как показано на следующем снимке экрана:

  [![](icons-images/appicons-sml.png "Добавьте все изображения watchOS, как показано на следующем снимке экрана")](icons-images/appicons.png#lightbox)

  Ссылаться на [логотипу Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) для размера (размеры также отображаются на экране). Помните, автоматически обрезается эти значки для подготовки к просмотру в кружке.

  Значок списка должен выглядеть следующим образом:

  ![](icons-images/xcassets-complete-sml.png "Значок списка в обозревателе решений")

4. Чтобы каталог активов, включенными в приложение, добавьте следующий раздел и значение **Info.plist приложения Watch**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcon.appiconset</string>
```

Можно проверить значки настроены правильно, проверив [приложение "Параметры" Apple Watch](~/ios/watchos/app-fundamentals/settings.md) в симуляторе iPhone или создание [уведомления](~/ios/watchos/platform/notifications.md) и подтверждения того, значок появляется уведомление экран.

> [!NOTE]
> Значки не может иметь альфа-канал (приложения будут отклонены во время отправки в App Store при наличии альфа-канал). Можно проверить, существует ли альфа-канал и удалите его [с помощью предварительной версии приложения в Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Связанные ссылки

- [Руководство по значок & образы watchOS Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
