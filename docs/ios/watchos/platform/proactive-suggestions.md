---
title: watchOS упреждающие предложения в Xamarin
description: В этой статье показано, как использовать упреждающие предложения в приложении watchOS 3 для стимулирования вовлеченности, позволив системе, чтобы заранее предоставить полезные сведения автоматически пользователю.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 979b103db478e3888d3a3c20df6afbd91d0c37d8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116905"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>watchOS упреждающие предложения в Xamarin

_В этой статье показано, как использовать упреждающие предложения в приложении watchOS 3 для стимулирования вовлеченности, позволив системе, чтобы заранее предоставить полезные сведения автоматически пользователю._


Новый для watchOS 3, способов присутствует новости упреждающие предложения для пользователей, чтобы связаться с приложения Xamarin.iOS, заранее присутствует полезные сведения автоматически пользователю в нужное время.


## <a name="about-proactive-suggestions"></a>О упреждающие предложения

В watchOS 3, `NSUserActivity` включает в себя `MapItem` свойство, которое позволяет приложению предоставлять сведения о расположении, который может использоваться в других контекстах. Например, если приложение, отображенное гостинице проверяет и предоставляет `MapItem` расположение, если пользователь переключается на приложение карты, расположение только что просматривали отеля бы быть доступны.

Приложение предоставляет этой функции в систему с помощью набора технологий, таких как `NSUserActivity`, MapKit, Media Player и UIKit. Кроме того предоставляя поддержку упреждающего предложений для приложения, он получает более глубокую интеграцию Siri бесплатно.

## <a name="location-based-suggestions"></a>Предложения на основе расположения

В watchOS 3, `NSUserActivity` класс включает `MapItem` свойство, которое позволяет разработчику указывать информацию о расположении, который может использоваться в других контекстах. Например, если приложение отображает отзывы о ресторанах, разработчик может настроить `MapItem` свойства расположение ресторана, который используется для просмотра в приложении. Если пользователь переключается на приложение карты, расположение ресторана доступен автоматически.

Если приложение поддерживает поиска приложения, его можно использовать новые компоненты адрес `CSSearchableItemAttributesSet` класс для указания расположения, которые пользователь может захотеть посетить. Установив `MapItem` свойство, другие свойства являются заполнены по умолчанию.

Дополнение к параметру `Latitude` и `Longitude` свойствах компонента адреса, рекомендуется, чтобы приложение указали `NamedLocation` и `PhoneNumbers` свойства, поэтому Siri могут инициировать вызов к расположению.

## <a name="contextual-siri-reminders"></a>Контекстные Siri напоминания

Позволяет пользователю использовать Siri, чтобы быстро создать напоминание для просмотра содержимого, они в настоящее время просматривать в приложении на более позднюю дату. К примеру, если они просматривать ресторане в приложении, они могут вызвать Siri и сказать *«Напомнить об этом при получении Главная».* Siri вызовет оповещение со ссылкой на проверку в приложении.

## <a name="implementing-proactive-suggestions"></a>Реализация упреждающие предложения

Добавление упреждающего предложение поддержки в приложение Xamarin.iOS обычно так же просто, как реализовать несколько интерфейсов API или расширение несколько интерфейсов API, которые уже должна реализовывать приложения.

Упреждающие предложения работают с приложениями тремя основными способами:

- **`NSUserActivity`** -Позволяет узнать, какие сведения на экране его пользователь в настоящее время работает в системе.
- **Варианты расположения** — Если приложение предоставляет или потребляет сведения на основе расположения, эти расширения API предложение новых способов совместно использовать эту информацию в приложения.

И поддерживается в приложении путем реализации следующее:

- **Контекстные напоминания Siri** — в iOS 10, `NSUserActivity` была увеличена, чтобы разрешить Siri, чтобы быстро создать напоминание для просмотра содержимого в настоящее время они просматриваются в приложении на более позднюю дату.
- **Варианты расположения** -iOS 10 улучшает `NSUserActivity` для записи расположения, просмотреть внутри приложения и повысить их во многих местах во всей системе.
- **Контекстные запросы Siri**  -  `NSUserActivity` предоставляет контекст для сведения, предоставляемые внутри приложения Siri, чтобы пользователь смог перейти направлениях или вызов быть место вызова Siri в приложении.

Все эти функции имеют одну вещь Общие, то все они используют `NSUserActivity` в той или иной для функционирования форме. 

## <a name="nsuseractivity"></a>NSUserActivity

Как уже говорилось, `NSUserActivity` помогает понять, какие сведения на экране его пользователь в настоящее время работает в системе. `NSUserActivity` состояние недоступно: кэширует механизм для записи действий пользователей, как они навигации по приложению. Например просмотрев ресторана приложения:

[![](proactive-suggestions-images/activity02.png "Приложение ресторана")](proactive-suggestions-images/activity02.png#lightbox)

С помощью следующие взаимодействия:

1. Когда вы работаете с приложением, `NSUserActivity` создается для воссоздания состояния приложения в более поздней версии.
2. Если пользователь выполняет поиск ресторан, идет та же последовательность создания действий.
3. И опять же, в том случае, когда пользователь просматривает результат. В последнем случае при просмотре расположение и в iOS 10, система находится в большей степени поддерживал некоторые основные понятия (например, расположение или связи взаимодействия).

Взгляните ближе на последнем экране:

[![](proactive-suggestions-images/activity03.png "Полезные данные NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

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

Убедитесь, это тот же идентификатор типа действия (`com.xamarin.platform`) как действие, созданное ранее. Приложение использует информацию, хранящуюся в `NSUserActivity` для восстановления состояния до там, где пользователь остановились.

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
4. Если пользователь переключается на приложение карты, адрес ресторана автоматически предлагается в качестве назначения.
5. Это работает даже для сторонних приложений (которые поддерживают `NSUserActivity`), поэтому пользователь может переключать приложение управления доступом поездке и адрес ресторана автоматически предлагается в качестве назначения существует также.
6. Он также предоставляет контекст для Siri, поэтому пользователь может вызвать Siri в приложении ресторан и попросите *«Get directions...»* и Siri предоставит указания ресторан, при просмотре.

Все функциональные возможности, выше общих имеет одну вещь, все они указывают, что предложение изначально источник. Приведенном выше примере это приложение проверки вымышленной ресторана.

Чтобы включить эту функцию для приложения с помощью нескольких небольшие изменения и дополнения существующих платформ была улучшена watchOS 3:

- `NSUserActivity` содержит дополнительные поля для записи сведения о расположении, которые можно просматривать внутри приложения.
- Несколько дополнения были внесены в MapKit и CoreSpotlight для записи расположения.
- Расположение функциональные области добавлено Siri, карты, многозадачность и других приложений в системе.

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

Затем текст, на основе описания в необходимый для текста на основе экземпляров (например, клавиатура QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

Широту и долготу необязательны, но убедитесь, что пользователь направляется к определенному расположению в приложение хочет отправить им:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Установив номера телефонов, приложения могут получить доступ к Siri, чтобы пользователь мог использовать Siri из приложения, например, * «Call сюда»:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Наконец приложение может указывать, подходит ли экземпляр для навигации и телефонные звонки.

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Рекомендации по обеспечению действий

Apple предлагает следующие рекомендации при работе с действиями:

- Используйте `NeedsSave` обновлений отложенной полезных данных.
- Убедитесь, что сохранить строгую ссылку на текущее действие.
- Передавать только небольшой полезных данных, которые включают достаточно информации для восстановления состояния.
- Убедитесь, что идентификаторы типа действия уникальное и описательное с помощью нотации обратного запроса DNS для указания их. 

## <a name="consuming-location-suggestions"></a>Использование предложения расположение

В следующем разделе рассматривается использование предложения расположение, получен из других частей системы (например, приложение карт) или другие сторонние приложения.

## <a name="routing-apps-and-locations-suggestions"></a>Маршрутизации приложений и расположений предложения

В этом разделе будет взгляните на использование предложения расположение непосредственно из приложения маршрутизации. Для маршрутизации приложения, чтобы добавить эту функцию, разработчик будет использовать существующий `MKDirectionsRequest` framework следующим образом:

- Для повышения уровня приложений в многозадачных.
- Чтобы зарегистрировать приложение как приложение маршрутизации.
- Для обработки, запустив приложение с MapKit `MKDirectionsRequest` объекта.
- Разрешить watchOS чтобы узнать, как предложить приложение, в зависимости от взаимодействия пользователя.

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

Новое в watchOS 3, приложение может быть отправлено адрес, который не поддерживает географических координат, к тому, что разработчику необходимо закодировать адрес:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Сводка

В этой статье в рамках упреждающие предложения и показал, как разработчик может использовать их для увеличения объема трафика в приложение Xamarin.iOS для watchOS. Он рассказал шаг, чтобы реализовать упреждающие предложения и представлены рекомендации по использованию.


## <a name="related-links"></a>Связанные ссылки

- [Примеры watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Руководство по программированию в SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
