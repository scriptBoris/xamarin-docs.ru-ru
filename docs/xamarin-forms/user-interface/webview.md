---
title: Веб-представления Xamarin.Forms
description: В этой статье объясняется, как использовать класс Xamarin.Forms WebView для представления локального или сетевого веб-содержимого и документы пользователям.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ed7bec4e25628d938218a40d157442debad8f835
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998378"
---
# <a name="xamarinforms-webview"></a>Веб-представления Xamarin.Forms

[`WebView`](xref:Xamarin.Forms.WebView) — Это представление для отображения HTML-содержимого и веб-приложения. В отличие от `OpenUri`, которая перенаправит пользователя на веб-браузер на устройстве, `WebView` отображает HTML содержимое внутри своего приложения.

![](webview-images/in-app-browser.png "В браузере приложения")

## <a name="content"></a>Content

`WebView` поддерживает следующие типы содержимого:

- Веб-сайтов HTML и CSS &ndash; WebView имеет полную поддержку для веб-сайтов, написанные с использованием HTML и CSS, включая поддержку JavaScript.
- Документы &ndash; так, как веб-представление реализуется с помощью собственных компонентов на каждой платформе, WebView способен отображать документы, которые можно просмотреть на каждой платформе. Это означает, что PDF-файлов работать в iOS и Android.
- Строк HTML &ndash; WebView можно показать строки HTML из памяти.
- Локальные файлы &ndash; веб-представление может представлять любой из типов содержимого, перечисленных выше внедренных в приложение.

> [!NOTE]
> `WebView` в Windows не поддерживает Silverlight, Flash или все элементы управления ActiveX, даже если они поддерживаются обозревателем Internet Explorer на этой платформе.

### <a name="websites"></a>Веб-сайты

Для отображения веб-сайта из Интернета, задайте `WebView` [ `Source` ](xref:Xamarin.Forms.WebViewSource) свойство на строку URL-адрес:

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URL-адреса должен быть полностью сформирован с протоколом, заданным (т. е. он должен иметь «http://» или «https://», прикрепленный к нему).

#### <a name="ios-and-ats"></a>iOS и ATS

Начиная с версии 9 iOS только позволит приложению для взаимодействия с серверами, реализующие рекомендации безопасности по умолчанию. Значения следует передавать `Info.plist` для обеспечения взаимодействия с серверами небезопасным.

> [!NOTE]
> Если приложению требуется подключение на небезопасной веб-сайте, следует всегда введите домен как исключений с помощью `NSExceptionDomains` вместо отключение ATS полностью с помощью `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` должен использоваться только в чрезвычайных ситуациях аварийного.

Ниже показано, как включить определенного домена (в этом вариантов xamarin.com), чтобы обойти требования к ATS:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
```

Его рекомендуется включать только некоторые домены для обхода ATS, позволяя использовать надежные узлы при пользоваться преимуществами дополнительную надежность в недоверенных доменах. Следующая команда демонстрирует метод менее безопасно отключить ATS для приложения:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

См. в разделе [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md) Дополнительные сведения об этой новой функции в iOS 9.

### <a name="html-strings"></a>Строк HTML

Если вы хотите представить строка HTML, определенные динамически в коде, необходимо создать экземпляр [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Отображение веб-представления HTML-строки")

В приведенном выше коде `@` используется для пометки HTML как строка литерала, то есть все обычные escape-символы учитываются.

### <a name="local-html-content"></a>Локальное содержимое HTML

Веб-представление может отображать содержимое HTML, CSS и Javascript, встроенным в приложении. Пример:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Обратите внимание на то, что шрифты, указанные в таблице выше CSS будет необходимо настроить для каждой платформы, как не каждая платформа имеет шрифты.

Для отображения локального содержимого с помощью `WebView`, вам потребуется открыть HTML-файл, как и любое другое, а затем загрузить содержимое в виде строки в `Html` свойство `HtmlWebViewSource`. Дополнительные сведения о открытия файлов, см. в разделе [работа с файлами](~/xamarin-forms/app-fundamentals/files.md).

На следующих снимках экрана показано отображение локального содержимого на каждой платформе результат:

![](webview-images/local-content.png "Отображение локального содержимого веб-представления")

Несмотря на то, что первая страница загрузится, `WebView` не имеет сведений о происхождения HTML. Это проблема, при работе со страницами, которые ссылаются на локальные ресурсы. Когда это может случиться примеры при другом, страницы делает ссылку локальных страниц использовать отдельный файл JavaScript, или страница ссылается на таблицу стилей CSS.  

Чтобы решить эту проблему, необходимо указать `WebView` место для поиска файлов в файловой системе. Этого нужно задать `BaseUrl` свойство `HtmlWebViewSource` используемые `WebView`.

Поскольку разные файловой системы на каждом из операционных систем, необходимо определить этот URL-адрес, на каждой платформе. Xamarin.Forms предоставляет `DependencyService` для разрешения зависимостей во время выполнения на каждой платформе.

Чтобы использовать `DependencyService`, сначала Определите интерфейс, который может быть реализован на каждой платформе:

```csharp
public interface IBaseUrl { string Get(); }
```

Обратите внимание на то, что до этого интерфейса возлагается на каждой платформе, приложение не запустится. В проекте common, убедитесь, что не забудьте установить для `BaseUrl` с помощью `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Затем необходимо предоставить реализации интерфейса для каждой платформы.

#### <a name="ios"></a>iOS

В iOS, веб-содержимого должен быть расположен в корневом каталоге проекта или **ресурсы** каталог с действием сборки *BundleResource*, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "Локальные файлы в iOS")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![](webview-images/ios-xs.png "Локальные файлы в iOS")

-----

`BaseUrl` Должно быть присвоено путь основного пакета:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

В Android, поместите HTML, CSS и изображений в папку средств с действием сборки *AndroidAsset* как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "Локальные файлы на устройстве Android")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![](webview-images/android-xs.png "Локальные файлы на устройстве Android")

-----

В Android `BaseUrl` должно быть присвоено `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

В Android, файлы в **активы** папка может также осуществляться через контекст текущего Android, который предоставляется командлетом `MainActivity.Instance` свойство:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В проектах универсальной платформы Windows (UWP), поместите HTML, CSS и изображений в корневую папку проекта с действием сборки *содержимого*.

`BaseUrl` Должно быть присвоено `"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Навигация

Веб-представления поддерживает навигацию по несколько методов и свойств, которые делает доступными:

- **GoForward()** &ndash; Если `CanGoForward` имеет значение true, вызвав `GoForward` переходит вперед на следующую страницу посещенных.
- **GoBack()** &ndash; Если `CanGoBack` имеет значение true, вызвав `GoBack` будет перейти к последней страницы.
- **CanGoBack** &ndash; `true` при наличии страниц для обратного перехода, `false` Если браузер находится в начальный URL-адрес.
- **Значение свойства CanGoForward** &ndash; `true` Если пользователь переходит назад и перейдите к странице, который уже был посещен.

На страницах `WebView` не поддерживает мультисенсорные жесты. Это важно, чтобы убедиться в том что содержимое, оптимизированный для мобильных устройств и отображается без необходимости для масштабирования.

Обычно для приложений отобразить ссылку в `WebView`, вместо того чтобы браузера устройства. В этих случаях полезно разрешить нормальный переход, но при попаданий пользователя создать резервную копию, а на начальную ссылку, приложение должно вернуть представление обычное приложение.

Используйте встроенные средства навигации методы и свойства, чтобы реализовать этот сценарий.

Начните с создания страницы для представления браузера:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В нашем коде:

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

Вот и все!

![](webview-images/in-app-browser.png "Кнопки навигации веб-представления")

## <a name="events"></a>События

Веб-представления вызывает два события, помогающие отвечать на изменения в состоянии.

- **Перемещение** &ndash; событие, возникающее при веб-представления загрузит новую страницу.
- **Переход** &ndash; события, возникающего при загрузке страницы и навигации был остановлен.

Если предполагается использование веб-страницы, занять много времени для загрузки, рассмотрите возможность использования этих событий для реализации индикатора состояния. Например, XAML выглядит следующим образом:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
  <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        IsVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

Два обработчика событий:

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
    LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
    LoadingLabel.IsVisible = false;
}
```

Это приводит к приведенным ниже (загрузка):

![](webview-images/loading-start.png "Пример события перехода WebView")

По завершении загрузки:

![](webview-images/loading-end.png "Веб-представления переход пример события")

## <a name="performance"></a>Производительность

Просмотрите энергоэкономичны все популярные веб-браузеры внедрять технологии, как и подготовки к просмотру и компиляция JavaScript с аппаратным ускорением. К сожалению, из-за ограничений безопасности, большинство этих улучшений не были доступны в equaivalent iOS из `WebView`, `UIWebView`. Xamarin.Forms `WebView` использует `UIWebView`. Если это проблема, нужно будет написать пользовательское средство отрисовки, использующий `WKWebView`, которая поддерживает быстрый просмотр. Обратите внимание, что `WKWebView` поддерживается только в iOS 8 и более поздних.

Веб-представления в Android по умолчанию почти так же быстро, как встроенного браузера.

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) использует механизм отрисовки Microsoft Edge. Настольных и планшетных устройств должны увидеть ту же производительность как с помощью сам браузер Edge.

## <a name="permissions"></a>Разрешения

Чтобы `WebView` для работы, необходимо убедиться в том что разрешения задаются для каждой платформы. Обратите внимание, что на некоторых платформах `WebView` работает в режиме отладки, но не в том случае, при построении для выпуска. Том, что некоторые разрешения, как и для доступа к Интернету на устройстве Android, устанавливаются по умолчанию с помощью Visual Studio для Mac при работе в режиме отладки.

- **UWP** &ndash; требуются возможности Интернет (клиент и сервер), при отображении содержимого сети.
- **Android** &ndash; требует `INTERNET` только в том случае, если отображение содержимого из сети. Локальное содержимое не требует специальных разрешений.
- **iOS** &ndash; не требует специальных разрешений.

## <a name="layout"></a>Макет

В отличие от большинства других представлений Xamarin.Forms `WebView` требует `HeightRequest` и `WidthRequest` указываются, если он содержится в StackLayout или RelativeLayout. Если не указать эти свойства `WebView` не будут отображаться.

В следующих примерах демонстрируется макеты, которые приводят к работе, подготовки к просмотру `WebView`s:

Макет StackLayout с WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout с WidthRequest & HeightRequest:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *без* WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Сетка *без* WidthRequest & HeightRequest. Сетка является одним из нескольких макетов, не требует указания запрошенного значения высоты и ширины.:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>Вызов JavaScript

[ `WebView` ](xref:Xamarin.Forms.WebView) Включает в себя возможность вызвать функцию JavaScript из C# и возвращает никакого результата в вызывающий код C#. Это осуществляется с помощью [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) метод, который показан в следующем примере из [WebView](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) пример:

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) Метод оценивает код JavaScript, указанное в качестве аргумента и возвращает любой результат в виде `string`. В этом примере `factorial` вызывается функция JavaScript, которая возвращает факториал `number` в результате. Этот код JavaScript, функция определена в HTML-код, локальный файл, который [ `WebView` ](xref:Xamarin.Forms.WebView) загружает и показан в следующем примере:

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="related-links"></a>Связанные ссылки

- [Работа с WebView (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [Веб-представления (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
