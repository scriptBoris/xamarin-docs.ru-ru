---
title: Введение в 3D Touch в Xamarin.iOS
description: В этой статье описывается, как использовать трехмерные сенсорные жесты, представленная iPhone версии 6s и iPhone 6s Plus. Эти жесты включить чувствительность к нажатию, считывания и pop и быстрые действия.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 60751437b891579c97acee0e032defcca2b510f6
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233869"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Введение в 3D Touch в Xamarin.iOS

_В этой статье описывается использование нового iPhone версии 6s и iPhone 6s Plus 3D Touch жесты в вашем приложении._

[![](3d-touch-images/info01.png "Примеры 3D Touch приложениями с включенной поддержкой")](3d-touch-images/info01.png#lightbox)

В этой статье содержатся и общие сведения об использовании новых 3D Touch интерфейсов API, добавляемый давление конфиденциальных жесты приложений Xamarin.iOS, запущенных на новом iPhone 6s и iPhone 6s Plus устройств.

С помощью 3D Touch приложения iPhone теперь имеет возможность не только сообщить, что пользователь касается экрана устройства, но она будет доступна для определения давление, усиливая пользователя и реагировать на них уровни различных давления.

3D Touch предоставляет следующие возможности для приложения:

- [Давление чувствительности](#Pressure-Sensitivity) - приложений теперь можно измерить потребляемую или свет пользователь касается экрана и пользоваться преимуществами этой информации.
  Например рисования приложение может сделать толще или тонкие основываться на потребляемую пользователь касается экрана строки.
- [Просмотр и открытие в окне](#Peek-and-Pop) -приложения теперь можно разрешить пользователю взаимодействовать с данными без необходимости перехода из текущего контекста. Нажав жестких на экране экрана, их можно считывать элемент, который их интересует работать (например, предварительный просмотр сообщения). Нажав сложнее, они может быть взято в элемент.
- [Быстрые действия](#Quick-Actions) -представить из Быстрые действия как контекстные меню, которые могут быть извлекается доступ при щелчке элемента в настольном приложении.
  С помощью быстрых действий, можно добавить ярлыки функций в приложении непосредственно из значка приложения на начальном экране.
- [Тестирование 3D Touch в симуляторе](#Testing-3D-Touch-in-the-Simulator) -необходимое оборудование Mac, позволяет тестировать приложения 3D поддержка сенсорного ввода в симуляторе iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Чувствительность к нажатию

Как уже говорилось, с помощью нового свойства [UITouch](xref:UIKit.UITouch) класса можно измерить степень давления, пользователь применяет для экрана устройства iOS и использовать эту информацию в пользовательском интерфейсе. Например делая это мазок кисти более прозрачные или непрозрачные зависимости на степень давления.

[![](3d-touch-images/pressure01.png "Это мазок кисти, к просмотру как более прозрачные или непрозрачные зависимости от объема памяти")](3d-touch-images/pressure01.png#lightbox)

В результате 3D Touch, если ваше приложение работает в iOS 9 (или более поздней) и устройство iOS может вспомогательных 3D Touch, вызовет изменения замедленной `TouchesMoved` возникает событие.

Например, при мониторинге `TouchesMoved` событие [UIView](xref:UIKit.UIView), для получения текущего давление, пользователь применяет для экрана можно использовать следующий код:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

`MaximumPossibleForce` Свойство возвращает наибольшее возможное значение для `Force` свойство [UITouch](xref:UIKit.UITouch) на устройства iOS, на котором приложение выполняется на основе.

> [!IMPORTANT]
> Изменения замедленной приведут к `TouchesMoved` событие, даже если X / Y-координаты не изменились. Из-за этого изменения в поведении приложения iOS должны быть подготовлены для `TouchesMoved` событий, вызываемый чаще и для X / Y-координаты точки должен совпадать с последнего `TouchesMoved` вызова.




Дополнительные сведения см. в разделе Apple [TouchCanvas: С помощью UITouch быстрого и эффективного](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) пример приложения и [ссылки на класс UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Просмотр и открытие в окне

3D Touch обеспечивает новые возможности для взаимодействия с информацией в приложении, быстрее, чем когда-либо, без необходимости перехода из текущего расположения пользователя.

Например, если приложение отображает таблицу сообщений, пользователь может нажать жестких элемент, чтобы просмотреть его содержимое в виде наложения (которое Apple называется *Показать*).

[![](3d-touch-images/peekandpop01.png "В примере из Просмотр содержимого")](3d-touch-images/peekandpop01.png#lightbox)

Если пользователь нажимает сложнее, он вводит представление обычное сообщение (который называется *Pop*-ping в представление).

### <a name="checking-for-3d-touch-availability"></a>Проверка доступности 3D Touch

При работе с [UIViewController]() поддерживает ли приложение выполняется на устройстве iOS 3D Touch можно использовать следующий код:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Этот метод может быть вызван перед *или после* `ViewDidLoad()`. 

### <a name="handling-peek-and-pop"></a>Обработка Peek и Pop

На устройстве iOS, который может обрабатывать 3D Touch, можно использовать экземпляр `UIViewControllerPreviewingDelegate` класс для управления отображением **Показать** и **Pop** элемента сведений. Например, если бы нам контроллер представления таблицы с именем `MasterViewController ` нам удалось использовать следующий код для поддержки **Показать** и **Pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

`GetViewControllerForPreview` Метод используется для выполнения **Показать** операции. Он получает доступ к данным ячейки и архивация таблиц, а затем загружает `DetailViewController` из текущей раскадровки. Установив `PreferredContentSize` (0,0) мы запрашиваем сведения об по умолчанию **Показать** размер представления. Наконец, мы все, кроме ячейки, мы при отображении с размытия `previewingContext.SourceRect = cell.Frame` и возвращается новое представление для отображения.

`CommitViewController` Повторно использует представление, мы создали в **Показать** для **Pop** просмотра, когда пользователь нажимает сложнее.

### <a name="registering-for-peek-and-pop"></a>Регистрация для считывания и Pop

От контроллера представления, чтобы предоставить пользователю **Показать** и **Pop** элементы из, нам нужно зарегистрировать для этой службы. В примере, приведенном выше контроллера представления таблиц (`MasterViewController`), мы используем следующий код:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Здесь мы называем `RegisterForPreviewingWithDelegate` метода с экземпляром `PreviewingDelegate` созданных ранее. На устройствах iOS, поддерживающих 3D Touch пользователь может нажать жестких элемент, чтобы показать его. Если они еще сложнее клавишу, элемент откроется в него стандартный отображения представления.

Дополнительные сведения см. в разделе наших [iOS 9 образец ApplicationShortcuts](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) и Apple [ViewControllerPreviews: С помощью UIViewController, предварительный просмотр интерфейсов API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) пример приложения [ссылки на класс UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [ссылки на класс UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) и [UIPreviewActionItem Справочник по протоколу](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Быстрые действия

С помощью 3D Touch и быстрые действия, можно добавить общие, быстрый и простой для быстрого доступа к функциям в приложении из значок экрана главной страницы на устройстве iOS.

Как уже говорилось, быстрые действия можно представить как контекстные меню, которые могут быть извлекается доступ при щелчке элемента в настольном приложении. Быстрые действия следует использовать для предоставления ярлыки наиболее распространенные функции и компоненты приложения.

[![](3d-touch-images/quickactions01.png "Пример меню быстрых действий")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Определение статического Быстрые действия

Если один или несколько быстрых действий, необходимых для приложения являются статическими и не нужно изменять, их можно определить в приложении `Info.plist` файл. Измените этот файл во внешнем редакторе и добавьте следующие разделы:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Здесь определяются два статических элементов быстрое действие со следующими ключами:

- `UIApplicationShortcutItemIconType` -Определяет значок, который будет отображаться элемент с указанным быстрое действие как один из следующих значений:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` -Определяет подзаголовок для элемента.
* `UIApplicationShortcutItemTitle` -Определяет заголовок для элемента.
* `UIApplicationShortcutItemType` — Строковое значение, мы будем использовать для идентификации элемента в приложении. Дополнительные сведения см. в следующем разделе.

> [!IMPORTANT]
> Быстрый пункты контекстного действия, которые заданы в `Info.plist` файл недоступен с `Application.ShortcutItems` свойство. Они только передаются в `HandleShortcutItem` обработчик событий. 





### <a name="identifying-quick-action-items"></a>Идентификация элементов быстрое действие

Как было показано выше, при определении элементов быстрое действие в вашем приложении `Info.plist`, назначенный строковое значение для `UIApplicationShortcutItemType` ключа для их идентификации.

Чтобы упростить эти идентификаторы для работы в код, добавьте класс с именем `ShortcutIdentifier` в приложение проекта и это должно выглядеть следующим образом:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>Быстрые действия обработки

Затем вам потребуется изменить приложения `AppDelegate.cs` -файл для обработки пользователя, выбирающего элемент быстрое действие из значка приложения на начальном экране.

Внесите следующие изменения:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

Во-первых, определим открытый `LaunchedShortcutItem` свойства для отслеживания последнего быстрое действие элемент, выбранный пользователем. Затем мы переопределяем `FinishedLaunching` метод и см. Если `launchOptions` были переданы и если они содержит элемент быстрое действие. Если это так, мы храним быстрое действие в `LaunchedShortcutItem` свойство.

Далее мы переопределяем `OnActivated` метод и передать, выбран любой элемент на панели быстрого запуска для `HandleShortcutItem` метод, чтобы на них реагировать. В настоящее время мы только записываем сообщения **консоли**. В реальном приложении что любой действие требовалось обрабатывается. После снимка действие `LaunchedShortcutItem` свойство очищается.

Наконец, если приложение уже была запущена, `PerformActionForShortcutItem` метод будет вызываться для обработки элемента быстрое действие, поэтому нам нужно переопределить его и вызовите наши `HandleShortcutItem` метод таким же образом.


### <a name="creating-dynamic-quick-action-items"></a>Создание динамических быстрое действие элементов

Кроме определения статического быстрое действие элементы в вашем приложении `Info.plist` файл, можно создать динамической на ходу быстрых действий. Чтобы определить два новых динамических быстрых действий, отредактируйте вашей `AppDelegate.cs` файл еще раз и изменение `FinishedLaunching` метод для поиска как в следующем:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

Теперь выполняется проверка на предмет `application` уже содержит набор динамически созданные `ShortcutItems`, если это не так мы создадим две новые `UIMutableApplicationShortcutItem` объектов, чтобы определить новые элементы и добавить их к `ShortcutItems` массива.

Код мы уже добавили в [быстрого действия обработки](#Handling-a-Quick-Action) выше раздел будет обрабатывать эти динамические Быстрые действия, так же, как статических адресов.

Следует отметить, что можно создать сочетание статических и динамических элементов быстрое действие (как мы это делаем здесь), вы не ограничены одно из них.

Дополнительные сведения см. наш [iOS 9 образец ViewControllerPreview](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) и Apple см. в разделе [ApplicationShortcuts: С помощью UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) пример приложения [ссылки на класс UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [ссылки на класс UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) и [ Ссылки на класс UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Тестирование 3D Touch в симуляторе

При с помощью последней версии Xcode и симулятора iOS на Mac, совместимый с Force Touch включить у вас сенсорная панель, можно проверить функциональность 3D Touch в симуляторе.

Чтобы включить эту функцию, запуск любых приложений в имитации iPhone оборудование, поддерживающее 3D Touch (iPhone версии 6s и выше). Затем выберите **оборудования** меню в iOS Simulator и включите **силой сенсорной панели используйте 3D touch** пункта меню:

[![](3d-touch-images/simulator01.png "Выберите в меню оборудования в симуляторе iOS и включите принудительного использования сенсорной панели для пункта меню 3D touch")](3d-touch-images/simulator01.png#lightbox)

С помощью этой функции active можно нажать становится все сложнее на сенсорной панели компьютера Mac включить 3D Touch так же, как и на iPhone на реальном оборудовании.

## <a name="summary"></a>Сводка

В этой статье был представлен новый 3D Touch API, доступным в iOS 9 для iPhone 6s и iPhone 6s Plus. Статья описывает добавление чувствительность к нажатию в приложение; с помощью считывания и Pop для быстрого отображения в приложении сведения из текущего контекста без навигации; и с помощью быстрых действий для предоставления сочетания клавиш в приложение наиболее часто используемые функции.



## <a name="related-links"></a>Связанные ссылки

- [iOS 9 ViewControllerPreview образца](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts образца](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Подготовьтесь к приложения iPhone 3D Touch](https://developer.apple.com/ios/3d-touch/)
