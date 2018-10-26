---
title: Улучшенные возможности поиска приложения в Xamarin.iOS
description: В этой статье рассматриваются усовершенствования Apple сделала для поиска приложения в iOS 10 и способы их реализации в Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110587"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Улучшенные возможности поиска приложения в Xamarin.iOS

_В этой статье рассматриваются усовершенствования Apple сделала для поиска приложения в iOS 10 и способы их реализации в Xamarin.iOS._

В iOS 10 Apple сделала ряд дополнительных возможностей поиска приложения, такие как связывание глубокого приобщиться, поиск в приложении, продолжение поиска и визуализации результатов проверки. В этой статье будут рассмотрены реализации этих функций в приложении Xamarin.iOS.

## <a name="about-app-search-enhancements"></a>О улучшенные возможности поиска приложения

Полезные сведения в iOS 10 содержит ряд усовершенствований для поиска приложения:

- **Популярность приобщиться прямой ссылкой (с разностной конфиденциальности)** -предоставляет способ распространения содержимого прямая ссылка на приложение в результатах поиска.
- **Поиск в приложении** -использовать новый `CSSearchQuery` класс обеспечивает возможность поиска Spotlight в приложении аналогичную как работают приложения почта, сообщения и заметки.
- **Поиск продолжения** — пользователь может начать поиск в центре внимания или Safari, а затем откройте приложение и по-прежнему поиска.
- **Визуализация результатов проверки** -Apple [средство проверки API поиска приложения](https://search.developer.apple.com/appsearch-validation-tool) теперь отображает визуальное представление разметки веб сайта и связывание глубокого preforming тестов.
- **Сообщения, общий доступ к приложениям образ** -позволяет популярных изображений в приложении для отображаться в результатах поиска Spotlight совместного использования в сообщениях (через расширение приложения сообщения).

Следующие разделы будут рассмотрены в следующих разделах более подробно.

## <a name="crowdsourced-deep-link-popularity"></a>Популярность приобщиться прямой ссылкой

iOS 10 предоставляет механизм для подсчета частоты, что следует часто используемых глубокого ссылок в приложение пользователя и использует эту информацию, чтобы повысить рейтинг приложения содержимого в результатах поиска, обеспечивая защиту удостоверение пользователя с помощью  *Разностные конфиденциальности*.

Для приложений, использующих `NSUserActivity` объектов обеспечивают прямой ссылкой URL-адреса и `EligibleForPublicIndexing` свойство значение `true`, iOS 10 отправляет подмножество *разностные хэши конфиденциальности* на серверах компании Apple. Эта информация затем используется для повышения уровня популярных содержимое в приложении, в результатах поиска.

Дополнительные сведения о реализации сложного связывания в приложении Xamarin.iOS см. в разделе наших [поиска с NSUserActivity](~/ios/platform/search/nsuseractivity.md) документации.

## <a name="in-app-searching"></a>Поиск в приложении

Путем реализации нового [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) класс, приложение может предоставлять поиска объекта Spotlight и сопоставления правил технологии можно искать содержимое внутри самого без пользователю не придется выйти из приложения (аналогично тому, как почта, сообщения и заметки о приложении работе).

Как правило, приложения, поддерживающие `CSSearchQuery` будет не нужно хранить свои собственные, отдельные поиска индекса. 

## <a name="search-continuation"></a>Продолжение поиска

В iOS 9 Apple представила API поиска (такие как полезные сведения, `NSUserActivity` и веб-разметка) для предоставления глубокого оформлением содержимого в приложение, чтобы разрешить пользователям выполнять поиск этого содержимого, с помощью интерфейсов поиска Spotlight и Safari. См. в разделе наших [новые API-интерфейсы поиска](~/ios/platform/search/index.md) документации для получения дополнительных сведений.

В iOS 10 Apple развивает эту функцию, поскольку пользователь может начать поиск в центре внимания или Safari, а затем продолжить поиск в том случае, когда он открывает приложение. 

Чтобы реализовать эту функцию, измените приложения `Info.plist` добавьте `CoreSpotlightContinuation` ключа типа **логическое** и присвойте ему значение `YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Редактирование CoreSpotlightContinuation в файле Info.plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Редактирование CoreSpotlightContinuation в файле Info.plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Реагировать на пользователя, продолжить результатов поиска (`NSUserActivity`), изменить `AppDelegate.cs` файл и переопределить `ContinueUserActivity` метод. Пример:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Этот код ищет тип действия для продолжения запроса (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), затем считывает текущий запрос пользователя из `NSUserActivity` словарь сведений о класса пользователя (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). На этой странице приложению необходимо предпринимать действия для продолжения поиска пользователя.

Дополнительные сведения о работе с поисковыми запросами в приложении Xamarin.iOS см. в разделе наших [поиска с полезные сведения](~/ios/platform/search/corespotlight.md) документации.

## <a name="visualization-of-validation-results"></a>Визуализация результатов проверки

Apple [средство проверки API поиска приложения](https://search.developer.apple.com/appsearch-validation-tool) теперь отображается визуальное представление разметки и связывание глубокого веб сайта (включая разметку, такие как определенные в [Schema.org](http://schema.org/)) при preforming тестов.

С помощью средства проверки, разработчик может видеть сведения, что программа-обходчик Applebot содержит индексированные для сайта, такие как название, описание, URL-адрес и любых других поддерживаемых элементов.

Дополнительные сведения о работе с веб-разметка, см. в разделе наших [поиск с помощью веб-разметка](~/ios/platform/search/web-markup.md) документации.

## <a name="message-app-image-sharing"></a>Изображение приложения сообщение совместное использование

Если расширение приложения сообщение предоставляет образов для совместного использования в сообщениях, расширение можно настроить разрешение для выполнения поиска Spotlight для популярных образов из в сообщения, не нужно выйти из приложения.

Чтобы включить эту функцию, выполните следующие действия.

1. Создайте расширение сообщения приложения.
2. Добавление `com.apple.developer.associated-domains` для прав приложения и включают список веб-доменов, на которых размещены образы, общий доступ к расширение приложения для сообщения. Для каждого домена, укажите `spotlight-image-search` службы.
3. Добавление `apple-app-site-association` файл на веб-сайт, на котором размещается изображения. Этот файл содержит словарь для `spotlight-image-search` службы и включает в себя идентификатор приложения, — это префикс идентификатор команды или идентификатор приложения, следуют идентификатор пакета. Файл может содержать до 500 пути и шаблоны, которые будут включены в поиск популярных изображений и индексируются с помощью Spotlight. Дополнительные сведения см. в разделе Apple [создания и загрузки файла сопоставления](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) документации.
4. Разрешить Applebot для обхода веб-сайтов. См. в разделе Apple [Applebot о](https://support.apple.com/HT204683) документации.

См. в разделе наших [интеграция приложения сообщений](~/ios/platform/message-app-integration/index.md) документации для получения дополнительных сведений.

## <a name="summary"></a>Сводка

Статья описывает усовершенствования Apple сделала для поиска приложения в iOS 10 и способы их реализации в Xamarin.iOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
