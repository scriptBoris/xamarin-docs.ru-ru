---
title: Веб-представление
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ae0b67de5856e6baef9a4989a93e65ead2854a62
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110086"
---
# <a name="web-view"></a>Веб-представление

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) можно создать собственные окно просмотра веб-страниц (или даже разрабатывать полный браузера). В этом руководстве вы создадите простой [`Activity`](https://developer.xamarin.com/api/type/Android.App.Activity/)
можно просматривать и веб-страницы.

Создайте новый проект с именем **HelloWebView**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий текст:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Так как это приложение будет получить доступ к Интернету, необходимо добавить соответствующие разрешения на Android файл манифеста. Откройте свойства проекта, чтобы указать, какие разрешения приложению для работы. Включить `INTERNET` разрешения, как показано ниже:

![Разрешение INTERNET в манифест Android](web-view-images/01-set-internet-permissions.png)

Теперь откройте **MainActivity.cs** и добавить с помощью директив для Webkit:

```csharp
using Android.Webkit;
```

В верхней части `MainActivity` объявите [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) объекта:

```csharp
WebView web_view;
```

Когда **WebView** — запрос на URL-адрес, он будет по умолчанию делегировать запрос к браузеру по умолчанию. Чтобы **WebView** загрузить URL-адрес (вместо браузер по умолчанию), вам необходимо подкласс `Android.Webkit.WebViewClient` и переопределить `ShouldOverriderUrlLoading` метод. Экземпляр эта пользовательская `WebViewClient` предоставляется `WebView`. Чтобы сделать это, добавьте следующие вложенные `HelloWebViewClient` класс `MainActivity`:

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

Когда `ShouldOverrideUrlLoading` возвращает `false`, он сообщает о для Android, текущего `WebView` экземпляр обработал запрос, и никаких дальнейших действий не требуется. 

Если вы используете API уровня 24 или более поздней версии, воспользуйтесь перегрузкой `ShouldOverrideUrlLoading` , принимающий `IWebResourceRequest` второго аргумента вместо `string`:

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

Затем используйте следующий код для [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
метод:

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

Это инициализирует член [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) , из [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/) макета и позволяет JavaScript для [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) с [ `JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (см. в разделе [C, вызовите\# из JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) рецепт сведения о том, как вызов C\# функции из кода JavaScript). Наконец, веб-страницу начальной загружается с [ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl).

Выполните сборку и запуск приложения. Вы должны увидеть приложение для просмотра простой веб-страницы, как показано на следующем снимке экрана:

[![Пример приложения, отображение веб-представление](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Для обработки **ОБРАТНО** "Кнопка" клавиши, добавьте следующий оператор using:

```csharp
using Android.Views;
```

Добавьте следующий метод в `HelloWebView` действия:

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

Этот [`OnKeyDown(int, KeyEvent)`](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent))
метод обратного вызова будет вызываться при каждом нажатии кнопки во время выполнения действия. Условие в использует [ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/) проверяемый ли нажата клавиша является **ОБРАТНО** кнопку и был ли [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) способен фактически Переход назад (если он имеет журнал). Если выполняются оба условия, а затем [ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/) вызывается метод, которому будет перемещаться назад на одну этапом [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) журнал. Возвращение `true` указывает, что событие было обработано. Если это условие не соблюдено, это событие отправляется обратно в систему.

Снова запустите приложение. Теперь можно переходить по ссылкам, а также переходить журнала страниц:

[![Снимки экрана с кнопкой «Назад» в действии](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>Связанные ссылки

- [C# вызов из JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)
