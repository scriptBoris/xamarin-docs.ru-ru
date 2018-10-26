---
title: Веб-представления в Xamarin.iOS
description: В этом документе описываются различные способы приложения Xamarin.iOS можно отобразить веб-содержимого. В нем описывается UIWebView, WKWebView, SFSafariViewController, Safari и безопасность транспорта приложения.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9adf514e4fc510617e3f4d801569935ee4a03f25
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104366"
---
# <a name="web-views-in-xamarinios"></a>Веб-представления в Xamarin.iOS

В течение времени существования iOS Apple выпустила ряд способов для разработчиков приложений для включения web представление функциональность в своих приложениях. Большинство пользователей используют встроенные веб-браузера Safari на устройстве iOS и ожидать, что функциональные возможности веб представлением из других приложений согласуется с этого интерфейса. Они ожидают те же жесты для работы, производительность совпадать на одном уровне и функциональные возможности.

В этой статье мы рассмотрим каждый из трех веб-представлений, предоставляемых компанией Apple: `UIWebView`, `WKWebview`, и `SFSafariViewController`, их сходства и различия и как их использовать. 

iOS 11 появился новый изменений к обоим `WKWebView` и `SFSafariViewController`. Дополнительные сведения см. в разделе [веб-изменения в руководство по iOS 11](~/ios/platform/introduction-to-ios11/web.md) руководства.

## <a name="uiwebview"></a>UIWebView

`UIWebView` — устаревший способ предоставления веб-содержимого в приложении компании Apple. Она была выпущена в iOS 2.0 и нерекомендуемым начиная с версии 8.0.

Если вы планируете поддерживать iOS версии более ранней, чем 8.0, необходимо использовать `UIWebView`. Из-за того, что `UIWebView` меньше оптимизирован для производительности, нежели другие альтернативы, рекомендуется должны проверять версию iOS пользователя. Если он 8.0 или более поздней версии, используя один из вариантов объясняются ниже создаст удобства работы пользователя.
 
Чтобы добавить UIWebView в приложение Xamarin.iOS, используйте следующий код:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

В результате получается следующий веб-представление:

[![](uiwebview-images/webview.png "Последствия ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Дополнительные сведения об использовании `UIWebView`, см. следующие инструкции:


- [Загрузить веб-страницы](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Загрузка локального содержимого](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Загрузить веб-документы](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` впервые появился в iOS 8 позволяет разработчикам приложений для реализации веб-страниц интерфейс аналогичен мобильных Safari. Причиной этого является, в частности, к тому факту, `WKWebView` использует обработчик Nitro Javascript, тот же механизм, используемый мобильных Safari. `WKWebView` следует всегда использовать UIWebView были недоступны из-за отказа [повышенную производительность](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), встроенные понятное жестов и простота взаимодействие между веб-страницы и приложения.
  
`WKWebView` можно добавить в приложение в почти такой же способ UIWebView, однако разработчик у вас есть гораздо больший контроль над пользовательского интерфейса и функциональности. Создание и отображение объекта view web отобразит запрошенной страницы, однако можно управлять как представление, как пользователь мог перейти и как пользователь выходит из представления.  

В приведенном ниже коде можно использовать для запуска `WKWebView` в приложение Xamarin.iOS:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

В результате получается следующий веб-представление:

[![](uiwebview-images/wkwebview.png "Пример веб-представление без ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

Важно отметить, что `WKWebView` находится в пространстве имен WebKit, чтобы вы могли добавить это с помощью директивы в начало класса.

`WKWebView` также может использоваться внутри приложений Xamarin.Mac и таким образом может потребоваться рассмотреть возможность его использования при создании кроссплатформенного приложения Mac или iOS.

[Обрабатывать предупреждения JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) рецепт также содержатся сведения об использовании WKWebView с Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` является последней способ предоставления веб-содержимого из приложения и в iOS 9 и более поздних версий. В отличие от `UIWebView` или `WKWebView`, `SFSafariViewController` является контроллером представления и поэтому не может использоваться с другими представлениями. Вы должны представить `SFSafariViewController` качестве нового контроллера представления, так же должны выводиться любого контроллера представления.
 
 `SFSafariViewController` — Это по сути «мини-safari», можно внедрять в приложения. Как WKWebView он использует один и тот же механизм Nitro Javascript, но также предоставляет широкий набор дополнительными возможностями Safari, как Автозаполнение, чтения и возможность совместно использовать файлы cookie и данные с мобильных устройств Safari. Взаимодействие между пользователем и `SFSafariViewController` недоступен для приложения. Приложение не будет доступа к любой из функций Safari по умолчанию.
 
Кроме того, по умолчанию реализует **сделать** кнопки, позволяя пользователю легко вернуться к приложению и вперед и назад, что позволяет переходить по стеку объектов веб-страниц. Кроме того он также предоставляет пользователю с помощью адресной строки предоставляя душевное спокойствие, что они ожидаемые веб-страницы. Поле адреса запрещает пользователю изменять URL-адрес. 

Эти реализации изменить нельзя, поэтому `SFSafariViewController` идеально подходит для использования в качестве браузера по умолчанию, если приложение хочет предоставить веб-страницы без какой-либо настройки.

В приведенном ниже коде можно использовать для запуска `SFSafariViewController` в приложение Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

В результате получается следующий веб-представление:

[![](uiwebview-images/sfsafariviewcontroller.png "Пример веб-представление с SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Можно также открыть мобильное приложение Safari из приложения, используя приведенный ниже код:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

В результате получается следующий веб-представление:

[![](uiwebview-images/safari.png "Веб-страницы, представленные в Safari")](uiwebview-images/safari.png#lightbox)

Перемещение пользователей из неработоспособной приложения Safari обычно всегда следует избегать. Большинство пользователей не будет ожидать навигации вне вашего приложения, поэтому если покинуть свое приложение, пользователи могут не возвращаться, по существу выполняется прерывание службы взаимодействия.

iOS 9 улучшения позволяют пользователю легко вернуться к приложения с помощью кнопки "Назад" в левом верхнем углу страницы Safari.

## <a name="app-transport-security"></a>Безопасность транспорта приложения

Безопасность транспорта приложения, или *ATS* появился компанией Apple в iOS 9, чтобы гарантировать соблюдение безопасное подключение рекомендации по всем каналам связи.

Дополнительные сведения о ATS, включая способы его реализации в приложении, см. [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md) руководства.

## <a name="related-links"></a>Связанные ссылки

- [Любимую (пример)](https://developer.xamarin.com/samples/monotouch/WebView/)
