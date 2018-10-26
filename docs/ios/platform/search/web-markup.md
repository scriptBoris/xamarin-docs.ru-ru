---
title: Поиск с помощью веб-разметка в Xamarin.iOS
description: В этом документе описывается создание веб-поиска результатов, которые связывают обратно в приложение Xamarin.iOS. В этом примере рассматривается включение веб-содержимого, индексирование, предоставляя веб-сайт вашего приложения сведения об инструментарии, используя баннеров смарт-приложении, универсальных ссылок и многое другое.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 243408ce6e2236b75ea35dfd17633a9a24493c1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102806"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Поиск с помощью веб-разметка в Xamarin.iOS

Для приложений, которые предоставляют доступ к своему содержимому через веб-сайта (не только в приложении), веб-содержимого могут быть помечены специальные ссылки, которые будут просматриваться Apple и обеспечивают создание глубинных ссылок для приложений на устройствах пользователей iOS 9.

Если ваше приложение iOS уже поддерживает мобильного Создание глубинных ссылок и веб-сайта предоставляются прямые ссылки на содержимое в приложения, Apple _Applebot_ программа-обходчик будет индексирования этого содержимого и автоматически добавить его в их индексов облака:

[![](web-markup-images/webmarkup01.png "Обзор индекса в облаке")](web-markup-images/webmarkup01.png#lightbox)

Apple будет предоставлять эти результаты в поиске Spotlight и Safari в результатах поиска.
Если пользователь нажимает на один из этих результатов (и они установили ваше приложение), а затем он будет перенаправлен к содержимому в приложении:

[![](web-markup-images/webmarkup02.png "Связывание с веб-сайта в результатах поиска для глубокого")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Включение индексирования содержимого для Web

Существует четыре действия, необходимые в качестве содержимого приложения для поиска с помощью веб-разметка:

1. Убедитесь, что Apple может обнаружить и индексировать веб-сайт вашего приложения, определив его как **поддержки** или **маркетинга** веб-сайта в iTunes Connect.
2. Убедитесь, что веб-сайт вашего приложения содержит необходимые разметки для реализации мобильных Создание глубинных ссылок. Ознакомьтесь с разделами ниже для получения дополнительных сведений.
3. Включите прямой ссылки, обработка в приложении iOS.
4. Добавьте разметку для структурированных данных, отображенные с веб-сайт приложения для предоставления насыщенные и привлекательные результат конечному пользователю. Хотя этот шаг не является обязательным, рекомендуется компанией Apple.

Следующие разделы будут рассмотрены эти шаги подробно.

## <a name="make-your-apps-website-discoverable"></a>Сделать доступной для обнаружения веб-сайт вашего приложения

Чтобы найти приложения веб-сайте Apple проще всего использовать его как **поддержки** или **маркетинга** веб-сайта, при отправке приложения в Apple через iTunes Connect.

## <a name="using-smart-app-banners"></a>С помощью баннеров смарт-приложении

Предоставляют объявления на смарт-приложения на веб-сайте, чтобы представить очистить ссылку в веб-приложения. Если приложение еще не установлен, Safari автоматически предложит пользователю устанавливать приложения. В противном случае можно коснуться использование **представление** ссылка для запуска приложения на веб-сайте. Например для создания интеллектуальных приложений баннер, можно использовать следующий код:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Дополнительные сведения см. в разделе Apple [повышение уровня приложений с помощью смарт-баннеров в приложении](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) документации.

## <a name="using-universal-links"></a>С помощью универсальной ссылки

Новое для iOS 9, универсальные ссылки рекомендуется использовать смарт-баннеров в приложении или существующие пользовательские схемы URL-адрес, указав следующее:

- **Уникальный** — же URL-адрес не может запросить, более одного веб-сайта.
- **Защита** — подписанный сертификат является обязательным для связанной веб-сайта, которое гарантирует, что веб-сайт принадлежит вами и надлежащим образом для приложения.
- **Гибкие** — конечного пользователя можно управлять, запускает ли URL-адрес веб-сайта или приложения.
- **Универсальные** — же URL-адрес может использоваться для определения содержимого веб-сайта и приложения.

## <a name="using-twitter-cards"></a>С помощью карт Twitter

Можно предоставить прямые ссылки на содержимое своего приложения, с помощью карты Twitter. Пример:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Дополнительные сведения см. в разделе Twitter [Twitter протокола карты](http://dev.twitter.com/cards/mobile) документации.

## <a name="using-facebook-app-links"></a>С помощью связей приложения Facebook

Можно предоставить прямые ссылки на содержимое своего приложения, с помощью ссылки приложения Facebook. Пример:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Дополнительные сведения см. в разделе Facebook [ссылки приложение](http://applinks.org) документации.

## <a name="opening-deep-links"></a>Открытие прямые ссылки

Необходимо добавить поддержку для открытия и отображения прямые ссылки в приложении Xamarin.iOS. Изменить **AppDelegate.cs** файл и переопределить `OpenURL` метод для обработки пользовательского формата URL-адрес. Пример:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

В приведенном выше коде мы ищем URL-адрес, содержащий `/appname` и передав значение `query` (`123` в этом примере) к контроллеру пользовательское представление в приложении, чтобы отобразить запрошенного содержимого для пользователя.

## <a name="providing-rich-results-with-structured-data"></a>Предоставляя широкие возможности результаты со структурированными данными

Включая разметку структурированных данных можно предоставить результаты поиска с широкими возможностями для конечного пользователя, выходящих за пределы просто название и описание. Включать изображения, данные конкретного приложения (например, оценок) и действия к результатам с помощью разметки структурированных данных.

Расширенные результаты более приятной и помогают повысить свой рейтинг в облаке на основе индекса поиска обманом дополнительные пользователям взаимодействовать с ними.

Один из вариантов для предоставления разметки структурированных данных, — с помощью Open Graph. Пример:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Дополнительные сведения см. в разделе [Open Graph](http://ogp.me) веб-сайта.

Другой общий формат для структурированных данных разметки — формат Микроданные schema.org элемента. Пример:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

Те же данные можно представить в формате JSON-LD schema.org элемента:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

Ниже показан пример метаданных с веб-сайта, предоставляя результаты поиска с широкими возможностями для конечного пользователя:

[![](web-markup-images/deeplink01.png "Полнофункциональный поиск результатов с помощью разметки структурированных данных")](web-markup-images/deeplink01.png#lightbox)

Apple в настоящее время поддерживает следующие типы схемы из schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Предложения
 - Организации
 - PriceRange
 - Рецепт
 - SearchAction

Дополнительные сведения об этих типах схемы см. в разделе [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Предоставление действий со структурированными данными

Конкретные типы структурированных данных позволит результат поиска, чтобы иметь возможность отреагировать конечным пользователем. В настоящее время поддерживаются следующие действия:

 - Набора номера телефона.
 - Получение направление сопоставления по указанному адресу.
 - Воспроизведение аудио-или видео.

Например определение действий для набора номера телефона может выглядеть следующим образом:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Когда этот результат поиска предоставляется конечному пользователю, в результате будет отображаться значок в виде небольшого телефона. Если пользователь касается значка, будет вызываться числу, заданному.

Следующий код HTML будет добавить действие для воспроизведения звукового файла из результата поиска:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Наконец следующий код HTML добавить действие, которое получает направлениях из результата поиска:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Дополнительные сведения см. в разделе Apple [сайта разработчика приложения поиска](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Руководство по программированию приложения поиска](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
