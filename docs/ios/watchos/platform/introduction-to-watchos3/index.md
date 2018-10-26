---
title: Введение в watchOS 3
description: В этой статье рассматриваются все новые и измененные API-интерфейсы и функции, доступные в watchOS 3 для разработчиков Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121936"
---
# <a name="introduction-to-watchos-3"></a>Введение в watchOS 3

_В этой статье рассматриваются все новые и измененные API-интерфейсы и функции, доступные в watchOS 3 для разработчиков Xamarin._

В этом документе рассматриваются следующие темы:

- [Новые возможности в watchOS 3](#Whats-New-in-watchOS-3)
    - [Усовершенствования платить Apple](#Apple-Pay-Enhancements) добавлена поддержка платежей в приложении в Apple Watch.
    - [Фоновые задачи](#Background-Tasks) предоставить приложению возможность обновления данных в фоновом режиме, поэтому оно будет готово, когда это нужно пользователю.
    - [Усовершенствования сложности](#Complications-Enhancements) были внесены для watchOS 3, которые предоставляют новые функции для приложений.
    - [Вновь доступных платформ](#Newly-Available-Frameworks) предоставили для приложений watchOS.
    - [Упреждающие предложения](#Proactive-Suggestions) позволяет приложению заранее отображать сведения для пользователя.
    * Несколько [безопасности и конфиденциальности улучшения](#Security-and-Privacy-Enhancements) были внесены в watchOS 3.
    - [Моментальные снимки и закрепления](#Snapshots-and-Dock) предоставить пользователю быстрый доступ к приложениям приложение watchOS.
    - [Уведомления для пользователей](#User-Notifications) предоставляет локальные и удаленные уведомления для пользователя.
    * Несколько [усовершенствования Framework подключения Watch](#Watch-Connectivity-Framework-Enhancements) был внесен в watchOS 3.
    * Несколько [усовершенствования Framework WatchKit](#WatchKit-Framework-Enhancements) был внесен в watchOS 3.
    - [Усовершенствования приложения для тренировок](#Workout-App-Enhancements) предоставляет новые возможности для тренировок связанные приложения Apple Watch.
- [Дополнительные изменения платформы](#Additional-Framework-Changes) было внесено watchOS 3.
- [Устаревшие интерфейсы API](#Deprecated-APIs) в watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>Новые возможности в watchOS 3

Apple добавлено несколько новых API-интерфейсов и служб в watchOS 3, а также множество улучшений существующих функций, включая:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Улучшения Apple Pay

В watchOS 3 PassKit framework была расширена для поддержки безопасного, в приложении платежи (физических товаров и услуг) для приложений, запущенных в Apple Watch.

Используйте новый [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) и [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) классы для представления и реагировать на интерфейс, где пользователь может разрешить запросы оплаты.

Чтобы узнать больше, ознакомьтесь с разделом наших [усовершенствования платить Apple](~/ios/watchos/platform/apple-pay.md) руководства.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Фоновые задачи

watchOS 3 представлены несколько фоновых задач, которые приложение может использовать для обновления информации предоставление содержимого необходимые пользователю перед его открытием.

Доступны следующие новые фоновой задачи:

- **Фоновые обновления приложения** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) задач позволяет приложению обновлять свое состояние в фоновом режиме. Обычно это будет включать другой задачи, такие как загрузка нового содержимого из Интернета с помощью [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Фоновые обновления моментального снимка** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) задач позволяет приложению обновлять и его содержимое, так и для пользовательского интерфейса, прежде чем система делает снимок, который будет использоваться для заполнения панели закрепления.
- **Фон Контрольные значения подключения** - [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) задача запускается для приложения при получении данных фона из парном iPhone.
- **Фон сеанса URL-адрес** - [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) задача запускается для приложения, когда фоновая передача требует авторизации или завершения (успешно или ошибка).

Чтобы узнать больше, ознакомьтесь с разделом наших [фоновых задач](~/ios/watchos/platform/background-tasks.md) руководства.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Усовершенствования сложностей

Сложности, небольшой визуальные элементы, которые предоставляют полезную информацию с первого взгляда. В зависимости от выбранного циферблате пользователь имеет возможность настройки оформлений с усложнения один или несколько.

watchOS 3 дает приложению возможность создавать один или несколько Усложнений watch приложения таким образом, пользователь может обращаться к его сведения в краткие циферблате.

Кроме того сложности обеспечивают следующие преимущества:

- Пользователя можно быстро запустить приложение, нажав усложнения непосредственно из оформлений.
- Наличие одной из сложностей приложения о причинах возникновения лиц watch система для сохранения его в состоянии готовности к выпуску, когда он пытается запустить приложение в фоновом режиме, сохраните ее в памяти и обеспечивает дополнительное время для обновления.
- Сложности гарантируется по меньшей мере 50 обновлений Push-уведомлений в день.
- Если приложение включает сложностей, он будет отражена в коллекции лиц Apple Watch (см. в разделе Apple [Добавление сложности в коллекции](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) Дополнительные сведения).

В watchOS 3, ClockKit framework теперь включает несколько новых шаблонов для огромные сложности таких как [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) и [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Кроме того, чтобы создать локализуемый текст, используйте новые способы [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) класса.

Чтобы узнать больше, ознакомьтесь с разделом наших [быстрые методы взаимодействия для watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Вновь доступных платформ

watchOS 3 включает в себя несколько существующих платформ Apple, которые были ранее недоступны, например:

- **SceneKit** -SceneKit используется для включения трехмерных моделей в пользовательский Интерфейс в приложении для Apple watch, включая большинство функций, доступных на других платформах, как физических освещения, теней анимации и системы частиц. 3D пространственных аудио-, пользовательскими шейдерами операционной системы или OpenGL, фильтры образа Core и физически основе материалы не поддерживаются.
- **SpriteKit** -SpriteKit использования отображения и анимировать спрайтов в приложении для Apple watch приложения пользовательского интерфейса, включая большинство функций, доступных на других платформах, как и действия, физики, освещение и частиц систем. 3D пространственных аудио-, воспроизведение видео и фильтры образа Core не поддерживается.
- **AVFoundation** - Чтобы управлять и воспроизведение звука.
- **CloudKit** — для перемещения данных между контейнерами приложений и iCloud контрольных значений.
- **Основы аудио** — для обработки типов данных для представления аудиопотоков, сложные буферов и значений времени.
- **GameKit** — для создания игр для социальных сетей.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Упреждающие предложения

watchOS 3 позволяет приложению заранее представлять сведения для пользователя в пределах заданного контекстов. Для поддержки этой возможности [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) теперь включает `MapItem` свойство, которое позволяет приложению указывать информацию о расположении для дальнейшего использования в других приложениях.

Чтобы узнать больше, ознакомьтесь с разделом наших [введение упреждающие предложения](~/ios/watchos/platform/proactive-suggestions.md) руководства.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Безопасность и конфиденциальность усовершенствования

Apple сделала ряд усовершенствований безопасности и конфиденциальности в watchOS 3, которые помогут разработчику повысить безопасность своих приложений и обеспечению конфиденциальности конечного пользователя.

Таким образом, приложения, работающие в watchOS 3 (или более поздней версии) необходимо статически объявлять о своих намерениях для доступа к определенной функции или сведения о пользователе, введя один или несколько конфиденциальности определенные ключи в их `Info.plist` файлы, в которых объясняется, почему приложение хочет получить доступ пользователю.

Так как watchOS 3 использует эти изменения совместно с iOS 10, см. наш iOS 10 [безопасности и конфиденциальности улучшения](~/ios/app-fundamentals/security-privacy.md) для дополнительных сведений.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Моментальные снимки и закрепления

В watchOS 3 Apple добавлено закрепления, где пользователи могут присоединять их часто используемыми приложениями и быстрого доступа к ним. Когда пользователь нажимает кнопку на стороне на Apple Watch, будет отображаться коллекции закрепленное приложение моментальных снимков. Выдвигается влево или вправо, чтобы найти нужное приложение, а затем выберите приложение, чтобы запустить его замена моментального снимка с интерфейсом запущенном приложении.

Система периодически делать снимки пользовательском Интерфейсе приложения и использует эти моментальные снимки для заполнения документы. watchOS предоставляет приложению возможность обновить его содержимое и пользовательского интерфейса, прежде чем этого моментального снимка.

Дополнительные сведения см. в разделе наших [фоновых задач](~/ios/watchos/platform/background-tasks.md) руководство и Apple [WKSnapshotRefreshBackgroundTask ссылку](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Уведомления для пользователей

Уведомление пользователя framework, которая появилась watchOS 3 поддерживает доставки локальных и удаленных уведомлений для Apple Watch. Используйте эту платформу запланировать уведомления на основе определенных условий, таких как время дня или расположение, а также для получения и обработки уведомлений.

Чтобы узнать больше, ознакомьтесь с разделом наших [быстрые методы взаимодействия для watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Посмотрите усовершенствования Framework подключения

Новый `HasContentPending` свойство [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) класс указывает, что сеанс получил данные в фоновом режиме, необходимо обработать. И `RemainingComplicationUserInfoTransfers` свойство возвращает оставшееся время приложения iOS можно обновить его watchOS усложнения.

Чтобы узнать больше, ознакомьтесь с разделом наших [фоновых задач](~/ios/watchos/platform/background-tasks.md) руководства.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Усовершенствования WatchKit Framework

watchOS 3 включает в себя несколько усовершенствований WatchKit framework, включая следующие:

- Приложения можно получить состояние цифровой корона с новым [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) класса и получать обновления при вращении корона с помощью [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) класса.
- [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) теперь включает класс `ApplicationState` метод и [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) константа, приложение может использовать для отслеживания состояния среды выполнения приложения. `WKExtension` также предоставляет два новых метода, которые могут использоваться для планирования задач в фоновом режиме.
- [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) теперь содержит новый `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` и `HandleBackgroundTasks` методы для отслеживания изменений в состояния приложения и обрабатывать обновления задачи в фоновом режиме.
- Новый [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) добавлен класс для предоставления следующих типов распознавания жестов для приложения watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer ](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) и [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- Новый [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) класс предоставляет интерфейс, любой HomeKit присоединенных IP-камеру.
- Новый [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) класс позволяет приложению отображать фильм «poster», который заменяется работающей фильма, когда пользователь касается его.
- Новый [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) класс позволяет приложению для представления как кнопка Apple Pay в пользовательском Интерфейсе, который будет инициировать запрос платежа шифрованию.
- Новый [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) класс предоставляет интерфейс для отображения сцены SceneKit на Apple Watch.
- Новый [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) класс предоставляет интерфейс для отображения сцены SpriteKit на Apple Watch.

Чтобы узнать больше, ознакомьтесь с разделом наших [быстрые методы взаимодействия для watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) руководства.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Усовершенствования приложения для тренировок

Новое для watchOS 3, тренировки связанные приложения имеют возможность работать в фоновом режиме на Apple Watch. Чтобы включить эту функцию (и получить доступ к данным HealthKit), приложение должно включать `WKBackgroundModes` в `Info.plist` файла со значением `workout-processing`.

Кроме того разработчик теперь имеет возможность запустить приложение для организации тренировок watchOS из версии приложения iOS на парном iPhone.

Чтобы узнать больше, ознакомьтесь с разделом наших [усовершенствования приложения для тренировок](~/ios/watchos/platform/workout-apps.md) руководства.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Изменения дополнительные платформы

Помимо основных framework изменения и дополнения, перечисленных выше Apple внесла много дополнительных незначительные framework изменений в watchOS 3.

Чтобы узнать больше, ознакомьтесь с разделом наших [дополнительные изменения платформы](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) руководства.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>Устаревшие интерфейсы API

Следующие интерфейсы API стали нерекомендуемыми в watchOS 3:

- `UILocalNotification` Класс UIKit устарел и должен быть заменен с помощью платформы уведомление пользователя.

См. в разделе Apple [watchOS 2.2 для отличия API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) документации полный список устаревании и изменений.


## <a name="related-links"></a>Связанные ссылки

- [Примеры watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Новые возможности в watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
