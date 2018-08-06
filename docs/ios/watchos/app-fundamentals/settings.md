---
title: Работа с watchOS параметров в Xamarin
description: В этом документе описывается работа с параметрами watchOS в Xamarin. В нем описывается добавление параметров в решение приложения Контрольные значения, с помощью этих параметров в Apple Watch приложения и приложения для iPhone.
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 6cfbcf3b4383588819490838c2a54cdb4faf9403
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790881"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>Работа с watchOS параметров в Xamarin

Apple Watch приложений можно использовать те же параметры функции как приложения iOS - параметры пользовательского интерфейса отображается в **Apple Watch** приложение для iPhone, но значения доступны в приложении для iPhone и также расширение контрольных значений.

![](settings-images/intro.png "Apple Watch приложения могут использовать те же параметры функции как приложения iOS")

Параметры сохраняются в общую папку, доступную для приложения iOS и расширения приложения контрольных значений, определяемых **группы приложения**. Вы должны [настройки группы приложений](~/ios/watchos/app-fundamentals/app-groups.md) перед добавлением параметров согласно приведенным ниже инструкциям.

## <a name="add-settings-in-a-watch-solution"></a>Добавление параметров в решении Контрольное значение

В **приложение для iPhone** в решении (*не* Контрольное значение приложении или расширения):

1. Щелкните правой кнопкой мыши **Добавить > новый файл...**  и выберите **Settings.bundle** (нельзя изменить имя в **новый файл** диалогового окна):

   [![](settings-images/settings-add-sml.png "Добавить новый набор параметров")](settings-images/settings-add.png#lightbox)

2. Измените имя на **параметры Watch.bundle** (выберите и введите **команда + R** переименование):

   ![](settings-images/settings-rename.png "Переименование пакета")

3. Добавьте новый раздел `ApplicationGroupContainerIdentifier` для **Root.plist** со значением, заданным в группу приложений, настройки, (например) `group.com.xamarin.WatchSettings` в данном образце):

   [ ![](settings-images/settings-appgroup-sml.png "Добавить ключ ApplicationGroupContainerIdentifier Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. Изменить **Settings-Watch.bundle/Root.plist** содержит параметры, которые вы хотите использовать - файл шаблона содержит группы.
  TextField, переключатель и ползунок по умолчанию (который можно удалить и заменить со своими параметрами):

  [![](settings-images/rootplist-sml.png "Изменить Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>Использование параметров в приложение Watch

Чтобы получить доступ к значениям, выбранным пользователем, создайте `NSUserDefaults` экземпляра с помощью приложения групп и указание `NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Приложения для Apple Watch

[![](settings-images/settings-app-sml.png "Новое приложение для iPhone Apple Watch")](settings-images/settings-app.png#lightbox)

Пользователи смогут с параметрами через новое **Apple Watch** приложения на их iPhone. Это приложение позволяет пользователю отображать приложений на контрольные значения, а также изменить параметры, предоставляемые с помощью **Watch.bundle параметры**.

![](settings-images/applewatch-1.png "Пример параметров приложения") ![](settings-images/applewatch-2.png "пример параметров приложения")



## <a name="related-links"></a>Связанные ссылки

- [Параметры doc Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
