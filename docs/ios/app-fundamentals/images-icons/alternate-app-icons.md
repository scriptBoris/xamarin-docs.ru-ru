---
title: Альтернативные значки приложений в Xamarin.iOS
description: В этом документе описывается, как использовать альтернативные значки приложений в Xamarin.iOS. Он описывает, как добавить эти значки в проект Xamarin.iOS, как изменить файл Info.plist и как программно управлять значка приложения.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: cc5052c8988a27605cf7680a3853f80e7afd38b7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105497"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Альтернативные значки приложений в Xamarin.iOS

_В этой статье рассматривается использование альтернативные значки приложений в Xamarin.iOS._

Apple добавлено несколько улучшений iOS 10.3, разрешить приложение для управления его значок:

 - `ApplicationIconBadgeNumber` — Получает или задает эмблему на значок приложения в Springboard.
 - `SupportsAlternateIcons` Если `true` приложение имеет дополнительный набор значков.
 - `AlternateIconName` — Возвращает имя выбранного альтернативный значок или `null` при использовании основной значок.
 - `SetAlternameIconName` -Используйте этот метод переключиться на альтернативный значок заданного значка приложения.

![](alternate-app-icons-images/icons04.png "Пример оповещения, когда приложение изменяет его значок")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Добавление альтернативного значков в проект Xamarin.iOS

Чтобы разрешить приложению переключиться на альтернативный значок, коллекция изображений значков необходимо включить в проект приложения Xamarin.iOS. Эти образы не может добавляться в проект с помощью стандартной `Assets.xcassets` метод, они должны быть добавлены к **ресурсы** папки напрямую.

Выполните следующие действия:

1. Выберите значок "обязательно" образы в папке, выберите все и перетащите их в **ресурсы** папку в **обозревателе решений**:

    ![](alternate-app-icons-images/icons00.png "Выберите значки образы из папки")

2. При появлении запроса, выберите **копирования**, **использовать одинаковое действие для всех выбранных файлов** и нажмите кнопку **ОК** кнопки:

    ![](alternate-app-icons-images/icons02.png "Добавление файла в папке диалоговое окно")

3. **Ресурсы** папки должна выглядеть следующим образом, после завершения:

    ![](alternate-app-icons-images/icons01.png "Папку «ресурсы» должен выглядеть следующим образом")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Изменение файла Info.plist

С помощью необходимых образов, добавленных к **ресурсы** папке [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) ключ будет необходимо добавить в проект **Info.plist** файл. Этот ключ будет определить имя значок "Создать" и образы, из которых он состоит.

Выполните следующие действия:

1. В **Обозревателе решений** дважды щелкните файл **Info.plist**, чтобы открыть его для редактирования.
2. Переключиться в режим **источника** представления.
3. Добавить **объединить значки** ключа и оставить **тип** присвоено **словарь**.
4. Добавить `CFBundleAlternateIcons` ключа и задайте **тип** для **словарь**.
5. Добавить `AppIcon2` ключа и задайте **тип** для **словарь**. Это будет имя нового набора значков альтернативные приложения.
6. Добавить `CFBundleIconFiles` ключа и задайте **тип** для **массива**
7. Добавить новую строку в `CFBundleIconFiles` массива для каждого значка файла без расширения и `@2x`, `@3x`, суффиксы и т.д. (пример `100_icon`). Повторите этот шаг для каждого файла, составляющие набор альтернативных значков.
8. Добавить `UIPrerenderedIcon` ключа `AppIcon2` словаря, задайте **тип** для **логическое** и значение для **нет**.
9. Сохраните изменения в файле.

Полученный в результате **Info.plist** файл должен выглядеть следующим образом, после завершения:

![](alternate-app-icons-images/icons03.png "Законченный файл Info.plist")

Или так, если открыть в текстовом редакторе:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Управление значка приложения 

С помощью значка образов, включенных в проекте Xamarin.iOS и **Info.plist** файл правильно настроены, разработчик может использовать один из многих новых функций, добавленных на устройства iOS 10.3 для управления значка приложения.

`SupportsAlternateIcons` Свойство `UIApplication` класса позволяет разработчику определить, поддерживает ли приложение альтернативные значки. Пример:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

`ApplicationIconBadgeNumber` Свойство `UIApplication` класса позволяет разработчику требуется получить или задать текущее количество эмблему на значок приложения в Springboard. Значение по умолчанию равно нулю (0). Пример:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

`AlternateIconName` Свойство `UIApplication` класс позволяет разработчику получить имя значка текущий выбранный альтернативный приложения или возвращается `null` Если приложение использует основной значок. Пример:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

`SetAlternameIconName` Свойство `UIApplication` класс разработчик может изменить значок приложения. Передайте имя этого значок для выбора или `null` чтобы вернуться к основной значок. Пример:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

При запуске приложения и пользователю выбрать альтернативный значок, появится предупреждение следующим образом:

![](alternate-app-icons-images/icons04.png "Пример оповещения, когда приложение изменяет его значок")

Если пользователь переключается обратно в основной значок, появится предупреждение следующим образом:

![](alternate-app-icons-images/icons05.png "Пример оповещения при изменении приложения основного значка")

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается добавление в проект Xamarin.iOS альтернативные значки приложений и их использование в приложении.



## <a name="related-links"></a>Связанные ссылки

- [iOSTenThree образца](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
