---
title: Введение в упреждающие предложения в Xamarin.iOS
description: В этой статье показано, как использовать упреждающие предложения в приложении Xamarin.iOS для стимулирования вовлеченности, позволив системе заранее предоставить полезные сведения автоматически пользователю.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 7b7564e3b94062c2294919121f32c4f830346bda
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105341"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Введение в упреждающие предложения в Xamarin.iOS

_В этой статье показано, как использовать упреждающие предложения в приложении Xamarin.iOS для стимулирования вовлеченности, позволив системе заранее предоставить полезные сведения автоматически пользователю._

Знакомы с iOS 10 способов присутствует новости упреждающие предложения для пользователей, чтобы связаться с приложения Xamarin.iOS, заранее присутствует полезные сведения автоматически пользователю в нужное время.

iOS 10 предоставляет новые способы вождения взаимодействия в приложение, позволив системе, чтобы заранее предоставить полезные сведения автоматически пользователю в нужное время. Так же, как iOS 9 включили возможность добавления глубокого поиска в приложение, с помощью Spotlight, Эстафетной и Siri предложения (см. в разделе [новые API-интерфейсы поиска](~/ios/platform/search/index.md)), с iOS 10 приложения могут предоставлять функциональные возможности, могут быть представлены пользователю системой изнутри следующие расположения:

- Переключатель приложения
- На экране блокировки
- CarPlay
- Карты
- Siri взаимодействия
- QuickType предложения

Приложение предоставляет этой функции в систему с помощью набора технологий, таких как `NSUserActivity`, веб-разметка, полезные сведения, MapKit, проигрыватель мультимедиа и UIKit. Кроме того предоставляя поддержку упреждающего предложений для приложения, он получает более глубокую интеграцию Siri бесплатно.

## <a name="location-based-suggestions"></a>Предложения на основе расположения

Знакомы с iOS 10, `NSUserActivity` класс включает `MapItem` свойство, которое позволяет разработчику указывать информацию о расположении, который может использоваться в других контекстах. Например, если приложение отображает отзывы о ресторанах, разработчик может настроить `MapItem` свойства расположение ресторана, который используется для просмотра в приложении. Если пользователь переключается на приложение карты, расположение ресторана доступен автоматически.

Если приложение поддерживает поиска приложения, его можно использовать новые компоненты адрес `CSSearchableItemAttributesSet` класс для указания расположения, которые пользователь может захотеть посетить. Установив `MapItem` свойство, другие свойства являются заполнены по умолчанию.

Дополнение к параметру `Latitude` и `Longitude` свойствах компонента адреса, рекомендуется, чтобы приложение указали `NamedLocation` и `PhoneNumbers` свойства, поэтому Siri могут инициировать вызов к расположению.

## <a name="web-markup-based-suggestions"></a>Предложения на основе веб-разметка

iOS 9, добавлена возможность включить разметку для структурированных данных в веб-сайт, который дополняет содержимое, которое отображается для пользователей в результатах поиска Spotlight и Safari (см. в разделе [поиск с помощью веб-разметка](~/ios/platform/search/web-markup.md)). iOS 10 добавляет возможность включать разметки на основе расположения (такие как [PostalAddress](http://schema.org/PostalAddress) согласно [Schema.org](http://schema.org/)) позволяет еще больше повысить удобство работы пользователя. Например если расположение представления пользователей страницы веб-сайта, система может предложить то же расположение при открытии из Maps.

## <a name="text-based-suggestions"></a>Предложения на основе текста

UIKit была расширена в iOS 10, чтобы включить [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) свойство [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) класс для указания семантическое значение содержимое в текстовое поле. На основе этой информации на месте системы можно обычно автоматически выберите тип сочетания, улучшения предложений автозамены и заранее интегрировать данные из других приложений и веб-сайтов.

Например, если пользователь вводит текст в текстовом поле, помеченное `UITextContentType.FullStreetAddress`, система может предложить автоматическое заполнение поля с расположением пользователя недавно Просмотр.

## <a name="media-based-suggestions"></a>Предложения на основе мультимедиа

Если приложение воспроизводит мультимедиа с помощью [MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API iOS 10 позволяет пользователям просматривать альбома и воспроизведение мультимедиа через приложение на экране блокировки.

## <a name="contextual-siri-reminders"></a>Контекстные Siri напоминания

Позволяет пользователю использовать Siri, чтобы быстро создать напоминание для просмотра содержимого, они в настоящее время просматривать в приложении на более позднюю дату. К примеру, если они просматривать ресторане в приложении, они могут вызвать Siri и сказать *«Напомнить об этом при получении Главная».* Siri вызовет оповещение со ссылкой на проверку в приложении.

## <a name="contact-based-suggestions"></a>Предложения на основе контакта

Позволяет приложению контактов (и контактные сведения) отображались в **обратитесь в службу** приложения на устройстве iOS, а также все контакты пользователей хранятся в системе.

## <a name="ride-sharing-based-suggestions"></a>Воспользуйтесь преимуществами управления доступом на основе предложения

Если приложение для управления доступом поездке использует [MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API, iOS 10 будет представлять их в качестве параметра в переключателе приложений в некоторых случаях в том случае, если пользователь является неприемлемо поездки. Приложение также должен быть зарегистрирован как приложение управления доступом поездке указав `MKDirectionsModeRideShare` для [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) в его `Info.plist` файл.

Если приложение поддерживает только общий доступ к поездке, предложение системы будут начинаться с *«Get поездке для...»*, если поддерживаются другие виды маршрутизации направление (например Walking или велосипед), система будет использовать *«Get указаниям,...»*

> [!IMPORTANT]
> [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/) объект, получающий приложение не содержит сведений, широту и долготу и потребует геокодирования.

## <a name="implementing-proactive-suggestions"></a>Реализация упреждающие предложения

Добавление упреждающего предложение поддержки в приложение Xamarin.iOS обычно так же просто, как реализовать несколько интерфейсов API или расширение несколько интерфейсов API, которые уже должна реализовывать приложения.

Упреждающие предложения работают с приложениями тремя основными способами:

- **`NSUserActivity` и Schema.org**  -  `NSUserActivity` помогает понять, какие сведения на экране его пользователь в настоящее время работает в системе. Schema.org добавляет аналогичные возможности на веб-страницы.
- **Варианты расположения** — Если приложение предоставляет или потребляет сведения на основе расположения, эти расширения API предложение новых способов совместно использовать эту информацию в приложения.
- **Варианты приложений мультимедиа** — системы можно повысить уровень приложения и его содержимое мультимедиа на основе контекста взаимодействия пользователя с устройством iOS.

И поддерживается в приложении путем реализации следующее:

- **Переадресуемое**  -  `NSUserActivity` была добавлена в iOS 8 для поддержки передачи, который разработчик может начать действие на одном устройстве, а затем продолжается на другой (см. в разделе [Общие сведения об Эстафетной](~/ios/platform/handoff.md)).
- **Поиск Spotlight** -iOS 9 добавлена возможность распространять содержимое приложения из в результатах поиска Spotlight с помощью `NSUserActivity` (см. в разделе [поиска с полезные сведения](~/ios/platform/search/corespotlight.md)).
- **Контекстные напоминания Siri** — в iOS 10, `NSUserActivity` была увеличена, чтобы разрешить Siri, чтобы быстро создать напоминание для просмотра содержимого пользователь просматривает в настоящее время в приложении на более позднюю дату.
- **Варианты расположения** -iOS 10 улучшает `NSUserActivity` для записи расположения, просмотреть внутри приложения и повысить их во многих местах во всей системе.
- **Контекстные запросы Siri**  -  `NSUserActivity` предоставляет контекст для сведения, предоставляемые внутри приложения Siri, чтобы пользователь смог перейти направлениях или вызов быть место вызова Siri в приложении.
- **Контакт взаимодействия** — в iOS 10, `NSUserActivity` позволяет приложениям communications повышен от карточку контакта (в приложении "Контакты") как метод альтернативные связи.

Все эти функции имеют одну вещь Общие, то все они используют `NSUserActivity` в той или иной для функционирования форме. 

## <a name="nsuseractivity"></a>NSUserActivity

Как уже говорилось, `NSUserActivity` помогает понять, какие сведения на экране его пользователь в настоящее время работает в системе. `NSUserActivity` состояние недоступно: кэширует механизм для записи действий пользователей, как они навигации по приложению. Например просмотрев приложение ресторана:

[![](proactive-suggestions-images/activity02.png "Состояние недоступно: NSUserActivity механизм кэширования")](proactive-suggestions-images/activity02.png#lightbox)

С помощью следующие взаимодействия:

1. Когда вы работаете с приложением, `NSUserActivity` создается для воссоздания состояния приложения в более поздней версии.
2. Если пользователь выполняет поиск ресторан, идет та же последовательность создания действий.
3. И опять же, в том случае, когда пользователь просматривает результат. В последнем случае при просмотре расположение и в iOS 10, система находится в большей степени поддерживал некоторые основные понятия (например, расположение или связи взаимодействия).

Взгляните ближе на последнем экране:

[![](proactive-suggestions-images/activity03.png "Сведения NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Здесь создается приложение `NSUserActivity` и его заполнения сведениями для воссоздания состояния более поздней версии. Приложение также включила некоторые метаданные, такие как имя и адрес расположения. С помощью этого действия создан приложение позволяет iOS знать, что он представляет текущее состояние пользователя.

Затем приложение решает, если действие выполняется объявление беспроводной сети для обработки, сохранен как временное значение для предложения расположение или добавляются в индекс Spotlight на устройстве для отображения в результатах поиска.

Дополнительные сведения об Эстафетной и Spotlight поиска, см. в разделе наших [Общие сведения об Эстафетной](~/ios/platform/handoff.md) и [iOS 9 новых API-интерфейсы поиска](~/ios/platform/search/index.md) руководства.

### <a name="creating-an-activity"></a>Создание действия

Прежде чем создавать действия, идентификатор типа действия будет необходимо создать для его идентификации. Идентификатор типа действия — это короткие строки, добавленной `NSUserActivityTypes` массив приложения `Info.plist` файл, используемый для идентификации данного типа действий пользователя. Будет существовать одна запись в массиве для каждого действия, которое приложение поддерживает и предоставляет для поиска приложения. См. в разделе наших [Создание типа идентификаторов справочные материалы по действиям](~/ios/platform/search/nsuseractivity.md) для получения дополнительных сведений.

Рассмотрим пример действия:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Новое действие создается с помощью идентификатора типа действия. Далее некоторые метаданные, определяющие действия создается, поэтому это состояние может быть восстановлена на более позднюю дату. Затем действие заданному понятное название и подключенные к сведений о пользователе. Наконец некоторые возможности включены и действия отправляется в систему.

Приведенный выше код можно улучшить, чтобы включить метаданные, предоставляющий контекст для действия, если внести следующие изменения:

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Если разработчик веб-сайт, который может отображать те же сведения, что и приложение, приложение может включать URL-адрес, и содержимое может отображаться на других устройствах, у которых нет на приложение, установленное (через перемещение вручную):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Восстановление действия

Реагировать на пользователя, коснувшись результат поиска (`NSUserActivity`) для приложения, изменение **AppDelegate.cs** файл и переопределить `ContinueUserActivity` метод. Пример:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Разработчику необходимо обеспечить, это тот же идентификатор типа действия (`com.xamarin.platform`) как действие, созданное ранее. Приложение использует информацию, хранящуюся в `NSUserActivity` для восстановления состояния до там, где пользователь остановились.

### <a name="benefits-of-creating-an-activity"></a>Преимущества создания действия

С минимальным объемом кода, приведенного выше приложение теперь могут использовать три новых возможностей iOS 10:

- **Handoff**
- **Поиск Spotlight**
- **Контекстные Siri напоминания**

Следующий раздел будет взгляните на пути к предоставлению две новые возможности iOS 10:

- **Расположение предложения**
- **Контекстные Siri запросы**

### <a name="location-based-suggestions"></a>Предложения на основе расположения 

Рассмотрим в качестве примера приложения ресторан поиска выше. Если он реализован `NSUserActivity` и правильно заполнены все метаданные и атрибуты, пользователь сможет выполните следующие действия:

1. Найти Ресторан в приложении, они должны соответствовать своему другу в.
2. Как пользователь перемещается из приложения с помощью переключателя приложений многозадачности, система автоматически отобразит предложение (в нижней части экрана) для получения указания ресторан, с помощью их любимых навигации приложения.
3. Если пользователь переключается в приложения для обмена сообщениями и начинает ввод *«Давайте встретимся в»*, QuickType клавиатуры будет предлагать вставки в адресе ресторана.
4. Если пользователь переключается на приложение карты, адрес ресторана автоматически предлагается в качестве назначения.
5. Это работает даже для сторонних приложений (которые поддерживают `NSUserActivity`), поэтому пользователь может переключать приложение управления доступом поездке и адрес ресторана автоматически предлагается в качестве назначения существует также.
6. Он также предоставляет контекст для Siri, поэтому пользователь может вызвать Siri в приложении ресторан и попросите *«Get directions...»* и Siri предоставит указания ресторан, при просмотре.

Все функциональные возможности, выше общих имеет одну вещь, все они указывают, что предложение изначально источник. Приведенном выше примере это приложение проверки вымышленной ресторана.

Чтобы включить эту функцию для приложения с помощью нескольких небольшие изменения и дополнения существующих платформ была улучшена iOS 10:

- `NSUserActivity` содержит дополнительные поля для записи сведения о расположении, которые можно просматривать внутри приложения.
- Несколько дополнения были внесены в MapKit и CoreSpotlight для записи расположения.
- Расположение функциональные области добавлено Siri, карты, клавиатура, многозадачность и других приложений в системе.

Чтобы реализовать рекомендации на основе расположения, начните с тот же код действия, представленные выше:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Если приложение использует MapKit, это простым добавлением текущего объекта map `MKMapItem` к действию:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Если приложение не использует MapKit, можно адаптировать приложение поиска и укажите следующие новые атрибуты для расположения:

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Рассмотрим приведенный выше код подробно. Во-первых в каждом экземпляре требуется имя расположения:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Затем на основе текстового описания расположение необходим для экземпляров на основе текста (например, QuickType клавиатуры):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

Широту и долготу необязательны, но убедитесь, что пользователь направляется к определенному расположению в приложение хотят отправить их, поэтому должна быть включена:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Установив номера телефонов, приложения могут получить доступ к Siri, чтобы пользователь мог использовать Siri из приложения, например, *«Call сюда»*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Наконец приложение может указывать, подходит ли экземпляр для навигации и телефонные звонки.

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Реализация взаимодействия контакта

Новое в iOS 10, связи приложений глубоко интегрированы в приложение контакты из карточки контакта. Для приложений, которые реализованы взаимодействия контакт пользователь может добавить данные заданного приложения конкретным сотрудникам из своих контактов. И если, например, они нажмите кнопку "быстрое действие" в верхней части карты, чтобы отправить сообщение, вложенное приложение будет иметь статус возможность отправки сообщений.

Если выбран сторонней приложение, его можно запоминаются и представлены в виде стандартного способа для сообщения данное лицо в следующий раз, пользователю необходимо связаться с ними.

Взаимодействия контакта, реализуются в приложение с помощью `NSUserActivity` и новая платформа намерения впервые появился в iOS 10. Дополнительные сведения о работе с намерениями, см. в разделе наших [основные сведения о понятиях SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) и [реализация SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) руководства.

#### <a name="donating-interactions"></a>Взаимодействие благотворительности

Рассмотрим, как приложение можете внести пожертвование взаимодействия:

[![](proactive-suggestions-images/activity04.png "Общие сведения о взаимодействии благотворительности")](proactive-suggestions-images/activity04.png#lightbox)

Приложение создает `INInteraction` , содержащий **намерение** (`INIntent`), **участников** и **метаданных**. **Намерение** представляет действие пользователя, такие как видео вызов или отправка текстового сообщения. **Участников** включают людей, получения сообщений. **Метаданных** определяет дополнительную информацию, например успешной отправки сообщения и т. д.

Разработчик никогда не непосредственно создает экземпляр класса `INIntent` или `INIntentResponse`, они будут использовать один из указанных дочерних классов (на основе задачи приложения выполнение от имени пользователя), наследуемым от этих родительских классов. Например `INSendMessageIntent` и `INSendMessageIntentResponse` для отправки текстового сообщения. 

После взаимодействие будет заполнена, вызовите `DonateInteraction` метод для информирования системы, доступный для использования взаимодействия.

Когда пользователь взаимодействует с приложением, из карточки контакта, взаимодействия получает состав `NSUserActivity`, который затем используется для запуска приложения:

[![](proactive-suggestions-images/activity05.png "Возвращает в состав NSUserActivity, который используется для запуска приложения взаимодействия")](proactive-suggestions-images/activity05.png#lightbox)

Взгляните на следующий пример способа отправки сообщения:

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

Просмотрев этот код, подробно, он создает и заполняет экземпляр класса `NSUserActivity` (как показано в [Создание действия](#Creating-an-Activity) предыдущем разделе). Затем он создает экземпляр `INSendMessageIntent` (который наследуется от `INIntent`) и заполняет его аспекты текст отправляемого сообщения:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

`INSendMessageIntentResponse` Создается и передается `NSUserActivity` созданный выше:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

`INInteraction` Построена с целью отправки сообщений (`INSendMessageIntent`) и ответа (`INSendMessageIntentResponse`) только что создали:

```csharp
var interaction = new INInteraction (intent, response);
```

Наконец система является уведомление взаимодействия:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Обработчик завершения передается в которых приложение может отвечать на пожертвование успешно или сбоем.

### <a name="activities-best-practices"></a>Рекомендации по обеспечению действий

Apple предлагает следующие рекомендации при работе с действиями:

- Используйте `NeedsSave` обновлений отложенной полезных данных.
- Убедитесь, что сохранить строгую ссылку на текущее действие.
- Передавать только небольшой полезных данных, которые включают достаточно информации для восстановления состояния.
- Убедитесь, что идентификаторы типа действия уникальное и описательное с помощью нотации обратного запроса DNS для указания их. 

## <a name="schemaorg"></a>Schema.org

Как показано выше, `NSUserActivity` помогает понять, какие сведения на экране его пользователь в настоящее время работает в системе. Schema.org добавляет аналогичные возможности на веб-страницы.

Schema.org можно предоставить те же типы взаимодействия на основе расположения на веб-сайт. Apple разработаны новые предложения расположение работать точно так же, при просмотре в Safari, как и в собственном приложении.

Некоторые Schema.org фон:

- Он предоставляет стандартом open web разметки словаря.
- Это работает, включая структурированных метаданных на веб-страницах.
- Существует более чем 500 схемы представления доступны различные концепции.
- Путем реализации его веб-сайта, разработчик может получить некоторые из преимуществ использования `NSUserActivity` в собственном приложении.

Схемы, упорядочены в дереве как структура, где конкретных типов, таких как *ресторан*, наследуют от более универсальные типы, такие как *местных организациях*. Дополнительные сведения см. в разделе [Schema.org](http://schema.org).

Например, если веб-страницы содержится следующие данные:

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

Если пользователь посещения страницы в Safari, а затем переключается на другое приложение, сведения о расположении из страницы фиксируются и предлагаются как предложение расположения в других частях системы (как показано в действиях выше).

Safari извлечет ничего на веб-странице, который соответствует любой из следующих свойств схемы:

- **PostalAddress**
- **GeoCoordinates**
- Свойство телефона.

Дополнительные сведения см. в разделе наших [поиск с помощью веб-разметка](~/ios/platform/search/web-markup.md) руководства.

## <a name="consuming-location-suggestions"></a>Использование предложения расположение

В следующем разделе рассматривается использование предложения расположение, получен из других частей системы (например, приложение карт) или другие сторонние приложения.

Существует два основных способа, приложение может использовать расположение предложения:

- С помощью клавиатуры QuickType.
- Напрямую, использует сведения о расположении в приложениях для маршрутизации.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Расположение предложения и QuickType клавиатуры

Если приложение имеет дело с адресами в основе текстовых форматов, приложение может воспользоваться преимуществами расположение предложений через пользовательский Интерфейс QuickType. iOS 10 расширяет пользовательский Интерфейс QuickType со следующими функциями:

- Приложение можно добавить указания о семантической конечной цели для текстовых полей в пользовательском Интерфейсе.
- Приложение может получить упреждающие предложения в приложении.
- Приложения могут использовать преимущества расширенной автозамены.

Новый `TextContentType` свойство, поле текстовых элементов управления в iOS 10 позволяет разработчику определить семантической цель для значения, которое пользователь будет вводить в этом поле. Пример:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Бы указать системе, что приложение ожидает от пользователя ввести полный почтовый адрес в заданного поля. Это позволит клавиатуры QuickType автоматическое предоставление местоположение предложения на клавиатуре, когда пользователь вводит значение в этом поле.

Ниже приведены некоторые из наиболее распространенных типов, доступных для разработчиков в `UITextContentType` статический класс:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Маршрутизации приложений и расположений предложения

В этом разделе будет взгляните на использование предложения расположение непосредственно из приложения маршрутизации. Для маршрутизации приложения, чтобы добавить эту функцию, разработчик будет использовать существующий `MKDirectionsRequest` framework следующим образом:

- Для повышения уровня приложений в многозадачных.
- Чтобы зарегистрировать приложение как приложение маршрутизации.
- Для обработки, запустив приложение с MapKit `MKDirectionsRequest` объекта.
- Чтобы предоставить возможность научиться предложить приложение, пользователю в нужное время iOS зависит от взаимодействия пользователя.

Когда приложение запускается с параметром MapKit `MKDirectionsRequest` оно должно автоматически начинает выдавать соответствующие инструкции пользователя для запрашиваемого местоположения, или представить пользовательский Интерфейс, который упрощает процесс для пользователя, чтобы быстро получить инструкции. Пример:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

Взгляните на этот код подробно. Проверяется, находится ли она допустимое назначение для запроса:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Если он является, то он создает `MKDirectionsRequest` из URL-адрес:

```csharp
var request = new MKDirectionsRequest(url);
```

Новое в iOS 10, приложение может быть отправлено адрес, который не поддерживает географических координат, к тому, что разработчику необходимо закодировать адрес:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Варианты приложений мультимедиа

Если приложение обрабатывает любой тип носителя, например приложение подкастов или потоковой передаче мультимедийного содержимого, такие как аудио и видео с iOS 10, он может использовать преимущества предложения мультимедиа в системе.

Для приложений, которые обрабатывают мультимедиа iOS поддерживают следующие функции:

- iOS повышает уровень приложения, которые пользователь, вероятно, для использования на основе их предыдущего поведения.
- Варианты, относящиеся к приложению, отображаются в центре внимания и представление "сегодня".
- Если приложение соответствует одной из следующих триггеров, он может повысить предложение экрана блокировки:
    - После подключения наушники или устройства Bluetooth устанавливается соединение.
    - После получения в автомобиле.
    - После поступления дома или работы. 

Включая простого вызова API в iOS 10, разработчик может создать более приятное ощущение экрана блокировки для пользователей приложения мультимедиа. С помощью `MPPlayableContentManager` класса для управления воспроизведением мультимедиа, полный носитель элементов управления (например, представленный приложение Music) появляется на экране блокировки для приложения.


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>Сводка

В этой статье в рамках упреждающие предложения и показал, как разработчик может использовать их для увеличения объема трафика в приложение Xamarin.iOS. Он рассказал шаг, чтобы реализовать упреждающие предложения и представлены рекомендации по использованию.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Руководство по программированию в SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
