---
title: Установка и использование watchOS в Xamarin
description: В этом документе описывается, как установить и использовать watchOS с помощью Xamarin. В нем описывается установка, watchOS проект структуры, как использовать конструктор iOS, интеграция Xcode и предоставляет советы по устранению неполадок.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/05/2017
ms.openlocfilehash: daece8029bd6a97923d6469f9b42d69efbd3f905
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119893"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Установка и использование watchOS в Xamarin

watchOS 4 требуется macOS Sierra (10.12) с Xcode 9.

изначально watchOS 1 требуется OS X Yosemite (10.10) с Xcode 7.

> [!WARNING]
> [обновления watchOS 1 не будет работать после 1 апреля 2018](https://developer.apple.com/news/?id=11162017a). Будущие обновления необходимо использовать watchOS 2 пакета SDK или более поздней версии; построение с watchOS 4 SDK рекомендуется использовать.

## <a name="project-structure"></a>Структура проекта

Приложение просмотра состоит из трех проектов:

- **Проект приложения Xamarin.iOS iPhone** -это обычный iPhone проект, это может быть любой из шаблонов Xamarin.iOS. Приложение просмотра и его расширение будет объединяться внутри этого основного проекта.

- **Проект расширения Watch** -это содержит код (например, классы контроллера) для приложения Watch.

- **Проект приложения Watch** -это содержит файл раскадровки пользовательского интерфейса с ресурсами пользовательского интерфейса для приложения Watch.

[Пример каталога контрольного значения Kit](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) решение выглядит в Xamarin.Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![](installation-images/catalog-solution.png "Решения в Visual Studio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "Решения в Visual Studio")

-----

Скачайте и запустите [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) образец, чтобы приступить к работе.
Экраны из примера можно найти на [элементов управления](~/ios/watchos/user-interface/index.md) страницы.


## <a name="creating-a-new-project"></a>Создание нового проекта

Не удается создать новое «Контрольные значения решение»... довольно приложение просмотра можно добавить в существующее приложение iOS. Выполните следующие действия, чтобы создать приложение просмотра.

1. Если у вас нет существующего проекта, сначала выберите **файл > новое решение** и создание приложения iOS (например, **приложение с одним представлением**):

    [![](installation-images/cycle8-2-sml.png "Выберите Файл > новое решение и создание приложения iOS")](installation-images/cycle8-2.png#lightbox)

2. После создания приложения iOS (или вы планируете использовать существующее приложение iOS), щелкните правой кнопкой мыши решение и выберите **Добавить > Добавить новый проект...** . В **новый проект** выберите окно **watchOS > приложение > приложение WatchKit**:

    [![](installation-images/cycle8-6-sml.png "Выберите watchOS > приложение > приложение WatchKit")](installation-images/cycle8-6.png#lightbox)

3. Следующий экран позволяет выбрать, какой проект приложения iOS должны включать приложении для Apple watch:

    [![](installation-images/cycle8-7-sml.png "Выбрать, какой проект приложения iOS должны включать приложении для Apple watch")](installation-images/cycle8-7.png#lightbox)

4. Наконец, выберите расположение для сохранения проекта (и при необходимости при включении системы управления версиями):

    [![](installation-images/cycle8-8-sml.png "Выберите расположение для сохранения проекта")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio для Mac автоматически настраивает [ссылки проекта и **Info.plist** параметры](~/ios/watchos/get-started/project-references.md) для вас.

## <a name="creating-the-watch-user-interface"></a>Создание пользовательского интерфейса контрольных значений

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Использовании конструктора iOS Xamarin

Дважды щелкните приложение watch **Interface.storyboard** для редактирования с помощью конструктора iOS. Контроллеры интерфейса и элементов управления пользовательского интерфейса можно перетащить на раскадровку в **элементов** и настроить их с помощью **свойства** панели:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "Раскадровки в конструкторе")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "Раскадровки в конструкторе")](installation-images/iosdesigner-vs.png#lightbox)

-----

Следует предоставить каждый новый контроллер интерфейса **класс** , выбрав его и введя имя в **свойства** панели (это будет создать необходимые C# фонового кода файлы автоматически):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Присвойте каждый новый контроллер интерфейс класса")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Присвойте каждый новый контроллер интерфейс класса")

-----

Создание переходов по **Ctrl + перетаскивание** от кнопки, таблицы или интерфейса контроллера на другой контроллер.


### <a name="using-xcode-on-the-mac"></a>С помощью Xcode на компьютере Mac

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Можно продолжать использовать Xcode для создания пользовательского интерфейса, щелкнув файл Interface.storyboard и выбрав **открыть с помощью > конструктора Interface Builder Xcode**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Пользователи Visual Studio также можно использовать конструктор, чтобы создавать собственный пользовательский интерфейс, выполнив переключение напрямую использовать узел сборки Mac.
Откройте решение в Visual Studio для Mac, а затем правой кнопкой мыши файл Interface.storyboard и выберите **открыть с помощью > конструктора Interface Builder Xcode**:

-----

![](installation-images/openwith-xcode.png "Открытие Interface.storyboard в построителе интерфейса Xcode")

Если с помощью Xcode, вы должны следовать те же действия для приложения watch, как и для нормального [раскадровки iOS приложения](~/ios/user-interface/storyboards/index.md) (таких как создание переменных экземпляров и действий по **Ctrl + перетаскивание** в **.h**заголовочный файл).

При сохранении раскадровки в построителе интерфейса Xcode, автоматически добавит переменных экземпляров и действий, создаваемых для C# **. designer.cs** файлы в проекте расширения контрольных значений.


### <a name="adding-additional-screens-in-xcode"></a>Добавление дополнительных экранов в Xcode

При добавлении дополнительных экранов (выходящие в шаблоне по умолчанию) в раскадровку с помощью конструктора Interface Builder Xcode **необходимо вручную добавить C# файлы кода** для каждого нового контроллера интерфейса.

Ссылаться на [Дополнительные инструкции по добавлению новых контроллеров интерфейс раскадровки,](~/ios/watchos/troubleshooting.md#add).

*Конструктор iOS Xamarin делает это автоматически, вручную действия не требуются.*


## <a name="building"></a>Сборка

Как и другие проекты iOS построения проекта, содержащего приложение просмотра. Процесс построения приведет к приложение iPhone (App), который содержит расширение просмотра (.appex), который в свою очередь содержит контрольные значения меньше кода приложение (App).


## <a name="launching"></a>Запуск

Вы можете запустить приложения watch в симуляторе, с помощью Visual Studio для Mac или Visual Studio (запускается на узел сборки Mac).

Существует два режима для запуска приложения WatchKit:

 - Обычное приложение режиме (по умолчанию), и
- [Уведомления](~/ios/watchos/platform/notifications.md) (требуется полезные данные уведомления теста в формате JSON).

### <a name="xcode-8-support"></a>Поддержка Xcode 8

После установки Xcode 8 (или более поздней версии), Apple Watch симуляторов отделены от iOS симуляторов (в отличие от [Xcode 6](#xcode6), где бы они были указаны как *внешнему дисплею*).
При выборе проекта приложения Watch и сделайте его запускаемым проектом, в симуляторе списке будут показаны *iOS симуляторов* сновером (как показано ниже).

[![](installation-images/xs-xcode8-watchos3-sml.png "Выбор типа симулятора")](installation-images/xs-xcode8-watchos3.png#lightbox)

При запуске отладки, *два* симуляторов следует запустить - симулятор iOS *и* Симулятору Apple Watch. Используйте **команда + Shift + H** перейдите к меню и clock циферблате; и использовать **оборудования** меню, чтобы задать **Force Touch давление**. Прокрутки на мыши или сенсорной панели будет имитировать с помощью цифровых корона.

#### <a name="troubleshooting"></a>Устранение неполадок

Следующая ошибка появится в **выходные данные приложения** при попытке запустить симулятор, не поддерживает парных контрольных значений:

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Ссылаться на [форумы Apple](https://forums.developer.apple.com/thread/7783) инструкции по настройке симуляторов, если значения по умолчанию не работают.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 и watchOS 1

Необходимо убедиться в *проект расширения watch* **запускаемым проектом** перед запуском или отладкой приложения. Не удается «запустить» самого приложения watch и при выборе приложения iOS запустится в обычном режиме, в симуляторе iOS.

По умолчанию приложение просмотра запускается в обычном режиме **приложения** режиме (режим уведомлений или не сразу) из Visual Studio для Mac **запуска** или **Отладка** команды.

При использовании Xcode 6 только iPhone 5, 5 iPhone, iPhone 6 и iPhone 6 Plus можно активировать для любого внешнего дисплея **Apple Watch - 38 мм** или **Apple Watch - 42 мм** где будет приложения watch отображается.

**Примечание:** помните, что на экране Контрольное значение не отображается автоматически в симуляторе iOS при использовании Xcode 6.
Используйте **оборудования > внешних отображает** меню, чтобы показывать окно контрольных значений.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Запуск режим уведомлений

Ссылаться на [страница "уведомления"](~/ios/watchos/platform/notifications.md) сведения как для обработки уведомлений в коде.


Visual Studio для Mac можно запустить приложение watch с уведомлением _режимов запуска_ для уведомлений:



Щелкните правой кнопкой мыши на проекте приложения watch и выберите **запуска с > Настраиваемая конфигурация...** :


[![](installation-images/runwith-customparams-sml.png "Выполнение настраиваемой конфигурации")](installation-images/runwith-customparams.png#lightbox)


Откроется **пользовательских параметров** окно, в котором можно выбрать **уведомления** (и предоставить полезные данные JSON), нажмите клавишу **запуска** для запуска приложения watch в симуляторе:


[![](installation-images/runwith-execargs-sml.png "Настройка уведомлений и полезных данных")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Отладка

Отладка поддерживается в Visual Studio для Mac и Visual Studio.
Не забудьте указать файл JSON уведомления при отладке в режиме уведомлений. На этом снимке экрана показана точка останова отладки попадании в приложение просмотра:

![](installation-images/debug-sml.png "На этом снимке экрана показана точка останова отладки попадании в приложении для Apple watch")

После запуска инструкций вы получите приложение для Apple watch на **iOS Simulator (Watch)**.
Режим уведомлений можно выбрать **Отладка > Открыть журнал системы** (**CMD + /**) и использовать `Console.WriteLine` в коде.

### <a name="debugging-lifecycle-event-handlers"></a>Отладка обработчики событий жизненного цикла

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

Файлы шаблонов watchOS (такие как `InterfaceController`, `ExtensionDelegate`, `NotificationController`, и `ComplicationController`) поставляются с их методов требуется жизненного цикла, которые уже реализованы. Добавить `Console.WriteLine` вызовов "и" чтение **выходные данные приложения** чтобы лучше понять жизненный цикл события.



## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Первое приложение просмотра видео](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Советы по WatchKit Apple](https://developer.apple.com/watchkit/tips/)
