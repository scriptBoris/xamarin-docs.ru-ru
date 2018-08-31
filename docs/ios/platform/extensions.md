---
title: iOS расширений в Xamarin.iOS
description: В этом документе описаны расширения, которые являются мини-приложений, представленных операций ввода-вывода в стандартных контексте, например, в центре уведомлений. Он описывает, как создать расширение и взаимодействия с ней из в родительское приложение.
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 831625c88bb3c0f8403d3b330054050488956cb6
ms.sourcegitcommit: f9fb316344fc4dce2fdce0dde3c5f4c2e4a42d08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "40251224"
---
# <a name="ios-extensions-in-xamarinios"></a>расширениях iOS в Xamarin.iOS

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**Создание расширений в iOS по [Xamarin University](https://university.xamarin.com/)**

Расширения, как впервые появился в iOS 8, предназначены специально `UIViewControllers` , представлены по iOS внутри стандартные контексты такие, как в **центр уведомлений**, как специализированные типы пользовательских сочетаний, запрошенный пользователем для выполнения входные данные или других контекстах, например редактирования фотографий, где модуль можно предоставить специальный эффект фильтры.

Все расширения установлены вместе с приложением контейнера (с обоих элементов, написанные с использованием 64-разрядные Unified API) и проходят активацию на определенный момент расширения в приложении узла. И поскольку они будут использоваться в качестве дополнения к существующим функциям системы, они должны быть высокая производительность, экономичные и надежные. 

## <a name="extension-points"></a>Точки расширения

|Тип|Описание:|Точка расширения|Узел приложения|
|--- |--- |--- |--- |
|Действие|Специализированный редактор или средство просмотра для определенного типа носителя|`com.apple.ui-services`|Любой|
|Поставщик документа|Позволяет приложению использовать хранилище удаленных документов|`com.apple.fileprovider-ui`|Приложения, использующие [UIDocumentPickerViewController](https://developer.xamarin.com/api/type/UIKit.UIDocumentPickerViewController/)|
|Клавиатура|Альтернативный клавиатуры|`com.apple.keyboard-service`|Любой|
|Редактирование фотографий|Манипуляций над и редактирования|`com.apple.photo-editing`|Редактор Photos.App|
|Общий доступ|Взаимодействует с социальными сетями, обмена сообщениями, служб и т. д.|`com.apple.share-services`|Любой|
|Сегодня|«Мини-приложения», отображаемые на экране сегодня и центр уведомлений|`com.apple.widget-extensions`|Сегодня и центр уведомлений|

[Дополнительное расширение точек](~/ios/platform/introduction-to-ios10/index.md#app-extensions) были добавлены в iOS 10.

## <a name="limitations"></a>Ограничения

Расширения имеют ряд ограничений, некоторые из которых являются универсальными для всех типов (для экземпляра, тип расширения не может получить доступ к камеры или микрофонов) при других типах расширения может иметь особые ограничения от их использования (например, пользовательские клавиатура не может использоваться для защиты данных поля ввода как для паролей). 

Универсальные ограничения являются:

- [Kit работоспособности](~/ios/platform/healthkit.md) и [пользовательского интерфейса пакета событий](~/ios/platform/eventkit.md) платформы не доступны
- Расширения не может использовать [расширенных фоновые режимы](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- Расширения не может получить доступ к камеры устройства или микрофонов (несмотря на то, что они могут обращаться к существующие файлы мультимедиа)
- Расширения не могут получать Air Drop данных (несмотря на то, что они могут передавать данные через Air Drop)
- [UIActionSheet](https://developer.xamarin.com/api/type/UIKit.UIActionSheet/) и [UIAlertView](https://developer.xamarin.com/api/type/UIKit.UIAlertView/) недоступны; необходимо использовать расширения [UIAlertController](https://developer.xamarin.com/api/type/UIKit.UIAlertController/)
- Несколькими членами [UIApplication](https://developer.xamarin.com/api/type/UIKit.UIApplication/) недоступны: [UIApplication.SharedApplication](https://developer.xamarin.com/api/property/UIKit.UIApplication.SharedApplication/), `UIApplication.OpenURL`, `UIApplication.BeginIgnoringInteractionEvents` и `UIApplication.EndIgnoringInteractionEvents`
- iOS определяет ограничение использования памяти 16 МБ на современных расширения.
- По умолчанию клавиатуры расширений нет доступа к сети. Это влияет на отладку на устройстве (ограничение не применяется в симуляторе), так как Xamarin.iOS требуется доступ к сети для отладки. Существует возможность запроса доступа к сети, задав `Requests Open Access` значение в файле Info.plist проекта для `Yes`. См. в разделе Apple [руководство клавиатуры Custom](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html) Дополнительные сведения об ограничениях расширения клавиатуры.

Отдельные ограничения, см. в разделе Apple [руководство по программированию расширения приложения](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/).

## <a name="distributing-installing-and-running-extensions"></a>Распространение, Установка и запуск расширений

Расширения распределяются из внутри приложения-контейнера, который, в свою очередь отправляется и распространяются через App Store. На этом этапе устанавливаются расширения, распространяемый с приложением, но пользователь должен явно включить каждое расширение. Различные типы расширения включены различными способами; Некоторые требуют пользователю переходить на **параметры** приложения и включить их оттуда. Хотя остальные включены в момент использования, таких как включение расширение совместного использования при отправке фотографию. 

Приложения, в котором используется расширение (где обнаружении эта точка расширения) называется **приложение Host**, так как это приложение, на котором размещена расширения при выполнении. — Приложение, которое устанавливает расширение **приложения-контейнера**, так как это приложение, которое содержится расширения во время установки.  

Как правило приложения-контейнера описывается расширение и сопровождает пользователя в процессе его включения.

## <a name="extension-lifecycle"></a>Жизненный цикл расширения

Расширение может быть сложнее, чем один [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) или более сложные расширения, которые предлагают несколько экранов пользовательского интерфейса. При обнаружении _точки расширения_ (например, когда общий доступ к изображения), они будут иметь возможность выбрать из расширения, зарегистрированные для этой точки расширения. 

Если они выберите один из приложения, расширения, его `UIViewController` будет создан и начать обычный жизненный цикл контроллера представления. Тем не менее в отличие от обычное приложение, которая будет приостановлено, но обычно не заканчивается, когда пользователь завершает взаимодействовать с ними, расширения загружаются, выполняется и затем завершается несколько раз.

Расширения могут взаимодействовать с их размещение приложений с помощью [NSExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) объекта. Некоторые расширения имеют операциям, получающим асинхронные обратные вызовы с результатами. Эти обратные вызовы выполняются в фоновых потоках и модуль должны учитывать это; Например, с помощью [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/member/Foundation.NSObject.InvokeOnMainThread/) для обновления пользовательского интерфейса. См. в разделе [взаимодействия с приложением узла](#Communicating-with-the-Host-App) Дополнительные сведения в приведенном ниже разделе.

По умолчанию расширения и их приложения-контейнеры могут не взаимодействовать, несмотря на то, устанавливаемого вместе. В некоторых случаях приложения-контейнера является по сути пустой «доставка» контейнер, цель которых выдается после установки расширения. Тем не менее, если условиям, приложения-контейнера и расширения могут совместно использовать ресурсы из общую область. Кроме того **сегодня расширение** может запросить его приложения-контейнера для открытия URL-адрес. Это поведение отображается в [развиваться мини-приложение обратного отсчета](http://github.com/xamarin/monotouch-samples/tree/master/ExtensionsDemo).

## <a name="creating-an-extension"></a>Создание расширения

Расширения (и их приложения-контейнеры) должны быть 64-разрядных двоичных файлов и созданные с помощью Xamarin.iOS [унифицированными API](http://developer.xamarin.com/guides/cross-platform/macios/unified). При разработке расширения, ваши решения будет содержать по крайней мере два проекта: проект приложения-контейнера и один для каждого расширения контейнера обеспечивает. 

### <a name="container-app-project-requirements"></a>Требования к контейнеру приложения проекта

Приложения-контейнера, используемый для установки расширения предъявляются следующие требования:

- Он должен поддерживать ссылку на проект расширения.   
- Он должен быть полноценное приложение (необходимо иметь возможность запустить и выполнить успешно) даже в том случае, если он ничего не делает больше, чем позволяют установить расширение. 
- Он должен иметь идентификатор пакета, который является основой для идентификатора пакета расширения проекта (см. в разделе ниже для получения дополнительных сведений).

### <a name="extension-project-requirements"></a>Требования к расширениям проекта

Кроме того проект расширения предъявляются следующие требования:

- Он должен иметь идентификатор пакета, который начинается с идентификатора пакета приложения его контейнера. Например, если приложения-контейнера имеет идентификатор пакета `com.myCompany.ContainerApp`, может быть идентификатором расширения `com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- Он должен определять ключ `NSExtensionPointIdentifier`, с соответствующим значением (такие как `com.apple.widget-extension` для **сегодня** мини-приложения в центре уведомлений) в его `Info.plist` файл.
- Он должен также определять *либо* `NSExtensionMainStoryboard` ключ или `NSExtensionPrincipalClass` в его `Info.plist` файл соответствующее значение:
    - Используйте `NSExtensionMainStoryboard` ключей, чтобы задать имя раскадровки, который представляет основной пользовательский Интерфейс для расширения (минус `.storyboard`). Например `Main` для `Main.storyboard` файла.
    - Используйте `NSExtensionPrincipalClass` клавишу, чтобы указать класс, который будет инициализирован при запуске расширения. Значение должно соответствовать **зарегистрировать** значение вашей `UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

Определенные типы расширений могут иметь дополнительные требования. Например **сегодня** или **центр уведомлений** расширения основной класс должен реализовывать [INCWidgetProviding](https://developer.xamarin.com/api/type/NotificationCenter.INCWidgetProviding/).

> [!IMPORTANT]
> При запуске проекта с помощью одного шаблонов расширения, предоставляемые Visual Studio для Mac, большинство (если не все) эти требования к предоставлен и выполнены для вас автоматически с помощью шаблона.

## <a name="walkthrough"></a>Пошаговое руководство 

В следующем пошаговом руководстве вы создадите пример **сегодня** мини-приложения, который вычисляет количество дней, оставшихся в год и день:

[![](extensions-images/carpediemscreenshot-sm.png "Сегодня мини-приложение пример, вычисляющую день и количество дней, оставшихся в год")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>Создание решения

Чтобы создать необходимые решения, сделайте следующее:

1. Во-первых, создайте новый iOS, **приложение с одним представлением** проекта и нажмите кнопку **Далее** кнопки: 

    [![](extensions-images/today01.png "Во-первых создайте новый iOS, приложение с одним представлением проекта и нажмите кнопку \"Далее\"")](extensions-images/today01.png#lightbox)
2. Назовите проект `TodayContainer` и нажмите кнопку **Далее** кнопки: 

    [![](extensions-images/today02.png "Назовите проект TodayContainer и нажмите кнопку \"Далее\"")](extensions-images/today02.png#lightbox)
3. Проверьте **имя_проекта** и **SolutionName** и нажмите кнопку **создать** кнопку, чтобы создать решение: 

    [![](extensions-images/today03.png "Проверьте имя проекта и SolutionName и нажмите кнопку «Создать», чтобы создать решение")](extensions-images/today03.png#lightbox)
4. Затем в **обозревателе решений**, щелкните правой кнопкой мыши решение и добавьте новый **iOS расширение** проекта из **сегодня расширение** шаблона: 

    [![](extensions-images/today04.png "Затем в обозревателе решений щелкните правой кнопкой мыши решение и добавьте новый проект расширения iOS из шаблона расширения уже сегодня")](extensions-images/today04.png#lightbox)
5. Назовите проект `DaysRemaining` и нажмите кнопку **Далее** кнопки: 

    [![](extensions-images/today05.png "Назовите проект DaysRemaining и нажмите кнопку \"Далее\"")](extensions-images/today05.png#lightbox)
6. Просмотрите проект и нажмите кнопку **создать** кнопку, чтобы создать его: 

    [![](extensions-images/today06.png "Просмотрите проект и нажмите кнопку «Создать», чтобы создать его")](extensions-images/today06.png#lightbox)

Полученное решение должна быть два проекта, как показано ниже:

[![](extensions-images/today07.png "Полученное решение должны появиться два проекта, как показано ниже")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>Создание расширения пользовательского интерфейса

Далее будет необходимо для разработки интерфейса для вашего **сегодня** мини-приложения. Это можно сделать с помощью раскадровки, либо путем создания пользовательского интерфейса в коде. Оба метода будут подробно рассмотрены ниже.

#### <a name="using-storyboards"></a>С помощью раскадровки

Для создания пользовательского интерфейса с помощью раскадровки, сделайте следующее:

1. В **обозревателе решений**, дважды щелкните проект расширения `Main.storyboard` файл, чтобы открыть его для редактирования: 

    [![](extensions-images/today08.png "Дважды щелкните файл Main.storyboard проектов расширения, чтобы открыть его для редактирования")](extensions-images/today08.png#lightbox)
2. Выберите метку, который был автоматически добавлен к пользовательскому Интерфейсу шаблоном и присвойте ему **имя** `TodayMessage` в **мини-приложение** вкладке **обозревателе свойств**: 

    [![](extensions-images/today09.png "Выберите метку, который был автоматически добавлен к пользовательскому Интерфейсу шаблоном и присвойте ему имя TodayMessage на вкладке \"мини-приложение\" обозреватель свойств")](extensions-images/today09.png#lightbox)
3. Сохраните изменения в раскадровку.

#### <a name="using-code"></a>С помощью кода

Чтобы создать пользовательский Интерфейс в коде, сделайте следующее: 

1. В **обозревателе решений**выберите **DaysRemaining** проекта, добавьте новый класс и назовите его `CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect DaysRemaining проекта, добавьте новый класс и назову CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. Опять же, в **обозревателе решений**, дважды щелкните файл с расширением `Info.plist` файл, чтобы открыть его для редактирования: 

    [![](extensions-images/code02.png "Дважды щелкните файл Info.plist расширения, чтобы открыть его для редактирования")](extensions-images/code02.png#lightbox)
3. Выберите **представление источника** (из нижней части экрана) и откройте `NSExtension` узла: 

    [![](extensions-images/code03.png "Выберите представление источника в нижней части экрана, а затем разверните узел NSExtension")](extensions-images/code03.png#lightbox)
4. Удалить `NSExtensionMainStoryboard` ключа и добавьте `NSExtensionPrincipalClass` со значением `CodeBasedViewController`: 

    [![](extensions-images/code04.png "Удалить ключ NSExtensionMainStoryboard и добавьте NSExtensionPrincipalClass со значением CodeBasedViewController")](extensions-images/code04.png#lightbox)
5. Сохраните изменения.

Далее следует изменить `CodeBasedViewController.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewController")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

Обратите внимание, что `[Register("CodeBasedViewController")]` совпадает со значением, которое было указано для `NSExtensionPrincipalClass` выше.

### <a name="coding-the-extension"></a>Создание кода расширения

С помощью пользовательского интерфейса, созданного, откройте `TodayViewController.cs` или `CodeBasedViewController.cs` файла (в зависимости от метода, используемого для создания пользовательского интерфейса выше), изменение **ViewDidLoad** метода и это должно выглядеть следующим образом:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

Если с помощью кода на основе метода интерфейса пользователя, замените `// Insert code to power extension here...` комментарий с новым кодом выше. После того, как вызов базовой реализации (и вставка метку для версии на основе кода), этот код выполняет простые вычисления, чтобы получить день года и сколько дней осталось. Затем оно отображает сообщение в метке (`TodayMessage`), созданный при разработке пользовательского интерфейса.

Обратите внимание на то, насколько похоже этот процесс заключается в обычный процесс написания приложения. Расширение `UIViewController` имеет одинаковый жизненный цикл контроллера представления в приложении, за исключением того, расширения нет фоновых режимов и не приостановлен, когда пользователь завершил работу с их помощью. Вместо этого расширения повторно инициализируются и отменить выделение при необходимости.

### <a name="creating-the-container-app-user-interface"></a>Создание пользовательского интерфейса приложения контейнера

В этом пошаговом руководстве приложения-контейнера, просто используется как метод поставки и установки расширения и не добавляет новых функций в свои собственные. Изменить TodayContainer `Main.storyboard` файл и добавить какой-либо текст, определение функции расширения и его установке:

[![](extensions-images/today10.png "Измените файл TodayContainers Main.storyboard и добавить какой-либо текст, определение функции расширения и его установке")](extensions-images/today10.png#lightbox)

Сохраните изменения в раскадровку.

### <a name="testing-the-extension"></a>Тестирование расширения

Чтобы проверить расширение в симуляторе iOS, запустите **TodayContainer** приложения. Появится основное представление контейнера:

[![](extensions-images/run01.png "Появится основное представление контейнеров")](extensions-images/run01.png#lightbox)

Затем попадания **Главная** кнопки в симуляторе, проведите вниз из верхней части экрана, чтобы открыть **центр уведомлений**выберите **сегодня** вкладку и нажмите кнопку **Изменить** кнопки:

[![](extensions-images/run02.png "Нажмите кнопку «Главная» в симуляторе, проведите вниз из верхней части экрана, чтобы открыть центр уведомлений, выберите вкладку \"сегодня\" и нажмите кнопку \"Изменить\"")](extensions-images/run02.png#lightbox)

Добавить **DaysRemaining** расширение **сегодня** просмотра и нажмите кнопку **сделать** кнопки:

[![](extensions-images/run03.png "Добавить расширение DaysRemaining представление \"сегодня\" и нажмите кнопку \"Готово\"")](extensions-images/run03.png#lightbox)

Новое мини-приложение, добавляемое к **сегодня** представление и результаты будут отображаться:

[![](extensions-images/run04.png "Представление \"сегодня\" добавляется новое мини-приложение и отображаются результаты")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>Взаимодействие с приложением узла

Пример сегодня расширения, созданную ранее не взаимодействует с его приложение host ( **сегодня** экрана). В противном случае он будет использовать [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) свойство `TodayViewController` или `CodeBasedViewController` классы. 

Для расширений, которые будет получать данные из их размещение приложений, данные хранятся в виде массива [NSExtensionItem](https://developer.xamarin.com/api/type/Foundation.NSExtensionItem/) объектов, хранящихся в [InputItems](https://developer.xamarin.com/api/property/Foundation.NSExtensionContext.InputItems/) свойство [ExtensionContext ](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) расширения `UIViewController`.

Другое расширение, например расширения редактирования фотографий, может различать пользователей, завершение или Отмена использования. Это будет передаваться обратно в ведущее приложение с помощью [CompleteRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CompleteRequest/) и [CancelRequest](https://developer.xamarin.com/api/member/Foundation.NSExtensionContext.CancelRequest/) методы [ExtensionContext](https://developer.xamarin.com/api/type/Foundation.NSExtensionContext/) свойство.

Дополнительные сведения см. в разделе Apple [руководство по программированию расширения приложения](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1).

## <a name="communicating-with-the-parent-app"></a>Взаимодействие с в родительское приложение

Группы приложений предоставляют различным приложениям (или одному приложению и его расширениям) общее место хранения файлов. Группы приложений можно использовать для следующих данных:

- [Параметры Apple Watch](~/ios/watchos/app-fundamentals/settings.md).
- [Общие параметры NSUserDefaults](~/ios/app-fundamentals/user-defaults.md).
- [Общие файлы](~/ios/watchos/app-fundamentals/parent-app.md#files).

Дополнительные сведения см. в разделе [группы приложений](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) части нашей **работа с возможностями** документации.

## <a name="mobilecoreservices"></a>MobileCoreServices

При работе с расширениями используйте универсальный идентификатор типа (UTI) для создания и обработки данных, которыми обмениваются приложения, другие приложения и/или службы.

`MobileCoreServices.UTType` Статический класс определяет следующие вспомогательные свойства, связанные с Apple `kUTType...` определений:

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

См. в следующем примере:

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

Дополнительные сведения см. в разделе [группы приложений](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) части нашей **работа с возможностями** документации.

## <a name="precautions-and-considerations"></a>Меры предосторожности и рекомендации

Модули имеют значительно меньше памяти, доступные на них, чем выполнение приложений. Они, должны выполняться быстро и с минимальными вторжений и приложение, которое они размещаются в. Тем не менее расширение должно также предоставляют функцию различение, что удобно для принимающего приложения с фирменной символикой пользовательский Интерфейс, который позволяет пользователю определить расширение разработчика или приложения-контейнера, к которым они принадлежат.

Учитывая эти тесной требование, следует развертывать расширения, которые были тщательно протестированы и оптимизированы для повышения производительности и потребления памяти. 

## <a name="summary"></a>Сводка

В этом документе подробно рассматривается расширения, доступные им, тип точки расширения и известные ограничения, накладываемые iOS на расширение. Это обсуждалось, создание, распространение, Установка и запуск расширения и жизненный цикл расширения. Она предоставляла Пошаговое руководство по созданию простого **сегодня** мини-приложения, показывающая два способа создания графического элемента пользовательского интерфейса с помощью раскадровки или кода. В этом примере показано тестирование расширения в симуляторе iOS. И, наконец кратко описано взаимодействие с ведущее приложение и ряд мер предосторожности и рекомендации, которые следует принимать при разработке расширение. 

## <a name="related-links"></a>Связанные ссылки

- [ContainerApp (пример)](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [Создание расширений в Xamarin.iOS (видео)](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
