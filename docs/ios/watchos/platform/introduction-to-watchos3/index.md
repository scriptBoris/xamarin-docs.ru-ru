---
title: Общие сведения о watchOS 3
description: В этой статье описаны все новые и измененные интерфейсы API и возможности, доступные в watchOS 3 для разработчиков Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: 506e3795538ceffc77301a608c504fc6ec2045a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780041"
---
# <a name="introduction-to-watchos-3"></a>Общие сведения о watchOS 3

_В этой статье описаны все новые и измененные интерфейсы API и возможности, доступные в watchOS 3 для разработчиков Xamarin._

В этом документе рассматривается в следующих разделах:

- [Новые возможности watchOS 3](#Whats-New-in-watchOS-3)
    - [Усовершенствования платить Apple](#Apple-Pay-Enhancements) добавлена поддержка платежей в приложении в Apple Watch.
    - [Фоновых задач](#Background-Tasks) предоставить приложению возможность обновить эти сведения в фоновом режиме, поэтому она готова, когда пользователь должен его.
    - [Усовершенствования сложностей](#Complications-Enhancements) были внесены для watchOS 3, которая предоставляет пользователям новые возможности для приложений.
    - [Вновь доступных платформ](#Newly-Available-Frameworks) предоставили для watchOS приложений.
    - [Упреждающее предложения](#Proactive-Suggestions) позволяет приложению заранее отображать сведения для пользователя.
    * Несколько [безопасности и конфиденциальности улучшения](#Security-and-Privacy-Enhancements) были внесены watchOS 3.
    - [Моментальные снимки и закрепление](#Snapshots-and-Dock) предоставить пользователю быстрый доступ к приложениям, watchOS приложения.
    - [Уведомления пользователя](#User-Notifications) позволяет локальным и удаленным уведомления пользователя.
    * Несколько [Framework усовершенствования Контрольные значения подключения](#Watch-Connectivity-Framework-Enhancements) были внесены в watchOS 3.
    * Несколько [WatchKit Framework усовершенствования](#WatchKit-Framework-Enhancements) были внесены в watchOS 3.
    - [Усовершенствования приложения тренировки](#Workout-App-Enhancements) предоставляет новые возможности для тренировки связанных приложений Apple Watch.
- [Дополнительные изменения Framework](#Additional-Framework-Changes) было внесено watchOS 3.
- [Устаревшие интерфейсы API](#Deprecated-APIs) в watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>Новые возможности watchOS 3

Apple были добавлены несколько новых API-интерфейсов и служб в watchOS 3 и усовершенствованиях существующих компонентов, включая:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Усовершенствования оплаты Apple

В watchOS 3 PassKit framework была расширена для поддержки для безопасности, в приложении платежи (физические товары и службы) для приложений, выполняющихся в Apple Watch.

Используйте новую [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) и [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) классы для представления и реагировать на интерфейс, где пользователь может авторизовать заявок.

Для получения дополнительных см. в разделе нашей [усовершенствования платить Apple](~/ios/watchos/platform/apple-pay.md) руководства.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Фоновые задачи

watchOS 3 представлены несколько фоновые задачи, которые приложение может использовать для обновления информации проверку содержимого перед его открытием нужны пользователю.

Доступны следующие новые фоновой задачи.

- **Фоновые обновления приложения** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) задача позволяет приложению обновлять его состояние в фоновом режиме. Обычно это относится и к другой задачи, такие как загрузка новое содержимое из Интернета, используя [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Фоновые обновления моментального снимка** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) задач позволяет приложению для обновления пользовательского интерфейса и его содержимое, прежде чем система делает снимок, который будет использоваться для заполнения дока.
- **Фон подключения Контрольные значения** - [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) начала задачи для приложения при получении данных фона с парной iPhone.
- **Фон сеанса URL-адрес** - [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) начала задачи для приложения, когда фоновая передача требует авторизации или завершения (успешно или ошибка).

Чтобы узнать больше, см. в разделе нашей [фоновые задачи](~/ios/watchos/platform/background-tasks.md) руководства.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Усовершенствования сложности

Сложностей, небольшие визуальные элементы, которые предоставляют полезные сведения, с одного взгляда. В зависимости от выбранного Циферблат часов пользователь имеет возможность настраивать Циферблат часов с одной или нескольких усложнения.

watchOS 3 дает приложению возможность создания одного или нескольких усложнения для наблюдения за приложения пользователю доступ к его сведения на обзор из Циферблат часов.

Кроме того сложности обеспечивают следующие преимущества:

- Пользователь может быстро запустить приложение, нажав на усложнения непосредственно из Циферблат часов.
- С его сложностей приложения на причины начертания Контрольные значения слишком приложения в состоянии все готово для запуска, когда он пытается запустить приложение в фоновом режиме, храните его в памяти и обеспечивает дополнительное время для обновления.
- Сложностей гарантируется по меньшей мере 50 принудительной обновлений в день.
- Если приложение включает сложностей, он будет отображаться в коллекции начертания Apple Watch (компании Apple. в разделе [сложностей добавления в коллекцию](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) Дополнительные сведения).

В watchOS 3 ClockKit framework теперь включает несколько новых шаблонов для долгосрочного сложностей например [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) и [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Кроме того, чтобы создать локализуемый текст, использовать новые методы [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) класса.

Чтобы узнать больше, см. в разделе нашей [быстрого взаимодействия, обеспечивающих watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Вновь доступных платформ

watchOS 3 включает несколько существующих инфраструктур Apple, которые ранее были недоступны такие как:

- **SceneKit** -SceneKit используется для включения трехмерные модели в приложение watch пользовательского интерфейса, включая большинство систем примитивов и функции, доступные на других платформах, как физических освещения, заливки, анимацию,. 3D пространственных аудио, пользовательские шейдеров исходного состояния системы или OpenGL, фильтры образа ядра и физически на основе материалов не поддерживаются.
- **SpriteKit** -SpriteKit используйте визуализации и анимации спрайтов в пользовательском Интерфейсе приложения Контрольное значение приложении, включая большинство функций, доступных на других платформах, таких как действия, физических, освещения и примитив систем. 3D пространственных аудио-, воспроизведение видео и фильтры образа Core не поддерживается.
- **AVFoundation** — для управления и воспроизведения звука.
- **CloudKit** — перемещение данных между контейнерами приложения и iCloud контрольных значений.
- **Основы аудио** - для обработки типов данных для представления звуковых потоков, сложные буферы и значения времени.
- **GameKit** — для создания игр социальных сетей.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Упреждающее предложения

watchOS 3 позволяет приложению заранее предоставлять информацию для пользователя в пределах заданного контекстов. Для поддержки этой возможности [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) теперь включает `MapItem` свойство, которое позволяет приложения, которые предоставляют сведения о расположении для дальнейшего использования другими приложениями.

Чтобы узнать больше, см. в разделе нашей [введение в упреждающего предложения](~/ios/watchos/platform/proactive-suggestions.md) руководства.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Безопасность и конфиденциальность усовершенствования

Apple представляет собой ряд улучшений безопасности и конфиденциальности в watchOS 3, которые помогут разработчику повысить безопасность приложений и обеспечить конфиденциальность пользователя.

В результате приложения, работающие на watchOS 3 (или более поздней версии) необходимо объявить статически их назначение для доступа к определенных функций или сведения о пользователе, указав один или несколько конфиденциальности определенные ключи в их `Info.plist` файлы, в которых объясняется, почему приложению необходимо получить доступ пользователю.

Поскольку watchOS 3 совместно использует эти изменения с iOS 10, см. в разделе iOS 10 [безопасности и конфиденциальности улучшения](~/ios/app-fundamentals/security-privacy.md) для дополнительных сведений.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Моментальные снимки и закрепление

В watchOS 3 Apple установил закрепления, где пользователи могут закреплять избранные приложений и быстрого доступа к ним. При нажатии кнопки боковой на Apple Watch, отображается коллекция моментальных снимков закрепленных приложения. Пользователь может проведите влево или вправо, чтобы найти нужное приложение, затем выберите приложения, чтобы запустить его замены моментального снимка интерфейса выполняющегося приложения.

Система периодически принимает пользовательского интерфейса приложения моментальных снимков и использует эти моментальные снимки для заполнения документы. watchOS дает приложению возможность обновить его содержимое и пользовательского интерфейса, прежде чем этот моментальный снимок.

Дополнительные сведения см. в разделе нашей [фоновые задачи](~/ios/watchos/platform/background-tasks.md) руководство и Apple [WKSnapshotRefreshBackgroundTask ссылка](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Уведомления для пользователей

Уведомление пользователя framework, представленных в watchOS 3 поддерживает доставки уведомлений, локальные и удаленные для Apple Watch. Используйте эту структуру планировать уведомления в зависимости от определенных условий, таких как время дня или расположение, а также получать и обрабатывать уведомления.

Чтобы узнать больше, см. в разделе нашей [быстрого взаимодействия, обеспечивающих watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Посмотрите усовершенствования Framework подключения

Новый `HasContentPending` свойство [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) класс указывает, что сеанс получил данные в фоновом режиме, необходимо обработать. И `RemainingComplicationUserInfoTransfers` свойство возвращает оставшееся время приложения iOS можно обновить его watchOS усложнения.

Чтобы узнать больше, см. в разделе нашей [фоновые задачи](~/ios/watchos/platform/background-tasks.md) руководства.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Усовершенствования WatchKit Framework

watchOS 3 включает несколько усовершенствований платформа данных WatchKit, включая следующие:

- Приложение может получить состояние цифровой Главная с использованием нового [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) класса и получать обновления при вращении Главная с помощью [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) класса.
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) класс теперь включает `ApplicationState` метод и [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) константу, приложение можно использовать для отслеживания состояния среды выполнения приложения. `WKExtension` также содержит два новых метода, которые могут использоваться для планирования задачи в фоновом режиме.
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) теперь включает новый `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` и `HandleBackgroundTasks` методы для контроля за изменениями в состояния приложения и обработки обновления задачи в фоновом режиме.
- Новый [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) был добавлен класс для предоставления распознавания жестов для наблюдения за приложениями следующих типов: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer ](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) и [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- Новый [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) класс предоставляет интерфейс для любого HomeKit присоединенного IP-камеру.
- Новый [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) класс позволяет приложению отображать фильма «плакат «заменяется выполняющегося фильма, когда пользователь нажимает кнопку.
- Новый [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) класс позволяет приложению предоставлять Apple Pay кнопку в пользовательском Интерфейсе, который будет инициировать запрос платежа при касании.
- Новый [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) класс предоставляет интерфейс для отображения сцены SceneKit на Apple Watch.
- Новый [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) класс предоставляет интерфейс для отображения сцены SpriteKit на Apple Watch.

Чтобы узнать больше, см. в разделе нашей [быстрого взаимодействия, обеспечивающих watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Усовершенствования тренировки приложения

Новые watchOS 3 тренировки связанных приложения имеют возможность работать в фоновом режиме на Apple Watch. Чтобы включить эту функцию (и получить доступ к данным HealthKit), необходимо включить приложение `WKBackgroundModes` ключа в `Info.plist` файл со значением `workout-processing`.

Кроме того разработчик теперь имеет возможность запускать приложение тренировки watchOS из версии приложения iOS на iPhone парной.

Для поиска дополнительных сведений см. в разделе нашей [усовершенствования приложения тренировки](~/ios/watchos/platform/workout-apps.md) руководства.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Изменения дополнительных Framework

Помимо основных framework изменения и дополнения, перечисленных выше Apple внесла много дополнительных framework незначительные изменения в watchOS 3.

Чтобы узнать больше, см. в разделе нашей [дополнительные изменения Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) руководства.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Устаревшие интерфейсы API

Следующие API-интерфейсы являются устаревшими в watchOS 3:

- `UILocalNotification` UIKit класс рекомендуется к использованию и должны быть заменены с помощью платформы уведомление пользователя.

В разделе Apple [watchOS 2.2 для отличия API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) документации для получения полного списка возражения, и изменения.


## <a name="related-links"></a>Связанные ссылки

- [Примеры watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Новые возможности watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
