---
title: watchOS уведомления в Xamarin
description: В этом документе описывается, как работать с уведомления watchOS в Xamarin. В нем описывается создание контроллеров уведомлений, создания уведомлений и тестировании уведомлений.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 53ec1b0777827e7f4faf81110507787c65dfaf63
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103937"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS уведомления в Xamarin

Приложения Watch могут получать уведомления, если их поддерживает содержащего приложения iOS. Нет встроенных уведомлений обработки, поэтому не *требуется* для добавления поддержки дополнительных уведомлений, описанных ниже, тем не менее если вы хотите настроить нужное поведение и внешний вид читайте дальше.

Ссылаться на [iOS уведомления](~/ios/platform/user-notifications/deprecated/index.md) doc, Дополнительные сведения о добавлении поддержка уведомлений в приложение iOS в решении.

## <a name="creating-notification-controllers"></a>Создание контроллеров уведомлений

На раскадровке контроллеры уведомления имеют специальный тип перехода, их активации. При перетаскивании нового **контроллера интерфейс уведомлений** на раскадровка будет иметь присоединенного объекта перехода:

![](notifications-images/notification-storyboard1.png "Новый контроллер интерфейс уведомлений с помощью присоединенного объекта перехода")

Когда уведомление segue выбран можно изменить его свойства:

![](notifications-images/notification-storyboard2.png "Уведомление segue выбранного")

После настройки контроллера, он может выглядеть как в следующем примере из WatchKitCatalog:

![](notifications-images/notifications-segue.png "Свойства уведомлений")


Существует два типа уведомлений:

- **Внешний вид, Short** -непрокручиваемый статического представления, определенные системой.

- **Внешний вид, долго** - прокручиваемым, настраиваемые представления, определенные пользователем! Можно указать более простой и статические версии и более сложные динамические версии.

### <a name="short-look-notification-controller"></a>Short внешний вид, контроллер уведомлений

Внешний вид, short пользовательский Интерфейс состоит из значок приложения, имя приложения и строка заголовка уведомления.

Если пользователь не будет пропускать уведомления, система автоматически переключается на внешний вид, длительно уведомление, с дополнительными сведениями.


### <a name="long-look-notification-controller"></a>Внешний вид долго контроллера уведомлений

Операционная система определяет, для отображения статического или динамического представления на основе ряда факторов. Необходимо указать статический интерфейс и при необходимости можно также включать динамический интерфейс для уведомлений.

#### <a name="static"></a>Static

Статические представление должно быть легко и быстро для отображения.

![](notifications-images/notification-static.png "Статическое представление")

#### <a name="dynamic"></a>Динамический

Динамическое представление может отображать больше данных и обеспечивать более интерактивным.

![](notifications-images/notification-dynamic.png "Динамическое представление")


## <a name="generating-notifications"></a>Создания уведомлений

Уведомления могут поступать с удаленного сервера ([службы Push-уведомлений Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), или APNS) или могут создаваться локально в приложении iOS.

Ссылаться на [Пошаговое руководство по уведомлениям iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) пример создания локальных уведомлений и [WatchNotifications пример](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/) рабочий пример.

Необходимо иметь локальные уведомления `AlertTitle` отображение на Apple Watch - `AlertTitle` строка отображается в интерфейсе внешний вид, Short. Как `AlertTitle` и `AlertBody` отображаются в списке уведомлений; и `AlertBody` интерфейса внешний вид, долго.

На этом снимке экрана показано `AlertTitle` отображаются в списке уведомлений и `AlertBody` долго внешний вид интерфейса (с помощью [пример кода](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "На этом снимке экрана показано AlertTitle, отображаемых в списке уведомлений") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody долго внешний вид интерфейса")

## <a name="testing-notifications"></a>Тестирование уведомления

Уведомления (локальные и удаленные) можно только правильно протестировать на устройстве, однако их можно имитировать с помощью **.json** файл в симуляторе iOS.

### <a name="testing-on-apple-watch"></a>Тестирование производительности на Apple Watch

При тестировании уведомлений на Apple Watch, помните, что [документации Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) указано следующее:

> По прибытии одно из уведомлений о локальном или удаленном вашего приложения на устройстве пользователя iPhone iOS определяет, для отображения этого уведомления на iPhone или на Apple Watch.

Это alluding к тому факту, что iOS решает ли уведомление будет отображаться на устройстве iPhone или в Watch. Если парном iPhone активна при получении уведомления, уведомления, вероятнее всего, отображаемый на iPhone и *не* направляются в часы.

Чтобы убедиться, что уведомление отображается в watch, отключите на экране iPhone (один раз, нажав кнопку питания) или разрешить ему переходят в спящий режим. Если парных Контрольное значение находится в диапазоне, питания и является надет на вас по рукам, уведомления будут направляться существует и включаться в часы (сопровождается слабая).

### <a name="testing-on-the-ios-simulator"></a>Тестирование на симуляторе iOS

Вы *необходимо* предоставляют полезные данные JSON тестирования при тестировании режим уведомлений в симуляторе iOS. Задайте путь **аргументы выполнения пользовательских** окно в Visual Studio для Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Visual Studio для Mac будет отображаются дополнительные параметры, когда задано расширение просмотра **запускаемым проектом**.
Щелкните правой кнопкой мыши на проект расширения контрольных значений и выберите **запуска с > пользовательские параметры...** :
    
[![](notifications-images/runwith-customparams-sml.png "Запуск с пользовательскими свойствами")](notifications-images/runwith-customparams.png#lightbox)
    
Откроется **аргументы выполнения** окно, содержащее **WatchKit** вкладки. Выберите **уведомления** и предоставляют полезные данные JSON, нажмите клавишу **Execute** для запуска приложения watch в симуляторе:
    
[![](notifications-images/runwith-execargs-sml.png "Выберите значение по умолчанию полезные данные уведомления")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы задать полезные данные уведомления теста в Visual Studio щелкните правой кнопкой мыши на расширение просмотра для редактирования **свойства проекта**. Перейдите к **Отладка** и выберите файл JSON уведомления из списка (он будет автоматически перечислять файлы JSON, включенный в проект).
    
[![](notifications-images/runwith-execargs-sml-vs.png "Выберите файл JSON уведомления")](notifications-images/runwith-execargs-vs.png#lightbox)

При расширении **запускаемым проектом**, Visual Studio будет отображаться дополнительные параметры, как показано ниже. Выберите один из **уведомления** функций открывается приложении для Apple watch **уведомления** режиме (с помощью JSON-файл, выбранный в окне «Свойства»):
    
![](notifications-images/runwith-vs.png "В меню устройства")

-----

Если тестирование выполняется в симуляторе файлом по умолчанию полезные данные JSON, контроллер уведомлений по умолчанию выглядит следующим образом:

![](notifications-images/notification-debug-sml.png "Пример уведомления об")

Можно также использовать [командной строки](~/ios/watchos/troubleshooting.md#command_line) запустить симулятор iOS.

### <a name="example-notification-payload"></a>Пример полезных данных уведомления

В [каталога контрольного значения Kit](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) существует пример приведен пример полезных данных JSON файла **NotificationPayload.json** (перечислены ниже).

```csharp
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```



## <a name="related-links"></a>Связанные ссылки

- [WatchNotifications (локальных уведомлений) (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Документация Apple Watch Kit уведомления](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
