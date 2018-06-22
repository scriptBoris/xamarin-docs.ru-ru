---
title: Веб-представление
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2baf7dae71ce7607c629b570ad25f477dec66c17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765312"
---
# <a name="web-view"></a>Веб-представление

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) можно создать собственные окно для просмотра веб-страниц (или даже создавать полный браузера). В этом учебнике вы создадите простой [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) , можно просматривать и перейдите на веб-страницы.

Создайте новый проект с именем **HelloWebView**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Поскольку это приложение будет доступ в Интернет, следует добавить соответствующие разрешения для Android в файле манифеста. Откройте свойства проекта, чтобы указать, какие разрешения приложению для работы. Включить `INTERNET` разрешения, как показано ниже:

![Настройка разрешения ИНТЕРНЕТА в манифесте Android](web-view-images/01-set-internet-permissions.png)

Теперь откройте **MainActivity.cs** и добавить с помощью директивы Webkit:

```csharp
using Android.Webkit;
```

В верхней части `MainActivity` объявите [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) объекта:

```csharp
WebView web_view;
```

Когда **WebView** будет предложено загрузить URL-адрес, он будет по умолчанию делегировать запрос в браузере по умолчанию. Чтобы **WebView** загрузить URL-адрес (а не браузер по умолчанию), необходимо подкласс `Android.Webkit.WebViewClient` и Переопределите `ShouldOverriderUrlLoading` метод. Эта пользовательская экземпляр `WebViewClient` передается `WebView`. Чтобы сделать это, добавьте следующие вложенные `HelloWebViewClient` класса внутри `MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

Когда `ShouldOverrideUrlLoading` возвращает `false`, сообщает для Android, текущий `WebView` экземпляр обработки запроса, и никаких дальнейших действий не требуется. 

Если вы используете уровень API, 24 или более поздней версии, используйте перегруженный `ShouldOverrideUrlLoading` , который принимает `IWebResourceRequest` второго аргумента вместо `string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

Затем используйте следующий код для [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) метод:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

Инициализирует элемент [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) , из [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) макета и позволяет JavaScript для [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) с [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (см. [вызова C\# из JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript) рецепт сведения о способах вызова C\# функции из кода JavaScript). Наконец, веб-страницу начальной загрузки с [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Выполните сборку и запуск приложения. Вы увидите приложении просмотра простой веб-страницы, как показано на следующем снимке экрана:

[![Пример приложения, отображение веб-представление](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Для обработки **ОБРАТНО** нажатие клавиши, добавьте следующие инструкции с помощью:

```csharp
using Android.Views;
```

Добавьте следующий метод внутри `HelloWebView` действия:

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

Это [ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent)) метод обратного вызова вызывается при каждом нажатии кнопки во время выполнения действия. Условие в использует [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) для проверки ли нажата клавиша — **ОБРАТНО** кнопки и необходимость [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) способен фактически Переход обратно (если таковой имеется журнал). Если выполняются оба условия, то [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) вызывается метод, в которой можно перейти назад один шаг в [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) журнала. Возвращение `true` указывает, что событие было обработано. Если это условие не выполняется, это событие отправляется обратно в систему.

Снова запустите приложение. Теперь можно переходить по ссылкам и переход назад по журналу страницы:

[![Снимки экрана примера кнопки «Назад» в действии](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Связанные ссылки

- [C# вызова из кода JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
