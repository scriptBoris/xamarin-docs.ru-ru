---
title: Реализация HybridWebView
description: В этой статье показано, как создать настраиваемый отрисовщик для пользовательского элемента управления HybridWebView, который показывает, как улучшить зависящие от платформы веб-элементы управления, чтобы разрешить вызов кода C# из JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/19/2018
ms.openlocfilehash: aa060bd16bc0220f6a6026106ff6c8d786daebc1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105042"
---
# <a name="implementing-a-hybridwebview"></a>Реализация HybridWebView

_Настраиваемые элементы управления пользовательского интерфейса Xamarin.Forms должны быть производными от класса View, который используется для размещения макетов и элементов управления на экране. В этой статье показано, как создать настраиваемый отрисовщик для пользовательского элемента управления HybridWebView, который показывает, как улучшить зависящие от платформы веб-элементы управления, чтобы разрешить вызов кода C# из JavaScript._

Каждое представление Xamarin.Forms имеет сопутствующий отрисовщик для каждой платформы, который создает экземпляр собственного элемента управления. Когда [`View`](xref:Xamarin.Forms.View) отображается в приложении Xamarin.Forms, в iOS создается класс `ViewRenderer`, который, в свою очередь, создает собственный элемент управления `UIView`. На платформе Android класс `ViewRenderer` создает элемент управления `View`. На универсальной платформе Windows (UWP) класс `ViewRenderer` создает экземпляр собственного элемента управления `FrameworkElement`. Дополнительные сведения об отрисовщике и классах собственных элементов управления, с которыми сопоставляются элементы управления Xamarin.Forms, см. в статье [Базовые классы и собственные элементы управления отрисовщика](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между классом [`View`](xref:Xamarin.Forms.View) и соответствующими собственными элементами управления, которые его реализуют:

![](hybridwebview-images/view-classes.png "Связь между классом View и его реализующими собственными классами")

Процесс отрисовки можно использовать для реализации настроек конкретных платформ путем создания настраиваемого отрисовщика для [`View`](xref:Xamarin.Forms.View) на каждой платформе. Этот процесс выглядит следующим образом:

1. [Создание](#Creating_the_HybridWebView) пользовательского элемента управления `HybridWebView`.
1. [Использование ](#Consuming_the_HybridWebView) `HybridWebView` из Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) настраиваемого отрисовщика для `HybridWebView` на каждой платформе.

Мы рассмотрим каждый элемент по очереди, чтобы реализовать отрисовщик `HybridWebView`, который расширяет возможности зависящих от платформы веб-элементов управления для вызова кода C# из JavaScript. Экземпляр `HybridWebView` будет использоваться для отображения HTML-страницы, которая предлагает пользователю ввести свое имя. Затем, когда пользователь нажимает кнопку HTML, функция JavaScript вызывает C# `Action`, который отображает всплывающее окно, содержащее имя пользователя.

Дополнительные сведения о процессе вызова C# из JavaScript см. в разделе [Вызов C# из JavaScript](#Invoking_C_from_JavaScript). Дополнительные сведения о странице HTML см. в разделе [Создание веб-страницы](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Создание HybridWebView

Пользовательский элемент управления `HybridWebView` можно создать путем создания подклассов класса [`View`](xref:Xamarin.Forms.View), как показано в следующем примере кода:

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

Пользовательский элемент управления `HybridWebView` создается в проекте библиотеки .NET Standard и определяет следующий API для элемента управления:

- Свойство `Uri`, которое указывает адрес веб-страницы для загрузки.
- Метод `RegisterAction`, который регистрирует `Action` с элементом управления. Зарегистрированное действие будет вызываться из JavaScript, содержащегося в файле HTML, на который можно сослаться через свойство `Uri`.
- Метод `CleanUp`, который удаляет ссылку на зарегистрированный объект `Action`.
- Метод `InvokeAction`, который вызывает зарегистрированный объект `Action`. Этот метод будет вызываться из настраиваемого отрисовщика в зависящем от платформы проекте.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Использование HybridWebView

На пользовательский элемент управления `HybridWebView` можно ссылаться в XAML в проекте библиотеки .NET Standard, объявив пространство имен для его расположения и используя префикс пространства имен в пользовательском элементе управления. В следующем примере кода показано, как пользовательский элемент управления `HybridWebView` может использоваться страницей XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

Префикс пространства имен `local` может иметь любое имя. Тем не менее значения `clr-namespace` и `assembly` должны соответствовать данным пользовательского элемента управления. После объявления пространства имен префикс используется для ссылки на пользовательский элемент управления.

В следующем примере кода показано, как пользовательский элемент управления `HybridWebView` может использоваться страницей C#:

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

Экземпляр `HybridWebView` будет использоваться для отображения собственного веб-элемента управления на каждой платформе. Для его свойства `Uri` задан HTML-файл, который хранится в зависящем от платформы проекте и будет отображаться пользовательским элементом управления. Отрисованный HTML просит пользователя ввести свое имя с помощью JavaScript, вызывая C# `Action` в ответ на нажатие кнопки HTML.

`HybridWebViewPage` регистрирует действие, которое необходимо вызывать из JavaScript, как показано в следующем примере кода:

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Это действие вызывает метод [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) для отображения модального всплывающего окна, которое представляет имя, введенное на странице HTML, отображаемой экземпляром `HybridWebView`.

Настраиваемый отрисовщик теперь можно добавлять в каждый проект приложения для улучшения зависящих от платформы веб-элементов управления благодаря возможности вызова кода C# из JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского отрисовщика на каждой платформе

Процесс создания класса пользовательского отрисовщика выглядит следующим образом:

1. Создайте подкласс класса `ViewRenderer<T1,T2>`, который отрисовывает пользовательский элемент управления. Первый аргумент типа должен быть пользовательским элементом управления, для которого предназначен отрисовщик, в данном случае — `HybridWebView`. Второй аргумент типа должен быть собственным элементом управления, который будет реализовывать пользовательское представление.
1. Переопределите метод `OnElementChanged`, который отрисовывает пользовательский элемент управления, и напишите логику для его настройки. Этот метод вызывается, когда создается соответствующий пользовательский элемент управления Xamarin.Forms.
1. Добавьте атрибут `ExportRenderer` в класс пользовательского отрисовщика, чтобы указать, что он будет использоваться для отрисовки пользовательского элемента управления Xamarin.Forms. Этот атрибут используется для регистрации пользовательского отрисовщика в Xamarin.Forms.

> [!NOTE]
> Для большинства элементов Xamarin.Forms предоставлять пользовательский отрисовщик в проекте для каждой платформы необязательно. Если пользовательский отрисовщик не зарегистрирован, будет использоваться отрисовщик по умолчанию для базового класса элемента управления. Однако настраиваемые отрисовщики необходимы в каждом зависящем от платформы проекте при отрисовке элемента [View](xref:Xamarin.Forms.View).

На следующей схеме показаны обязанности каждого проекта в примере приложения, а также связи между ними:

![](hybridwebview-images/solution-structure.png "Обязанности проекта настраиваемого отрисовщика HybridWebView")

Пользовательский элемент управления `HybridWebView` отрисовывается с помощью зависящих от платформы классов отрисовщика, которые являются производными от класса `ViewRenderer` каждой платформы. Это приводит к тому, что каждый пользовательский элемент управления `HybridWebView` отрисовывается с помощью зависящих от платформы веб-элементов управления, как показано на следующих снимках экрана:

![](hybridwebview-images/screenshots.png "HybridWebView на каждой платформе")

Класс `ViewRenderer` предоставляет метод `OnElementChanged`, который вызывается при создании пользовательского элемента управления Xamarin.Forms для отрисовки соответствующего собственного веб-элемента управления. Этот метод принимает параметр `ElementChangedEventArgs`, содержащий свойства `OldElement` и `NewElement`. Эти свойства представляют элемент Xamarin.Forms, к которому *был* присоединен отрисовщик, и элемент Xamarin.Forms, к которому *присоединен* отрисовщик, соответственно. В примере приложения свойство `OldElement` будет иметь значение `null`, а свойство `NewElement` будет содержать ссылку на экземпляр `HybridWebView`.

Настройка и создание экземпляра собственного веб-элемента управления выполняется в переопределенной версии метода `OnElementChanged` в классе отрисовщика для каждой платформы. Метод `SetNativeControl` должен использоваться для создания экземпляра собственного веб-элемента управления и будет присваивать свойству `Control` ссылку на элемент управления. Кроме того, ссылку на отрисовываемый элемент управления Xamarin.Forms можно получить с помощью свойства `Element`.

В некоторых случаях метод `OnElementChanged` может вызываться несколько раз. Поэтому в целях предотвращения утечек памяти необходимо с осторожностью создавать собственный элемент управления. В следующем примере кода показан подход, используемый при создании собственного элемента управления в пользовательском отрисовщике:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Собственный элемент управления должен создаваться только один раз, когда свойству `Control` задано значение `null`. Элемент управления следует настраивать и подписывать на обработчики событий только при присоединении пользовательского отрисовщика к новому элементу Xamarin.Forms. Аналогично, от всех обработчиков событий, на которые были созданы подписки, следует отписываться только при изменении элемента с подключенным отрисовщиком. Реализовав этот подход, вы сможете создать пользовательский отрисовщик, на который не влияют утечки памяти.

Каждый класс пользовательского отрисовщика дополняется атрибутом `ExportRenderer`, который регистрирует отрисовщик в Xamarin.Forms. Атрибут принимает два параметра — имя типа отрисовываемого пользовательского элемента управления Xamarin.Forms и имя типа пользовательского отрисовщика. Префикс `assembly` в атрибуте указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются структура веб-страницы, загружаемой каждым собственным веб-элементом управления, процесс вызова C# из JavaScript и реализация этого в каждом зависящем от платформы классе настраиваемого отрисовщика.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Создание веб-страницы

В следующем примере кода показана веб-страница, которая будет отображаться пользовательским элементом управления `HybridWebView`:

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

Веб-страница позволяет пользователю ввести свое имя в элементе `input` и предоставляет элемент `button`, который будет вызывать код C# при нажатии. Этот процесс выглядит следующим образом:

- Когда пользователь нажимает на элемент `button`, вызывается функция JavaScript `invokeCSCode`, и значение элемента `input` передается в функцию.
- Функция `invokeCSCode` вызывает функцию `log` для отображения данных, которые она отправляет в C# `Action`. Затем она вызывает метод `invokeCSharpAction`, чтобы вызывать C# `Action`, передавая параметр, полученный от элемента `input`.

Функция JavaScript `invokeCSharpAction` не определена на веб-странице и внедряется в нее каждым настраиваемым отрисовщиком.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Вызов C# из JavaScript

Процесс вызова C# из JavaScript идентичен на каждой платформе:

- Настраиваемый отрисовщик создает собственный веб-элемент управления и загружает файл HTML, заданный свойством `HybridWebView.Uri`.
- После загрузки веб-страницы настраиваемый отрисовщик внедряет функцию JavaScript `invokeCSharpAction` в веб-страницу.
- Когда пользователь вводит свое имя и нажимает на элемент HTML `button`, вызывается функция `invokeCSCode`, которая, в свою очередь, вызывает функцию `invokeCSharpAction`.
- Функция `invokeCSharpAction` вызывает метод в настраиваемом отрисовщике, который, свою очередь, вызывает метод `HybridWebView.InvokeAction`.
- Метод `HybridWebView.InvokeAction` вызывает зарегистрированный `Action`.

В следующих разделах мы рассмотрим, как этот процесс реализуется на каждой платформе.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского отрисовщика в iOS

В следующем примере кода показан пользовательский отрисовщик для платформы iOS:

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

Класс `HybridWebViewRenderer` загружает веб-страницу, указанную в свойстве `HybridWebView.Uri`, в собственный элемент управления [`WKWebView`](https://developer.xamarin.com/api/type/WebKit.WKWebView/), а функция JavaScript `invokeCSharpAction` внедряется в веб-страницы. Когда пользователь вводит свое имя и нажимает элемент HTML `button`, выполняется функция JavaScript `invokeCSharpAction`, и метод `DidReceiveScriptMessage` вызывается после получения сообщения от веб-страницы. В свою очередь, этот метод вызывает метод `HybridWebView.InvokeAction`, который будет вызывать зарегистрированное действие для отображения всплывающего окна.

Это достигается следующим образом:

- При условии, что свойство `Control` является `null`, выполняются следующие операции:
  - Создается экземпляр [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/), который позволяет размещать сообщения и внедрять сценарии пользователя в веб-страницу.
  - Экземпляр [`WKUserScript`](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) создается для внедрения функции JavaScript `invokeCSharpAction` в веб-страницу после загрузки веб-страницы.
  - Метод [`WKUserContentController.AddScript`](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) добавляет экземпляр [`WKUserScript`](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) к контроллеру содержимого.
  - Метод [`WKUserContentController.AddScriptMessageHandler`](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) добавляет обработчик сообщений сценария с именем `invokeAction` в экземпляр [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/), который приводит к определению функции JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` во всех фреймах всех веб-представлений, которые будут использовать экземпляр `WKUserContentController`.
  - Создается экземпляр [`WKWebViewConfiguration`](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/), и экземпляр [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) устанавливается в качестве контроллера содержимого.
  - Создается экземпляр элемента управления [`WKWebView`](https://developer.xamarin.com/api/type/WebKit.WKWebView/), и вызывается метод `SetNativeControl`, чтобы назначить ссылку на элемент управления `WKWebView` свойству `Control`.
- Если настраваемый отрисовщик подключен к новому элементу Xamarin.Forms:
  - Метод [`WKWebView.LoadRequest`](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) загружает HTML-файл, указанный свойством `HybridWebView.Uri`. Код указывает, что файл сохранен в папке `Content` проекта. Когда веб-страница отображается, функция JavaScript `invokeCSharpAction` внедряется в веб-страницу.
- Когда элемент, к которому присоединен отрисовщик, меняется:
  - Ресурсы освобождаются.

> [!NOTE]
> Класс `WKWebView` поддерживается только в iOS 8 и более поздних версий.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского отрисовщика в Android

В следующем примере кода показан пользовательский отрисовщик для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

Класс `HybridWebViewRenderer` загружает веб-страницу, указанную в свойстве `HybridWebView.Uri`, в собственный элемент управления [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/), а функция JavaScript `invokeCSharpAction` внедряется в веб-страницы после окончания загрузки веб-страницы с переопределением `OnPageFinished` в классе `JavascriptWebViewClient`:

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Когда пользователь вводит свое имя и нажимает элемент HTML `button`, выполняется функция JavaScript `invokeCSharpAction`. Это достигается следующим образом:

- При условии, что свойство `Control` является `null`, выполняются следующие операции:
  - Создается собственный экземпляр [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/), JavaScript включается в элементе управления, а экземпляр `JavascriptWebViewClient` задается как реализация `WebViewClient`.
  - Вызывается метод `SetNativeControl`, чтобы назначить ссылку на элемент управления [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) свойству `Control`.
- Если настраваемый отрисовщик подключен к новому элементу Xamarin.Forms:
  - Метод [`WebView.AddJavascriptInterface`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) вставляет новый экземпляр `JSBridge` в главный фрейм контекста JavaScript веб-преставления, называя его `jsBridge`. Это открывает доступ к методам в классе `JSBridge` из JavaScript.
  - Метод [`WebView.LoadUrl`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) загружает HTML-файл, указанный свойством `HybridWebView.Uri`. Код указывает, что файл сохранен в папке `Content` проекта.
  - В классе `JavascriptWebViewClient` функция JavaScript `invokeCSharpAction` внедряется в веб-страницу после завершения загрузки страницы.
- Когда элемент, к которому присоединен отрисовщик, меняется:
  - Ресурсы освобождаются.

Когда выполняется функция JavaScript `invokeCSharpAction`, она, в свою очередь, вызывает метод `JSBridge.InvokeAction`, который показан в следующем примере кода:

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

Класс должен быть производным от `Java.Lang.Object`, и методы, предоставляемые для JavaScript, должны быть снабжены атрибутами `[JavascriptInterface]` и `[Export]`. Таким образом, когда функция JavaScript `invokeCSharpAction` внедряется в веб-страницу и выполняется, она вызывает метод `JSBridge.InvokeAction`, поскольку имеет атрибуты `[JavascriptInterface]` и `[Export("invokeAction")]`. В свою очередь, этот метод `InvokeAction` вызывает метод `HybridWebView.InvokeAction`, который будет вызывать зарегистрированное действие для отображения всплывающего окна.

> [!NOTE]
> Проекты, использующие атрибут `[Export]`, должны содержать ссылку на `Mono.Android.Export`, или возникнет ошибка компилятора.

Обратите внимание, что класс `JSBridge` поддерживает `WeakReference` для класса `HybridWebViewRenderer`. Это позволяет избежать создания циклической ссылки между двумя классами. Дополнительные сведения см. в разделе [Слабые ссылки](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) на MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского отрисовщика на универсальной платформе Windows

В следующем примере кода показан пользовательский отрисовщик для платформы UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

Класс `HybridWebViewRenderer` загружает веб-страницу, указанную в свойстве `HybridWebView.Uri` в собственном элементе управления `WebView`, а функция JavaScript `invokeCSharpAction` внедряется в веб-страницы после загрузки веб-страницы с методом `WebView.InvokeScriptAsync`. Когда пользователь вводит свое имя и нажимает элемент HTML `button`, выполняется функция JavaScript `invokeCSharpAction`, и метод `OnWebViewScriptNotify` вызывается после получения уведомления от веб-страницы. В свою очередь, этот метод вызывает метод `HybridWebView.InvokeAction`, который будет вызывать зарегистрированное действие для отображения всплывающего окна.

Это достигается следующим образом:

- При условии, что свойство `Control` является `null`, выполняются следующие операции:
  - Вызывается метод `SetNativeControl`, чтобы создать экземпляр нового собственного элемента управления `WebView` и назначить ссылку на него свойству `Control`.
- Если настраваемый отрисовщик подключен к новому элементу Xamarin.Forms:
  - Обработчики событий для событий `NavigationCompleted` и `ScriptNotify` регистрируются. Событие `NavigationCompleted` возникает, когда собственный элемент управления `WebView` завершил загрузку текущего содержимого или произошел сбой навигации. Событие `ScriptNotify` возникает, когда содержимое в собственном элементе управления `WebView` использует JavaScript для передачи строки в приложение. Веб-страница активирует событие `ScriptNotify` путем вызова `window.external.notify` с передачей параметра `string`.
  - Свойство `WebView.Source` получает значение URI HTML-файла, который задается свойством `HybridWebView.Uri`. Код предполагает, что файл сохранен в папке `Content` проекта. Когда веб-страница отобразится, событие `NavigationCompleted` сработает и метод `OnWebViewNavigationCompleted` будет вызван. Функция JavaScript `invokeCSharpAction` будет внедрена в веб-страницу с помощью метода `WebView.InvokeScriptAsync`, если навигация успешно завершена.
- Когда элемент, к которому присоединен отрисовщик, меняется:
  - Подписка от событий отменяется.

## <a name="summary"></a>Сводка

В этой статье показано, как создать настраиваемый отрисовщик для пользовательского элемента управления `HybridWebView`, который показывает, как улучшить зависящие от платформы веб-элементы управления, чтобы разрешить вызов кода C# из JavaScript.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererHybridWebView (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Вызов C# из JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
