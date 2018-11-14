---
title: Сочетания клавиш Siri в Xamarin.iOS
description: В этом документе описывается использование Siri клавиш в iOS 12. В нем описывается NSUserActivities, настраиваемые цели назначение клавиш голоса, соответствующие сочетания клавиш и многое другое.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 38e71914d9f7f6bb46ebeee8d548968c207d3b16
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617765"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Сочетания клавиш Siri в Xamarin.iOS

В [iOS 10](~/ios/platform/sirikit/index.md), Apple представила SiriKit, благодаря чему возможна сборки обмена сообщениями, функций VoIP-вызовов, платежи, тренировки, воспользуйтесь преимуществами нового заказа столиков в ресторане и фото поиска приложений, взаимодействующих с Siri.

В [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit положены поддержка дополнительных типов приложений и большую гибкость для настройки пользовательского интерфейса.

iOS 12 добавляет клавиш Siri, позволяя всех типов приложений, чтобы предоставлять свою функциональность Siri. Siri узнает, когда определенные задачи на основе приложений больше всего подходят для пользователя и использует эти сведения, чтобы предложить возможные действия с помощью _клавиш_. При касании на ярлык или вызова этого метода с помощью голосовых команд будет открыть приложение или выполнять фоновую задачу.

Сочетания клавиш можно использовать для ускорения пользователя возможности для выполнения распространенных задач — во многих случаях даже не открывая приложение.

## <a name="sample-app-soup-chef"></a>Пример приложения: полный курс по работе Chef

Чтобы лучше понять Siri ярлыки, взгляните на [Soup Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/) примера приложения. Полный курс по работе Chef позволяет пользователям размещать заказы из мнимой soup ресторан, просмотреть историю их заказов и определить фраз для использования при упорядочивании полный курс по работе, взаимодействуя с Siri.

> [!TIP]
> Перед тестированием Chef полный курс по работе на устройстве или симуляторе iOS 12, включите следующие два параметра, которые полезны при отладке сочетания клавиш:
>
> - В **параметры** приложение, включить **разработчика > последние сочетания клавиш отображения**.
> - В **параметры** приложение, включить **разработчика > пожертвований отображения на экране блокировки**.
>
> Эти отладки без труда найти недавно созданные марки параметры (а не прогнозу) ярлыки на iOS блокировка экрана и экрана поиска.

Чтобы использовать пример приложения:

- Установка и запуск примера приложения Chef полный курс по работе на симуляторе iOS 12 или [устройства](#testing-on-device).
- Нажмите кнопку **+** кнопки в правом верхнем углу для создания нового заказа.
- Выберите тип полный курс по работе, укажите количество и параметры и коснитесь **заказать**.
- На **История заказа** коснитесь только что созданный порядок его просмотра.
- В нижней части экрана сведений заказа, коснитесь **добавить Siri**.
- Запись голоса фразу, чтобы связать с порядком и коснитесь **сделать**.
- Свести к минимуму Soup Chef, вызывать Siri и снова разместить заказ, добавив фразу голосовой записанного.
- По завершении порядок Siri повторно открыть полный курс по работе Chef и обратите внимание на то, что новый порядок указано на **История заказа** экрана.

Этот пример демонстрирует способы:

- [Использования NSUserActivity сочетания клавиш для открытия приложения](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Использовать настраиваемый намерений ярлык для выполнения задачи](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Предоставляют пользовательский интерфейс для пользовательских намерение](#providing-a-user-interface-for-a-custom-intent).
- [Создайте ярлык голосовой](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info.plist и Entitlements.plist

Прежде чем рассказывать, более глубоко Chef полный курс по работе кода, взгляните на его **Info.plist** и **Entitlements.plist** файлов.

### <a name="infoplist"></a>Info.plist

**Info.plist** файл **SoupChef** проект определяет **идентификатор пакета** как `com.xamarin.SoupChef`. Этот идентификатор пакета будет использоваться в качестве префикса для пакета идентификаторы Intents и Intents UI расширения, который обсуждается далее в этом документе.

**Info.plist** файл также содержит следующее:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Это `NSUserActivityTypes` пары "ключ/значение" Указывает, что полный курс по работе Chef знает, как обрабатывать `OrderSoupIntent`и [ `NSUserActivity` ](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/) необходимости [ `ActivityType` ](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.ActivityType/) из «com.xamarin.SoupChef.viewMenu».

Действия и настраиваемые цели, переданный в приложение, в отличие от его расширения обрабатываются в `AppDelegate` ( [ `UIApplicationDelegate` ](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/)), [ `ContinueUserActivity` ](https://developer.xamarin.com/api/member/UIKit.UIApplicationDelegate.ContinueUserActivity/) метод.

### <a name="entitlementsplist"></a>Entitlements.plist

**Entitlements.plist** файл **SoupChef** проект содержит следующее:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Эта конфигурация показывает, что приложение использует эту группу приложений «group.com.xamarin.SoupChef». **SoupChefIntents** расширение приложения использует ту же группу приложений, что позволяет два проекта для совместного использования [`NSUserDefaults`](https://developer.xamarin.com/api/type/Foundation.NSUserDefaults/)
данные.

`com.apple.developer.siri` Ключ указывает, что приложение взаимодействует с Siri.

> [!NOTE]
> **SoupChef** наборов конфигурации сборки проекта **настраиваемые назначения** для **Entitlements.plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>С помощью NSUserActivity ярлык для открытия приложения

Чтобы создать ярлык, который открывает вывод определенное содержимое в приложении, создайте `NSUserActivity` и присоединить ее к контроллера представления для экрана, хотелось бы ярлык, чтобы открыть.

### <a name="setting-up-an-nsuseractivity"></a>Настройка NSUserActivity

На экране меню `SoupMenuViewController` создает `NSUserActivity` и присваивает его на контроллер представления [ `UserActivity` ](https://developer.xamarin.com/api/property/UIKit.UIResponder.UserActivity/) свойство:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Установка `UserActivity` свойство _donates_ действия для Siri. Из этого пожертвование Siri получает сведения о где и когда это действие относится к пользователю и узнает, чтобы лучше предложить его в будущем.

`NSUserActivityHelper` Служебный класс включается в **SoupChef** решения в **SoupKit** библиотеки классов. Он создает `NSUserActivity` и задаются различные свойства, связанные с Siri и поиска:

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

В частности Обратите внимание на следующее:

- Установка `EligibleForPrediction` для `true` указывает, что Siri можно прогнозировать это действие и область его ярлык.
- [ `ContentAttributeSet` ](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.ContentAttributeSet/) Массив — это стандарт [ `CSSearchableItemAttributeSet` ](https://developer.xamarin.com/api/type/CoreSpotlight.CSSearchableItemAttributeSet/) используется для включения `NSUserActivity` в результатах поиска iOS.
- [`SuggestedInvocationPhrase`](https://developer.xamarin.com/api/property/Foundation.NSUserActivity.SuggestedInvocationPhrase/) является фразой, Siri предложит пользователю как потенциальные вариант, при назначении фразы для ярлыка.

### <a name="handling-an-nsuseractivity-shortcut"></a>Обработка NSUserActivity ярлык

Для обработки `NSUserActivity` ярлык, пользователь, приложение iOS необходимо переопределить `ContinueUserActivity` метод `AppDelegate` , отвечать на запросы на основе `ActivityType` поле переданного `NSUserActivity` объекта:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Этот метод вызывает метод `HandleUserActivity`, который находит переход на экран меню и он вызывается:

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Присвоение NSUserActivity фразы

Чтобы назначить фразы для `NSUserActivity`, откройте iOS **параметры** приложения и выберите **Siri и поиск > ярлыки**. Затем выберите ярлык (в данном случае «Порядок завтрак») и записывать фразу.

Вызвав Siri и используя эта фраза будет открыт Chef полный курс по работе на экран меню.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>С помощью пользовательских намерений ярлыка для выполнения задачи

### <a name="defining-a-custom-intent"></a>Определение пользовательских намерение

Чтобы установить ярлык, который позволяет пользователю быстро выполнения конкретной задачи, относящиеся к приложению, создайте настраиваемые цели. Пользовательские намерение представляет задачу, пользователю может потребоваться завершить, параметров, относящихся к этой задачи и возможные ответы, полученные в результате выполнения задачи. В зависимости от того, как пользовательские намерение определяется вызова этого метода можно открыть свое приложение или запустить в фоновом режиме.

Используйте Xcode 10, чтобы создать настраиваемые цели. В [репозитория SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), пользовательские намерение определяется в **OrderSoupIntentCodeGen**, это проект с Objective-C. Открыть этот проект и выберите **Intents.intentdefinition** файл **навигатор проекта** для просмотра **OrderSoup** намерение.

Обратите внимание на следующее:

- Имеет целью **категории** из **порядок**. Существуют различные заранее заданным категориям, которые могут использоваться для настраиваемые цели; Выберите тот, который наиболее близко соответствует задачу, которая позволит пользовательских намерения. Так как это полный курс по работе, упорядочение приложения, **OrderSoupIntent** использует **порядок**.
- **Подтверждение** флажок указывает, должны ли Siri запрашивать подтверждение перед выполнением задачи. Для **Soup порядок** намерений в Chef полный курс по работе, этот параметр включен, так как пользователь делает покупки.
- **Параметры** раздел файла .intentdefinition определяет параметры, относящиеся к ярлык. Чтобы заказать полный курс по работе, Soup Chef необходимо знать тип полный курс по работе, его количество и любые связанные параметры.
Каждый параметр имеет тип; параметр, который не может быть представлен Предопределенный тип задаются в качестве **Custom**.
- **Сочетания типов** интерфейс описывает различные сочетания параметров, можно использовать Siri, предлагая ярлык. Связанный **Title** и **подзаголовок** разделы позволяют определять сообщения, Siri будет использовать при проведении предлагаемые ярлык для пользователя.
- **Поддерживает выполнение фонового** должен быть установлен флажок для любой ярлык, который может выполняться без открытия в приложении для дальнейшего участия пользователя.

### <a name="defining-custom-intent-responses"></a>Определение пользовательских ответов намерений

**Ответа** элемента, вложенного ниже **OrderSoup** намерение представляет возможные ответы, возникающие в результате порядок полный курс по работе.

В **OrderSoup** определения ответа в цель, обратите внимание на следующее:

- Ответ **свойства** можно использовать, чтобы настроить сообщение, передается обратно пользователю. **OrderSoup** намерений ответ имеет **soup** и **waitTime** свойства.
- **Шаблоны ответов** указать различные успешных и неудачных сообщений, которые могут использоваться для указания состояния после завершения задачи объекта intent.
- **Успех** должен быть установлен флажок для ответов, означает успех операции.
 - **OrderSoupIntent** использует доступ предоставлен, ответ **soup** и **waitTime** свойства, чтобы предоставить понятные и полезные сообщение, описывающее, когда будут готовы порядок полный курс по работе.

### <a name="generating-code-for-the-custom-intent"></a>Создание кода для пользовательского намерение

Построение проекта Xcode, содержащий это определение настраиваемого намерений приводит к Xcode для создания кода, который может использоваться как программно взаимодействовать с настраиваемые цели и его ответы.

Чтобы просмотреть созданный код:

- Откройте **AppDelegate.m**.
- Добавьте Импорт пользовательских намерение заголовочный файл: `#import "OrderSoupIntent.h"`
- В любом методе в классе добавьте ссылку на `OrderSoupIntent`.
- Щелкните правой кнопкой мыши `OrderSoupIntent` и выберите **перейти к определению**.
- Щелкните правой кнопкой мыши, в файле вновь открыть **OrderSoupIntent.h**и выберите **Показать в Finder**.
- Откроется **Finder** окно, содержащее .h и .m файл, содержащий созданный код.

Этот сформированный код включает:

- `OrderSoupIntent` — Класс, представляющий настраиваемые цели.
- `OrderSoupIntentHandling` Протокол, который определяет методы, которые будут использоваться для подтверждения того, что цель должна выполняться и метод, который фактически и выполняет его.
- `OrderSoupIntentResponseCode` — Перечисление, которое определяет различные состояния ответа.
- `OrderSoupIntentResponse` — класс, представляющий ответ на выполнение объекта intent.

### <a name="creating-a-binding-to-the-custom-intent"></a>Создание привязки в пользовательских намерения

Чтобы использовать код, сгенерированный Xcode в приложении Xamarin.iOS, создайте C# привязки для него.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Создание статической библиотеки и C# определений привязки

В [репозитория SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), взгляните на **OrderSoupIntentStaticLib** и откройте **OrderSoupIntentStaticLib.xcodeproj** проекта Xcode.

Это **Cocoa Touch статическая библиотека** проект содержит **OrderSoupIntent.h** и **OrderSoupIntent.m** файлы, созданные в Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Настройка параметров построения проекта статической библиотеки

В Xcode **навигатор проекта**, выберите проект верхнего уровня, **OrderSoupIntentStaticLib**и перейдите к **этапы сборки > источники компиляции**.
Обратите внимание, что **OrderSoupIntent.m** (которая импортирует **OrderSoupIntent.h**) приводится здесь. В **ссылку двоичного файла с библиотеками**, обратите внимание, что **Intents.framework** и **Foundation.framework** включены.
Эти параметры заданы платформа будет построен неправильно.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Создание статической библиотеки и создание C# определения привязок

Для создания статической библиотеки и создать C# определения привязки для него, выполните следующие действия:

- [Установить цель Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), средство, используемое для создания определений привязки из файлов .h и .m, созданный с помощью Xcode.

- Настройте систему таким образом, чтобы использовать средства командной строки Xcode 10:

    > [!WARNING]
    > Обновление выбранного средства командной строки влияет на всех установленных версий Xcode на компьютере. Когда вы закончите Soup Chef примера использования приложения, не забудьте отменить это задание к ее исходной конфигурации.

    - В Xcode, выберите **Xcode > предпочтения > расположения** и задайте **средства командной строки** наиболее текущей установке Xcode 10, доступных в системе.

- В окне терминала `cd` для **OrderSoupIntentStaticLib** каталога.

- Тип `make`, какие сборки:

    - Статическая библиотека, **libOrderSoupIntentStaticLib.a**
    - В **bo** выходной каталог C# определения привязки:
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

**OrderSoupIntentBindings** проект, который использует этот статическую библиотеку и их связанные привязки определений, автоматически создает эти элементы.
Тем не менее запуска вручную с помощью описанный выше процесс гарантирует, что построение выполняется должным образом.

Дополнительные сведения о создания статической библиотеки, а также создать с помощью цели Sharpie C# определения привязок, взгляните на [привязке библиотеки Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) Пошаговое руководство.

#### <a name="creating-a-bindings-library"></a>Создание библиотеки привязки

С помощью статической библиотеки и C# определения привязки создавать, оставшиеся фрагмент необходимо использовать, созданный с помощью Xcode — это код, связанный с намерением в проекте Xamarin.iOS библиотеку привязок.

В [репозитория Soup Chef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), откройте **SoupChef.sln** файла. Помимо прочего, это решение содержит **OrderSoupIntentBinding**, библиотека привязок для статической библиотеки, созданный выше.

В частности, обратите внимание, что этот проект содержит:

- **ApiDefinitions.cs** — файл, созданный выше Sharpie цели и добавления к данному проекту. Этот файл **действие при построении** присваивается **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** — еще один файл формируемой выше Sharpie цели и добавления к данному проекту. Этот файл **действие при построении** присваивается **ObjcBindingCoreSource**.
- Объект **собственную ссылку** для **libOrderSoupIntentStaticLib.a**, статическая библиотека, построен ранее.

> [!NOTE]
> Оба **ApiDefinitions.cs** и **StructsAndEnums.cs** содержат атрибуты, такие как `[Watch (5,0), iOS (12,0)]`. Эти атрибуты, созданные Sharpie цели были закомментированы, поскольку они не являются обязательными для этого проекта.

Дополнительные сведения о создании C# библиотека привязок, ознакомьтесь с [привязке библиотеки Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) Пошаговое руководство.

Обратите внимание, что **SoupChef** проект содержит ссылку на **OrderSoupIntentBinding**, что означает, что он теперь доступны, в C#, классы, интерфейсы и перечисления, он содержит:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Добавление файла .intentdefinition в решение

В C# **SoupChef** решения, **SoupKit** проект содержит код, совместно использовать приложения и его расширения. **Intents.intentdefinition** файл был переведен в **Base.lproj** каталог **SoupKit**, и он имеет **действие при построении** из **Содержимого**. Процесс сборки копирует этот файл в пакет приложений Soup Chef, когда он необходим для правильной работы приложения.

### <a name="donating-an-intent"></a>Благотворительности объекта intent

Чтобы предложить ярлык Siri его необходимо понимать при относится ярлык.

Чтобы Siri этот об, Soup Chef _donates_ объекта intent для Siri каждый раз пользователь размещает заказ солянка из. Зависимости от этого пожертвование —, когда он был, пожертвования, где он был пожертвования, содержит параметры – Siri узнает, когда следует предлагать ярлык в будущем.

**SoupChef** использует `SoupOrderDataManager` класса для размещения пожертвований.
При вызове для размещения заказа на полный курс по работе для пользователя, `PlaceOrder` в свою очередь вызывает метод [ `DonateInteraction` ](https://developer.xamarin.com/api/member/Intents.INInteraction.DonateInteraction/):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

После получения объекта intent, оно помещается в [ `INInteraction` ](https://developer.xamarin.com/api/type/Intents.INInteraction/).
`INInteraction` Прав [`Identifier`](https://developer.xamarin.com/api/property/Intents.INInteraction.Identifier/)
Уникальный идентификатор заказа (это будет полезно позже при удалении намерений пожертвований, которые больше не являются допустимыми), которое соответствует. Затем взаимодействие списания для Siri.

Вызов `order.Intent` выборки считывания `OrderSoupIntent` , представляющий порядок, задав его `Quantity`, `Soup`, `Options`и образ и вызов фразу для использования в качестве подсказки, когда пользователь регистрирует фразы для Siri связать с целью:

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Удаление недопустимый пожертвования

Важно удалить пожертвований, которые больше не являются допустимыми, чтобы Siri не выполняет предложения ярлык бесполезных или сбивающим с толку.

С помощью Chef Soup **Настройка меню** экрана можно использовать для пометки элемента считается недоступным меню. Siri больше не должна предлагать ярлык для упорядочивания элемента меню недоступны, поэтому `RemoveDonation` метод `SoupMenuManager` удаляет пожертвования для пунктов меню, которые больше не доступны. Это достигается за счет:

- Поиск заказов, связанных с элементом меню сейчас недоступна.
- Персональные их идентификаторам.
- Удаление взаимодействий с этой же идентификатором.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Создание расширения Intents

Код, выполняемый, когда Siri вызывает объект intent помещается в расширение Intents, которое можно добавить как новый проект в решение приложения Xamarin.iOS например Chef супа. В **SoupChef** решения, расширение вызывается **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents — Info.plist и Entitlements.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info.plist

**Info.plist** в **SoupChefIntents** проект определяет **идентификатор пакета** как `com.xamarin.SoupChef.SoupChefIntents`.

**Info.plist** файл также содержит следующее:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

В указанных выше **Info.plist**:

- `IntentsRestrictedWhileLocked` список целей, которые должны обрабатываться только когда устройство разблокировано.
- `IntentsSupported` Перечисляет объекты Intent, обрабатываются этим модулем.
- `NSExtensionPointIdentifier` Указывает тип расширения приложения (см. в разделе [документации Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) Дополнительные сведения).
- `NSExtensionPrincipalClass` Задает класс, который должен использоваться для обработки намерения, поддерживаемые этим расширением.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – Entitlements.plist

**Entitlements.plist** в **SoupChefIntents** проект имеет **группы приложений** возможностей. Эта возможность настроен для использования той же группе приложений, что **SoupChef** проекта:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Полный курс по работе Chef сохраняет данные с `NSUserDefaults`. Для совместного использования данных между приложением и расширение приложения, они ссылаются на той же группе приложений в их **Entitlements.plist** файлов.

> [!NOTE]
> **SoupChefIntents** наборов конфигурации сборки проекта **настраиваемые назначения** для **Entitlements.plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Обработка OrderSoupIntent фоновой задачи

Расширение Intents выполняет необходимые фоновые задачи для ярлыка, в зависимости от пользовательских намерение.

Вызовы Siri [ `GetHandler` ](https://developer.xamarin.com/api/member/Intents.INExtension.GetHandler/) метод `IntentHandler` класс (определенный в **Info.plist** как `NSExtensionPrincipalClass`) для получения экземпляра класса, который расширяет `OrderSoupIntentHandling`, который может использоваться для обработки `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, определенный в **SoupKit** проекта общего кода, реализует два важных метода:

- `ConfirmOrderSoup` — Подтверждает ли должна выполняться задача, связанная с целью
- `HandleOrderSoup` — Размещает заказ полный курс по работе и взаимодействует с пользователем, вызвав обработчик завершения в переданном

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Обработка OrderSoupIntent, открыть приложение

Приложения должны правильно обрабатывать намерений, которые не работают в фоновом режиме.
Эти параметры обрабатываются в так же, как `NSUserActivity` сочетания клавиш в `ContinueUserActivity` метод `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Предоставление пользовательского интерфейса для пользовательского намерение

Расширение интерфейса Intents UI предоставляет пользовательский интерфейс для расширения Intents. В **SoupChef** решения, **SoupChefIntentsUI** — это расширение интерфейса Intents UI, которое предоставляет интерфейс для **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI — Info.plist и Entitlements.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

**Info.plist** в **SoupChefIntentsUI** проект определяет **идентификатор пакета** как `com.xamarin.SoupChef.SoupChefIntentsui`.

**Info.plist** файл также содержит следующее:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

В указанных выше **Info.plist**:

- `IntentsSupported` Указывает, что `OrderSoupIntent` обрабатывается это расширение интерфейса Intents UI.
- `NSExtensionPointIdentifier` Указывает тип расширения приложения (см. в разделе [документации Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) Дополнительные сведения).
- `NSExtensionMainStoryboard` Указывает, раскадровки, который определяет основной интерфейс этого расширения

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements.plist

**SoupChefIntentsUI** проекту не нужен **Entitlements.plist** файла.

### <a name="creating-the-user-interface"></a>Создание пользовательского интерфейса

Так как **Info.plist** для **SoupChefIntentsUI** задает `NSExtensionMainStoryboard` ключа `MainInterface`, **MainInterace.storyboard** файл определяет интерфейс для расширение интерфейса Intents UI.

В этой раскадровке является контроллером единое представление типа **IntentViewController**. Он ссылается на два представления:

- **invoiceView**, типа `InvoiceView`
- **confirmationView**, типа `ConfirmOrderView`

> [!NOTE]
> Интерфейсы для **invoiceView** и **confirmationView** определяются в **Main.storyboard** как вторичный представления. IOS Designer в Visual Studio для Mac и Visual Studio 2017 не обеспечивает поддержку для просмотра или изменения вторичного представления; Чтобы сделать это, откройте **Main.storyboard** в Interface Builder в Xcode.

`IntentViewController` реализует [`IINUIHostedViewControlling`](https://developer.xamarin.com/api/type/IntentsUI.IINUIHostedViewControlling/)
интерфейс, используемый для предоставления пользовательского интерфейса, при работе с намерениями Siri. В [`ConfigureView`](https://developer.xamarin.com/api/member/IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView/)
метод вызывается для настройки интерфейса, отображение, подтверждение или накладной, в зависимости от того подтверждается ли взаимодействие ([`INIntentHandlingStatus.Ready`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)) или будет выполнено успешно ([ `INIntentHandlingStatus.Success`](https://developer.xamarin.com/api/type/Intents.INIntentHandlingStatus/)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Дополнительные сведения о `ConfigureView` метод, презентация, посвященная Apple WWDC 2017, [новые возможности в SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Создание ярлыка голоса

Полный курс по работе Chef предоставляет интерфейс для назначить голоса ярлык для каждого заказа, делая возможным soup заказа с Siri. На самом деле интерфейс, используемый для записи и назначения клавиш голоса в iOS и требует небольшого объема кода.

В `OrderDetailViewController`, когда пользователь касается таблицы **добавить Siri** строки, [ `RowSelected` ](https://developer.xamarin.com/api/member/UIKit.UITableViewSource.RowSelected/) метод появится экран для добавления или изменить ярлык voice:

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

Зависимости от того, существует ли существующий голосовой ярлык для заказа отображаемого `RowSelected` представляет контроллер представления типа [ `INUIEditVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIEditVoiceShortcutViewController/) или [ `INUIAddVoiceShortcutViewController` ](https://developer.xamarin.com/api/type/IntentsUI.INUIAddVoiceShortcutViewController/).
В каждом случае `OrderDetailViewController` устанавливается как контроллер представления `Delegate`, поэтому он также реализует [`IINUIAddVoiceShortcutViewControllerDelegate`](https://developer.xamarin.com/api/type/IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate/)
и [ `IINUIEditVoiceShortcutViewControllerDelegate` ](https://developer.xamarin.com/api/type/IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate/).

## <a name="testing-on-device"></a>Тестирование на устройстве

Чтобы запустить полный курс по работе Chef на устройстве, выполните приведенные ниже инструкции. Также прочитайте [примечание об автоматической подготовки](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Группы приложений, идентификаторов приложений, профили подготовки

В **сертификаты, идентификаторы и профили** раздел [портал разработчиков Apple](https://developer.apple.com/), выполните следующие действия:

- Создание группы приложений для обмена данными между приложением Chef полный курс по работе и его расширения. Например: **group.com.yourcompanyname.SoupChef**

- Создайте три идентификаторов приложений: один для самого приложения, для расширения Intents и для расширения интерфейса Intents UI. Пример:

    - Приложение: **com.yourcompanyname.SoupChef**
        - Чтобы этот идентификатор приложения назначьте SiriKit и **группы приложений** возможности.

    - Расширение intents: **com.yourcompanyname.SoupChef.Intents**
        - Чтобы этот идентификатор приложения назначьте **группы приложений** возможностей.

    - Расширение intents UI: **com.yourcompanyname.SoupChef.Intentsui**
        - Этот идентификатор приложения должен нет специальных возможностей.

- После создания выше идентификаторов приложений, измените **группы приложений** созданную возможность назначать приложения и расширение Intents, указав группу конкретного приложения.

- Создайте три новые профили подготовки для разработки, одной для каждого из новых идентификаторов приложений.

- Скачайте эти профили подготовки и дважды щелкните каждый из них, чтобы установить его. Если Visual Studio для Mac или Visual Studio 2017 уже выполняется, перезапустите его, чтобы убедиться, что он регистрирует новые профили подготовки.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Редактирование Info.plist, Entitlements.plist и исходный код

В Visual Studio для Mac или Visual Studio 2017 сделайте следующее:

- Обновление различных **Info.plist** файлы в решении. Укажите приложение, расширение Intents и расширение интерфейса Intents UI **идентификатор пакета** и идентификаторов приложений, определенный выше:

    - Приложение: **com.yourcompanyname.SoupChef**
    - Расширение intents: **com.yourcompanyname.SoupChef.Intents**
    - Расширение intents UI: **com.yourcompanyname.SoupChef.Intentsui**

- Обновление **Entitlements.plist** файл **SoupChef** проекта:
    - Для **группы приложений** возможность, группу на новую группу приложений, созданный ранее (в приведенном выше примере было **group.com.yourcompanyname.SoupChef**).
    - Убедитесь, что **SiriKit** включен.

- Обновление **Entitlements.plist** файл **SoupChefIntents** проекта:
    - Для **группы приложений** возможность, группу на новую группу приложений, созданный ранее (в приведенном выше примере было **group.com.yourcompanyname.SoupChef**).

- Наконец, откройте **NSUserDefaultsHelper.cs**. Задайте `AppGroup` значение новой группы приложений переменной (например, задать его `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Настройка параметров сборки

В Visual Studio для Mac или Visual Studio 2017:

- Откройте параметры и свойства для **SoupChef** проекта. На **подписывание пакета iOS** вкладке **удостоверение подписывания** автоматический и **профиль подготовки** новый конкретного приложения подготовки созданного профиля выше.

- Откройте параметры и свойства для **SoupChefIntents** проекта. На **подписывание пакета iOS** вкладке **удостоверение подписывания** автоматический и **профиль подготовки** для новых инструментов Intents относящихся к модулю профиль подготовки вы создали выше.

- Откройте параметры и свойства для **SoupChefIntentsUI** проекта. На **подписывание пакета iOS** вкладке **удостоверение подписывания** автоматический и **профиль подготовки** с новым интерфейсом пользователя намерения относящихся к модулю профиль подготовки созданную выше.

С изменениями приложение будет выполняться на устройстве iOS.

### <a name="automatic-provisioning"></a>Автоматическая подготовка

Обратите внимание, что можно использовать [автоматическую подготовку](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) для выполнения большинства из них подготовки задачи непосредственно в интегрированной среде разработки.
Тем не менее автоматической подготовки не настроить группы приложений. Необходимо будет вручную настроить **Entitlements.plist** файлы с именем группы приложений, вы бы хотели использовать, посетите портал разработчиков Apple для создания группы приложений, назначьте эту группу приложений для каждого идентификатора приложения, созданные автоматически Подготовка, повторно создайте профили подготовки (приложения, расширение Intents, расширение интерфейса Intents UI), чтобы включить в группу только что созданному приложению, скачайте и установите их.

## <a name="related-links"></a>Связанные ссылки

- [Полный курс по работе с Chef (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Полный курс по работе с Chef (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Введение в Siri клавиш — WWDC 2018 г.](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Создание продукта для голоса с помощью клавиш Siri — WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Сочетания клавиш Siri Siri циферблате — WWDC 2018 г.](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Новые возможности в SiriKit — WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Создание приложения расширение Intents (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
