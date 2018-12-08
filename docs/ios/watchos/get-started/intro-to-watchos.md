---
title: Введение в watchOS
description: Этот документ содержит обзор watchOS, описывающий жизненного цикла приложения, типы интерфейсов пользователя, размеры экранов, ограничения и многое другое.
ms.prod: xamarin
ms.assetid: 99c316d6-6707-40f6-bec9-801d05888759
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: ba5e7a24524f9371cbd810e18c11acc9e2e2a4cb
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055625"
---
# <a name="introduction-to-watchos"></a>Введение в watchOS

> [!NOTE]
> Ознакомьтесь с [введение в watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md) обзор новых функций.

## <a name="about-watchos"></a>О watchOS

Решение приложение watchOS содержит 3 проектов:

- **Просмотрите расширения** — проект, содержащий код приложения watch.
- **Просмотрите приложение** — раскадровки пользовательского интерфейса и ресурсов.
- **Родительское приложение iOS** — это приложение является обычной iPhone. Приложение просмотра и расширение объединяются в iPhone для доставки часы пользователя.

В watchOS 1 приложения код расширения выполняется на iPhone, Apple Watch, фактически внешнему дисплею. приложения для watchOS 2 и 3 выполнять полностью в Apple Watch. На следующей схеме показано это различие:

[ ![](intro-to-watchos-images/arch-sml.png "На этой диаграмме отображается разница между watchOS 1 и watchOS 2 (и более)")](intro-to-watchos-images/arch.png#lightbox)

Независимо от того, какую версию watchOS предназначено в Visual Studio для Mac в панели решения законченное решение будет выглядеть следующим образом:

[![](intro-to-watchos-images/projectstructure-sml.png "Панель решения")](intro-to-watchos-images/projectstructure.png#lightbox)

*Родительское приложение* в watchOS решение — это приложение регулярных операций ввода-вывода. Это единственный проект в решение, которое является видимым **на телефоне**. Варианты использования для этого приложения будет включать в руководствах, экраны администрирования и средний уровень фильтрации, cacheing и т. д. Тем не менее, пользователь может устанавливать и запускать приложения watch/расширения без **когда-нибудь** с открытыми в родительское приложение, поэтому при необходимости в родительское приложение на выполнение для однократной инициализации или администрирования, необходимо запрограммировать на watch приложения и расширения, информирующее пользователя.

Несмотря на то, что в родительское приложение предоставляет приложение просмотра и расширение, они выполняются в разные «песочницы».

В watchOS 1 они могут обмениваться данными через группу общего приложения или с помощью статической функции `WKInterfaceController.OpenParentApplication`, который будет активировать `UIApplicationDelegate.HandleWatchKitExtensionRequest` метод в приложении родительского `AppDelegate` (см. в разделе [работа с в родительское приложение](~/ios/watchos/app-fundamentals/parent-app.md)).

В watchOS 2 или более поздней версии платформы подключения Контрольное значение используется для связи с родительским приложением с помощью `WCSession` класса.

## <a name="application-lifecycle"></a>Жизненный цикл приложения

В расширении watch подкласс `WKInterfaceController` класс создается для каждой сцены раскадровки.

Эти `WKInterfaceController` классы являются аналогом `UIViewController` объекты в программировании операций ввода-вывода, но не имеют тот же уровень доступа к представлению.
К примеру нельзя динамически добавлять элементы управления к или реструктуризации пользовательского интерфейса.
Можно, тем не менее, скрыть и Показать элементы управления и, в некоторых элементах управления, изменить их размер, прозрачность и параметры внешнего вида.

Жизненный цикл `WKInterfaceController` объект включает в себя следующие вызовы:

- [Переходит в спящий режим](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.Awake/) : в этом методе необходимо выполнить большую часть инициализации.
- [WillActivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.WillActivate/) : вызывается незадолго до приложении для Apple Watch отображения пользователю. Используйте этот метод для выполнения последнего момента инициализации, запуска анимации и т. д.
- На этом этапе появится в приложении для Apple Watch и расширение начинает отвечать на запросы для ввода данных пользователем и обновление отображение приложения Watch на логике приложения.
- [DidDeactivate](https://developer.xamarin.com/api/member/WatchKit.WKInterfaceController.DidDeactivate/) после Watch приложения закрыто пользователем, этот метод вызывается. После возврата этого метода, элементы управления пользовательского интерфейса не может изменяться до следующего `WillActivate` вызывается. Этот метод также будет вызываться при разрыве подключения для iPhone.
- После отключения расширения оно является недоступным для программы. Ожидающие асинхронные функции **не** вызываться. Просмотрите набор расширений не используют фоновую обработку режимов. Если программа активируется пользователем, но приложение не было завершено в операционной системе, будет первый метод, вызываемый `WillActivate`.

![](intro-to-watchos-images/wkinterfacecontrollerlifecycle.png "Обзор жизненного цикла приложения")

## <a name="types-of-user-interface"></a>Типы пользовательского интерфейса

Существует три типа взаимодействия, которую пользователь может иметь с приложение для Apple watch.
Все программируются с помощью пользовательских вложенных классов `WKInterfaceController`, поэтому универсально применяется последовательность описанные выше жизненного цикла (уведомления программируются с помощью вложенных классов `WKUserNotificationController`, который сам является подклассом `WKInterfaceController`):

### <a name="normal-interaction"></a>Обычный взаимодействия

Большая часть взаимодействия Контрольные значения/расширение приложения будет более вложенные классы `WKInterfaceController` , написанный в соответствии с сцен в приложении watch **Interface.storyboard**. Это подробно рассматривается в [установки](~/ios/watchos/get-started/installation.md) и [Приступая к работе](~/ios/watchos/get-started/index.md) статей.
На следующем рисунке показана часть [каталога контрольного значения Kit](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) образца раскадровки. Для каждой сцены, показали здесь, имеется соответствующий пользовательский `WKInterfaceController` (`LabelDetailController`, `ButtonDetailController`, `SwitchDetailController`т. д.) в проекте расширения.

![](intro-to-watchos-images/scenes.png "Примеры взаимодействия норм.")

### <a name="notifications"></a>Уведомления

[Уведомления](~/ios/watchos/platform/notifications.md) являются основной вариант использования для Apple Watch. Поддерживаются локальные и удаленные уведомления. Взаимодействие с уведомлениями, происходит в два этапа, вызывается короткое и длинное вид.

Короткий выглядит кратко, отображаться значок приложения, его имя и название (как указано с `WKInterfaceController.SetTitle`).

Long выглядеть объединяет предоставляемая системой **переплета** области и кнопки закрытия с пользовательское содержимое на основе раскадровки.

`WKUserNotificationInterfaceController` расширяет `WKInterfaceController` с методами `DidReceiveLocalNotification` и `DidReceiveRemoteNotification`.
Переопределите эти методы для реагирования на события уведомления.

Дополнительные сведения о разработке пользовательского интерфейса уведомлений, см. [Apple Watch рекомендациям по интерфейсам](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Notifications.html#//apple_ref/doc/uid/TP40014992-CH20-SW1)

![](intro-to-watchos-images/notifications.png "Примеры уведомлений")

## <a name="screen-sizes"></a>Размеры экрана

Apple Watch имеет два размера лиц: 38 мм и 42 мм, как с помощью с соотношением 5:4 для отображения и дисплей Retina. Готовый к применению размеров являются:

- 38 мм: 136 x 170 пикселей на логический (физические пиксели 272 x 340)
- 42 мм: 156 x 195 логических пикселях (физические пиксели 312 x 390).

Используйте `WKInterfaceDevice.ScreenBounds` определить на какие display выполняется приложение для Apple Watch.

Как правило проще разрабатывать разработки текст и макет с более ограниченный отображение 38 мм и увеличить масштаб.
При запуске в среде большего размера, уменьшение масштаба может привести к некрасиво перекрытие или усечения текста.

Дополнительные сведения о [работа с размерами экрана](~/ios/watchos/app-fundamentals/screen-sizes.md).


## <a name="limitations-of-watchos"></a>Ограничения для watchOS

Существуют некоторые ограничения watchOS, которые следует учитывать при разработке приложений для watchOS:

- Apple Watch устройства обеспечивают ограниченную хранилища — помните дискового пространства, перед загрузкой больших файлов (например) аудио- или фильма файлы).

- Многие watchOS [элементов управления](~/ios/watchos/user-interface/index.md) имеют аналоги в UIKit, но различные классы (`WKInterfaceButton` вместо `UIButton`, `WKInterfaceSwitch` для `UISwitch`т. д.) и имеют ограниченный набор методов, по сравнению с их UIKit эквиваленты. Кроме того, watchOS имеет некоторые элементы управления, такие как `WKInterfaceDate` (для отображения даты и времени), UIKit не поддерживает.

  - Нельзя настроить маршрутизацию уведомления только в часы или iPhone только (какому роду элементов управления, пользователь имеет на маршрутизацию не объявлено компанией Apple).

Некоторые другие ограничения и часто задаваемые вопросы:

- Apple не разрешает пользовательские Контрольные значения сторонних лиц.

- API-интерфейсы, которые позволяют просмотреть, чтобы управлять iTunes на подключенный телефон являются закрытыми.


## <a name="further-reading"></a>Дополнительные сведения

Ознакомьтесь с документацией из Apple:

* [Разработка набора контрольных значений](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html#//apple_ref/doc/uid/TP40014969-CH8-SW1)

* [Просмотрите руководство по программированию Kit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/DesigningaWatchKitApp.html)

* [Рекомендации по работе с человеческим интерфейсом Apple Watch](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/index.html#//apple_ref/doc/uid/TP40014992-CH3-SW1)


## <a name="related-links"></a>Связанные ссылки

- [watchOS 3 каталога (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [watchOS 1 каталога (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Установка и настройка](~/ios/watchos/get-started/installation.md)
- [Первое приложение просмотра видео](http://blog.xamarin.com/your-first-watch-kit-app/)
- [В разработке Apple для Watch руководства](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/index.html)
- [Советы по WatchKit Apple](https://developer.apple.com/watchkit/tips/)
- [Введение в watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
