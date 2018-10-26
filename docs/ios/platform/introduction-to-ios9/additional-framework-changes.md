---
title: Изменения платформы дополнительных iOS 9
description: В этом документе описываются изменения дополнительные платформы, впервые появился в iOS 9. В нем описывается AVFoundation AVKit и CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: bdb401cd9fd3cfa1e33acec1252cfffbd8be3ebd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116645"
---
# <a name="additional-ios-9-frameworks-changes"></a>Изменения платформы дополнительных iOS 9

_В этой статье рассматриваются дополнительные, незначительные изменения и улучшения для существующих платформ IOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 логотип")](additional-framework-changes-images/ios9.png#lightbox)

Помимо основные изменения в iOS Apple сделала изменения и усовершенствования несколько существующих инфраструктур в iOS 9.

## <a name="avfoundation-framework-additions"></a>Дополнения AVFoundation Framework

В платформе AVFoundation [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) классов теперь можно указать голос по идентификатору Дополнительно языка.

Например следующий код получает список всех доступных голоса:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

Затем можно использовать один из голосов, в списке, задав его как `Voice` свойство экземпляра [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) класса.

[AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) класс теперь поддерживает смешанные файловые и потоковой передачи мультимедиа для Интернета в очереди. Предыдущие версии может только очереди мультимедиа того же типа.

Дополнительные сведения см. в разделе Apple [AVSpeechSynthesisVoice ссылку](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Дополнения AVKit Framework

Для работы с новой функцией рисунок in-Picture (PIP), платформа AVKit включает новый `AVPictureInPictureController` и [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) классы:

- **AVPictureInPictureController** -этот класс позволяет приложению iOS 9 реагировать на пользователя, запуск воспроизведения видео в окне PIP с плавающей запятой, размер которой можно изменять на iPad.
- **AVPlayerViewController** -управляет `AVPlayer` контроллер, используемый для представления видео в окне PIP с плавающей запятой, размер которой можно изменять на iPad.

Дополнительные сведения см. в разделе наших [многозадачность для iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) документации и Apple [ссылку AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) и [ссылку AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Введение в CloudKit веб-служб

Инфраструктура CloudKit упрощает разработку приложений, доступа к iCloud. Сюда входят методы для получения данных приложений и средств права, а также возможность обеспечить безопасное хранение сведений о приложении. Этот комплект предоставляет пользователям уровня анонимности, разрешив доступ к приложениям с их iCloud идентификаторы без обмена личными данными.

Новый _CloudKit веб-служб_ framework предоставляет библиотеку JavaScript (CloudKit JS), который может быть включен в веб-сайт для предоставления доступа к одним и тем же CloudKit на основе данных и содержимое как приложение Xamarin.iOS.

> [!IMPORTANT]
> Прежде, чем вы можете получить доступ к, представления или обновить содержимое из CloudKit базы данных с помощью CloudKit JS, необходимо предварительно определить схемы этой базы данных.




Дополнительные сведения см. следующие документы:

- [Введение в CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -наши Общие сведения об использовании CloudKit в приложении Xamarin.iOS.
- [Быстрый запуск CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -Apple введение в CloudKit.
- [Справочник по JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -CloudKit JS документации компании Apple.
- [CloudKit веб-служб ссылки](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -Apple ссылку, которая описывает интерфейс HTTP для CloudKit.
- [Каталог CloudKit: Введение в CloudKit (Cocoa и JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -Apple примера приложения, с помощью CloudKit и CloudKit JS.

> [!IMPORTANT]
> Компания Apple [предоставляет инструменты](https://developer.apple.com/support/allowing-users-to-manage-data/), которые помогают разработчикам надлежащим образом соблюдать Общий регламент по защите данных Европейского союза (GDPR).

## <a name="foundation-framework-additions"></a>Дополнения Framework Foundation

В iOS 9 Apple включены следующие изменения в среде Foundation:

### <a name="changes-to-nsbundle"></a>Изменения в NSBundle

Были внесены следующие изменения для [NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) класс для iOS 9:

* `GetPreservationPriorityForTag (NSString tag)` — Возвращает текущий приоритет хранения для ресурсов с помощью данного тега. Допустимые значения лежат в диапазоне `0.0` для `1.0`, сначала очищаются ресурсы с самым низким приоритетом.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` — Задает текущий приоритет хранения для ресурсов с помощью заданного тегов. Допустимые значения лежат в диапазоне `0.0` для `1.0`, сначала очищаются ресурсы с самым низким приоритетом.

Дополнительные сведения см. в разделе Apple [NSBundle ссылку](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Изменения в NSProcessInfo

Каждого процесса, запущенного на устройстве iOS имеется один, _процесс агента сведения_ (PIA). Используйте [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) класс, предоставляющий сведения о текущей основных сборках ВЗАИМОДЕЙСТВИЯ и управления питанием и температурным для данного процесса.

Например для управления автоматического завершения процесса можно использовать следующий код:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Дополнительные сведения см. в разделе Apple [NSProcessInfo ссылку](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Реагирование на режим пониженного энергопотребления

Используйте `LowPowerModeEnabled` свойство [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) класс, чтобы определить, включен ли режим низкого потребления на устройстве iOS, на которой выполняется приложение. Пример:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Изменения платформы HealthKit

Apple включает следующие изменения для [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework в iOS 9:

- Поддержка массовое удаление и отслеживания удаления записей в базе данных HealthKit. См. в разделе Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) и [ссылки на класс HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) Дополнительные сведения.
- Чтобы были добавлены новые категории отслеживания и характеристики `HKQuantityTypeIdentifier` класс (такие как `UVExposure`) и `HKCategoryTypeIdentifier` класс (такие как `OvulationTestResult`). См. в разделе Apple [ссылки константы HealthKit](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) Дополнительные сведения.

См. в нашем [введение в HealthKit](~/ios/platform/healthkit.md) Дополнительные сведения о работе с HealthKit в Xamarin.iOS.

## <a name="local-authentication-framework-changes"></a>Изменения платформы локальной проверки подлинности

Apple включает следующие изменения для [локальной проверки подлинности](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework в iOS 9:

- С помощью `EvaluateAccessControl` и `EvaluatePolicy` методы [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) класс, теперь вы можете повторно использовать Touch ID соответствий из предыдущей успешной разблокировки попыток.
- Возможность получения списка зарегистрированных в настоящее время пальцами.
- Поддержка отслеживания, когда палец добавляется или удаляется из проверки подлинности.
- Возможность использования _контекст проверки подлинности_ вызовы цепочки ключей и поддержки для оценки доступ к цепочке ключей управления перечислены.
- Возможность отменить запрос пользователя из кода.

См. в нашем [Общие сведения о Touch ID](~/ios/platform/touchid.md) Дополнительные сведения о работе с Touch ID в Xamarin.iOS.

### <a name="lacontext-changes"></a>Изменения LAContext

Были внесены следующие изменения для [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) класс для iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -возвращает максимальный объем времени, который можно использовать с проверкой подлинности идентификатор сенсорного ввода.
- **EvaluatedPolicyDomainState** — Возвращает или задает состояние оценки политики.
- **MaxBiometryFailures** -были разнесены в iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Возвращает или задает количество времени, который можно использовать с проверкой подлинности идентификатор сенсорного ввода.
- **EvaluateAccessControl** — асинхронно оценивает политику проверки подлинности.
- **Сделать недействительным** -делает недействительным с проверкой подлинности идентификатор заданного сенсорного ввода.
- **IsCredentialSet** -возвращает `true` Если в настоящее время заданы учетные данные.
- **SetCredentialType** задает указанный тип учетных данных.

См. в разделе Apple [LAContext ссылку](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) для получения дополнительных сведений.

## <a name="mapkit-framework-changes"></a>Изменения платформы MapKit

Apple включает следующие изменения для [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework в iOS 9:

- MapKit теперь обеспечивает поддержку для запуска приложения карты непосредственно в передаче направлениях, а также для выполнения запросов к передаче предполагаемое время прибытия (ETA) с помощью [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) и [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) классы.
- Результаты поиска, возвращенные MapKit и [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) класс также может предоставлять результирующий часовой пояс.
- Теперь вы можете полностью настроить карты заметки, представленный приложения iOS с помощью `DetailCalloutAccessoryView` свойство [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) класса.

См. наш [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md) и [Пошаговое руководство. изучение заметки и наложения в MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) Дополнительные сведения о работе с картами и заметок в Xamarin.iOS и Apple [CLGeocoder ссылки](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) Дополнительные сведения.

## <a name="passkit-framework-additions"></a>Дополнения PassKit Framework

Apple включает следующие изменения для [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework в iOS 9:

- Apple Pay теперь поддерживает хранилище дебетовой и кредитные карты, а также карт Discover. См. в разделе **сетей оплаты** раздел Apple [ссылки на класс PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) Дополнительные сведения.
- Из непосредственно в приложении Xamarin.iOS, теперь добавляемый сетей оплаты и поставщиков карты Apple Pay. См. в разделе Apple [ссылки на класс PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) для получения дополнительных сведений.

См. в нашем [введение в PassKit](~/ios/platform/passkit.md) Дополнительные сведения о работе с PassKit в Xamarin.iOS.

## <a name="safari-services-framework-additions"></a>Safari служб Framework дополнения

Apple включает следующие изменения для [служб Safari](https://developer.xamarin.com/api/namespace/SafariServices/) framework в iOS 9:

- Теперь вы можете использовать новый [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) для отображения веб-содержимого в приложении Xamarin.iOS. Он предоставляет возможность совместно использовать данные веб-сайтов и файлов cookie с помощью приложения Safari, а также некоторые функции Safari (например, средство чтения и Автозаполнение). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) функции **сделать** кнопка, которая будет возвращать пользователей в приложение при завершении работы, просмотр веб-содержимое.

Так как [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) класс предназначен для отображения на одной странице веб-содержимого, можно использовать его для замены всех [WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/) или [UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)элементов управления в существующие приложения Xamarin.iOS.

### <a name="displaying-a-website"></a>Отображение веб-сайта

В следующем коде приведен пример вызова [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) из внутри другой контроллер представления:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Изменения платформы UIKit

Apple включает в себя множество улучшений в несколько элементов из [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) платформа для iOS 9. В следующих разделах будут подробно эти изменения.

### <a name="3d-touch-events"></a>События 3D Touch

Знакомы с iOS 9 и iPhone версии 6s и iPhone 6s Plus, 3D Touch добавляет конфиденциальных жесты давление приложения iOS. Таким образом, если ваше приложение работает в iOS 9 (или более поздней) и устройство iOS может вспомогательных 3D Touch, изменения замедленной вызовет `TouchesMoved` возникает событие. 

Из-за этого изменения в поведении приложения iOS должны быть подготовлены для `TouchesMoved` чаще, вызов события даже в том случае, если X / Y-координаты не изменились. 

Дополнительные сведения см. в разделе наших [введение в 3D Touch](~/ios/platform/3d-touch.md) руководства.

### <a name="document-open-in-place-functionality"></a>Функциональные возможности документ открытым на месте

С помощью `FinishedLaunching (application, launchOptions)` или `WillFinishLaunching (Application, launchOptions)` методы [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) класс, теперь можно открыть документ и измените его на месте (в отличие от работы на копию).

Для поддержки новых функциональных возможностей открытым на месте, добавьте `LSSupportsOpeningDocumentsInPlace` ключа в приложение Xamarin.iOS **Info.plist** файла со значением `YES`.

См. в разделе Apple [UIApplicationDelegate ссылку](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) для получения дополнительных сведений.

### <a name="enhanced-touch-events"></a>События касания улучшенные

Apple предоставляет ряд дополнительных возможностей на события касания в iOS 9. К ним относятся возможность использовать Touch прогноза и получить доступ к промежуточных штрихи между обновлениями отображения.

См. в разделе Apple [руководство обработки событий для iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) для получения дополнительных сведений.

### <a name="fetching-tailored-content"></a>Получение содержимого в соответствии с предпочтениями

Новый `NSDataAsset` класс позволяет приложения Xamarin.iOS для получения содержимого, адаптированные к памяти и графических возможностей, на котором он выполняется в настоящее время на устройстве iOS.

### <a name="new-layout-anchors"></a>Новый макет привязки

Новый `NSLayoutAnchor` и `NSLayoutDimension` классы привязки макета, работают с новыми свойствами привязки из [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) класс (такие как `LeadingAnchor` и `WidthAnchor`) для упрощения макета в iOS 9.

См. наш [Знакомство с унифицированным раскадровкам](~/ios/user-interface/storyboards/unified-storyboards.md) Дополнительные сведения о работе с Автомакет и размеры в приложение Xamarin.iOS и Apple [ссылку NSLayoutAnchor](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Справочник по NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) и [UIView ссылки](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) Дополнительные сведения.

### <a name="new-readable-content-margins"></a>Новые поля для чтения содержимого

Новый `UILayoutGuide` класс может использоваться для предоставления поля для чтения содержимого и определения областей draw для содержимого внутри представления. См. в разделе Apple [UILayoutGuide ссылки](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) Дополнительные сведения.

### <a name="text-input-in-notifications-modifications"></a>Ввод текста в уведомления изменения

[UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/) классе определено новое `Behavior` свойство, которое может использоваться для поддержки ввода текста на уведомления.

### <a name="uiapplicationdelegate-changes"></a>Изменения UIApplicationDelegate

Хотя формально не устарела компанией Apple, они предлагают, заменив все вызовы `FinishedLaunching (UIApplication application)` метод [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) класс `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` или `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` методы.

См. в разделе Apple [UIApplicationDelegate ссылку](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) для получения дополнительных сведений.

### <a name="uikit-dynamics-changes"></a>Изменения UIKit Dynamics

В iOS 9 Apple включены следующие изменения в UIKit Dynamics:

- Dynamics теперь поддерживает непрямоугольные конфликтов границы.
- Новый настраиваемый `UIFieldBehavior` класс используется для поддержки различных типов полей.
- Были добавлены дополнительные вложения типы `UIAttachmentBehavior` класса.

См. в разделе Apple [UIAttachment ссылку](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) для получения дополнительных сведений.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView и UIDatePicker изменения

До iOS 9 [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) и [UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/) элементы управления были неизменяемый и будет автоматически изменить для заполнения контейнера (обычно ширину устройства iOS, приложение для запуска на).

В iOS 9 это автоматическое изменение размеров исчезновения и элементы управления, которые будут передаваться в ширину 320 точек на всех устройствах iOS, независимо от размера и ориентации экрана.

Чтобы исправить эту ситуацию, используйте автоматический макет и размеры закрепить ширину элемента управления к краям родительского контейнера (представление), а также укажите требуемую высоту. См. в нашем [Знакомство с унифицированным раскадровкам](~/ios/user-interface/storyboards/unified-storyboards.md) Дополнительные сведения о работе с автоматический макет и размер классов в приложении Xamarin.iOS.

### <a name="new-uitextinputassistantitem-class"></a>Новый класс UITextInputAssistantItem

Используйте новый `UITextInputAssistantItem` класса группы кнопок панели макета в _панель_. Панель является новой области, доступные в программируемой клавиатуры для предоставления ввода клавиш.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Введение в iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [Новые возможности в iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
