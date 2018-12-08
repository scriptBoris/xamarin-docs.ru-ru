---
title: Работа с watchOS родительское приложение в Xamarin
description: В этом документе описывается, как работать с watchOS родительского приложения в Xamarin. В нем описывается расширений приложения WatchKit, приложений iOS, общее хранилище и многое другое.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 74b9243b1b985cba6d815a673d1abed054eda234
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059951"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Работа с watchOS родительское приложение в Xamarin

> [!IMPORTANT]
> Доступ только с помощью приведенных ниже примерах родительское приложение работает в watchOS 1 приложения watch.


Для обмена данными между приложении для Apple watch и приложение iOS, которое объединяется с разными способами:

- Контрольные значения расширений может [вызвать метод](#code) от родительское приложение, которое выполняется в фоновом режиме на iPhone.

- Контрольные значения расширений может [совместно используют хранилище, доступном](#storage) с приложением iPhone родительского.

- Использование перемещение вручную для передачи данных из обзора или уведомления в приложении для Apple Watch, отправка пользователя к контроллеру определенный интерфейс в приложении.

Родительское приложение, также иногда называется приложения-контейнера.


<a name="code" />

## <a name="run-code"></a>Выполните код

Связи между расширением watch и iPhone в родительское приложение демонстрируется в [GpsWatch пример](https://developer.xamarin.com/samples/GpsWatch).
Расширение контрольных значений можно запросить в родительское приложение iOS, то часть обработки на его имени с помощью `OpenParentApplication` метод.

Это особенно полезно в тех случаях, для долго выполняющихся задач (включая сетевые запросы) - только родительского приложения iOS можно воспользоваться фоновую обработку для выполнения этих задач и сохранения полученных данных в расположении, доступном для расширения контрольных значений.



### <a name="watch-kit-app-extension"></a>Расширение приложения Watch Kit

Вызовите `WKInterfaceController.OpenParentApplication` в расширении приложения watch. Он возвращает `bool` , указывающее, является ли метод запрос был отправлен успешно или нет. Проверьте `error` параметр в ответе `Action` на предмет произошла во время выполнения метода, выполняемому в приложении iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```


### <a name="ios-app"></a>Приложение iOS

Все вызовы из расширения приложения watch направляются через приложения iPhone `HandleWatchKitExtensionRequest` метод.
Если вы вносите различные запросы в приложении для Apple watch, то этот метод потребуется запросить `userInfo` словаря, чтобы определить способ обработки запроса.


```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```


<a name="storage" />

## <a name="shared-storage"></a>Общее хранилище

Если вы настраиваете [групп приложений](~/ios/watchos/app-fundamentals/app-groups.md) то расширения iOS 8 (включая расширения watch) могут обмениваться данными в родительское приложение.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>Параметры NSUserDefaults

Следующий код могут быть написаны на расширение приложения watch и iPhone в родительское приложение таким образом, они могут ссылаться на общий набор `NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>Файлы

Расширение приложения "и" Контрольные значения операций ввода-вывода также могут совместно использовать файлы, используя общий путь к файлу.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Примечание: Если путь является `null` проверьте [конфигурации группы приложений](~/ios/watchos/app-fundamentals/app-groups.md) для обеспечения профили подготовки были настроены правильно и были загружены или не установлены на компьютере разработчика.

Дополнительные сведения см. в разделе [возможностей групп приложений](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) документации.

## <a name="wormholesharp"></a>WormHoleSharp

Популярные механизм открытым исходным кодом для watchOS 1 (на основе [MMWormHole](https://github.com/mutualmobile/MMWormhole)) для передачи данных или команды между в родительское приложение и приложении для Apple watch.

Можно настроить WormHole, с помощью групп приложений следующим образом в приложении iOS и посмотрите расширения:

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Скачайте C# версии [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).



## <a name="related-links"></a>Связанные ссылки

- [GpsWatch (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WormHoleSharp (пример)](https://github.com/Clancey/WormHoleSharp)
- [Справочник по WKInterfaceController Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple обмена данными с содержащего приложение](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
