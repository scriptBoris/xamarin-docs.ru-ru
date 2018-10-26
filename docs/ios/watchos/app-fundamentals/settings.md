---
title: Работа с параметрами в Xamarin watchOS
description: В этом документе описывается работа с параметрами watchOS в Xamarin. В нем описывается добавление параметров в решение приложения watch, с помощью этих параметров в приложение и приложение Apple Watch на iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 36164e1e9f92b5a5520d10f769f3953cfa2ceb85
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106355"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Работа с параметрами в Xamarin watchOS

Apple Watch приложения могут использовать те же функциональные возможности параметров, как приложения iOS — параметры пользовательского интерфейса отображается в **Apple Watch** приложение для iPhone, но значения доступны в приложении для iPhone и также расширение просмотра.

![](settings-images/intro.png "Apple Watch приложения могут использовать те же функциональные возможности параметров, как приложения iOS")

Параметры, которые будут храниться в общую папку, доступную для приложения iOS и расширение приложения watch, определяется **групп приложений**. Вы должны [настроить группу приложений](~/ios/watchos/app-fundamentals/app-groups.md) прежде чем добавлять параметры, используя приведенные ниже инструкции.

## <a name="add-settings-in-a-watch-solution"></a>Добавление параметров в решении контрольных значений

В **приложение для iPhone** в решении (*не* приложении для Apple watch или расширения):

1. Щелкните правой кнопкой мыши **Добавить > новый файл...**  и выберите **Settings.bundle** (нельзя изменить имя в **новый файл** диалоговое окно):

   [![](settings-images/settings-add-sml.png "Добавить новый набор параметров")](settings-images/settings-add.png#lightbox)

2. Измените имя на **параметры Watch.bundle** (выберите и введите **Command + R** переименование):

   ![](settings-images/settings-rename.png "Переименование пакета")

3. Добавьте новый раздел `ApplicationGroupContainerIdentifier` для **Root.plist** со значением, равным эту группу приложений, которые вы настроили, (например) `group.com.xamarin.WatchSettings` в примере):

   [ ![](settings-images/settings-appgroup-sml.png "Добавьте раздел ApplicationGroupContainerIdentifier файла \"root.plist\"")](settings-images/settings-appgroup.png#lightbox)

4. Изменить **Settings-Watch.bundle/Root.plist** должен содержать параметры, которые вы хотите использовать - файле шаблона содержит группу.
  TextField, переключатель и ползунка по умолчанию (который можно удалить и заменить со своими параметрами):

  [![](settings-images/rootplist-sml.png "Изменить Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Использование параметров в приложении для Apple Watch

Чтобы получить доступ к значениям, выбранным пользователем, создайте `NSUserDefaults` экземпляра с помощью групп приложений и указав `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Приложение Apple Watch

[![](settings-images/settings-app-sml.png "Новое приложение Apple Watch на iPhone")](settings-images/settings-app.png#lightbox)

Пользователи будут взаимодействовать с параметрами через новый **Apple Watch** приложения на своих устройствах iPhone. Это приложение позволяет показать или скрыть приложений в Контрольные значения, а также изменить параметры, доступной с помощью **Watch.bundle параметры**.

![](settings-images/applewatch-1.png "Пример параметров приложения") ![](settings-images/applewatch-2.png "пример параметров приложения")



## <a name="related-links"></a>Связанные ссылки

- [Apple doc параметры](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
