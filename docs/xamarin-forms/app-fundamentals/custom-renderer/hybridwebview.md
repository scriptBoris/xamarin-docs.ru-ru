---
title: Реализация HybridWebView
description: В этой статье показано, как создать пользовательское средство отрисовки HybridWebView пользовательского элемента управления, который показывает, как улучшить веб-платформы элементов управления, чтобы разрешить коду C# вызываться из JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/19/2018
ms.openlocfilehash: aa060bd16bc0220f6a6026106ff6c8d786daebc1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105042"
---
# <a name="implementing-a-hybridwebview"></a>Реализация HybridWebView

_Элементы управления Xamarin.Forms настраиваемый пользовательский интерфейс должен быть производным от класса представления, который используется для размещения макеты и элементы управления на экране. В этой статье показано, как создать пользовательское средство отрисовки HybridWebView пользовательского элемента управления, который показывает, как улучшить веб-платформы элементов управления, чтобы разрешить коду C# вызываться из JavaScript._

Каждое представление Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `View` ](xref:Xamarin.Forms.View) отображается в приложении Xamarin.Forms в iOS, `ViewRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `UIView` элемента управления. На платформе Android `ViewRenderer` создает экземпляр класса `View` элемента управления. В универсальной платформы Windows (UWP), `ViewRenderer` класс создает экземпляр собственного `FrameworkElement` элемента управления. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `View` ](xref:Xamarin.Forms.View) и соответствующие собственные элементы управления, которые реализуют его:

![](hybridwebview-images/view-classes.png "Отношение между классом представления и его реализации собственных классов")

Процесс подготовки отчета можно использовать для реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `View` ](xref:Xamarin.Forms.View) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_HybridWebView) `HybridWebView` пользовательского элемента управления.
1. [Использовать](#Consuming_the_HybridWebView) `HybridWebView`из Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательское средство отрисовки для `HybridWebView` на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь для реализации `HybridWebView` модуля подготовки отчетов, который расширяет возможности веб-платформы элементов управления, чтобы разрешить коду C# вызываться из JavaScript. `HybridWebView` Экземпляр будет использоваться для отображения HTML-страницы, пользователю предлагается ввести свое имя. Затем, когда пользователь нажимает кнопку HTML, будет вызывать функцию JavaScript C# `Action` , отображается всплывающее окно, содержащее имя пользователей.

Дополнительные сведения о процессе для вызова C# из JavaScript, см. в разделе [вызова C# из JavaScript](#Invoking_C_from_JavaScript). Дополнительные сведения о странице HTML, см. в разделе [Создание веб-страницы](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Создание HybridWebView

`HybridWebView` Пользовательского элемента управления, которые могут создаваться путем создания подклассов [ `View` ](xref:Xamarin.Forms.View) класса, как показано в следующем примере кода:

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

`HybridWebView` Пользовательский элемент управления создается в проекте библиотеки .NET Standard и определяет следующий API для элемента управления:

- Объект `Uri` свойство, которое указывает адрес веб-страницы для загрузки.
- Объект `RegisterAction` метод, который регистрирует `Action` с элементом управления. Зарегистрированных действий будет вызываться из JavaScript, содержащихся в файле HTML, который ссылается через `Uri` свойство.
- Объект `CleanUp` метод, который удаляет ссылку к зарегистрированному `Action`.
- `InvokeAction` Метода, который вызывает зарегистрированную `Action`. Этот метод будет вызываться из пользовательского средства визуализации в каждом проекте под конкретную платформу.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Использование HybridWebView

`HybridWebView` Пользовательского элемента управления можно ссылаться в XAML в проекте библиотеки .NET Standard, объявление пространства имен для его расположения и используя префикс пространства имен на пользовательском элементе управления. В следующем примере кода показано как `HybridWebView` пользовательский элемент управления может быть поглощен страницу XAML:

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

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны соответствовать данные пользовательского элемента управления. После объявления пространства имен, префикс используется для ссылки на пользовательский элемент управления.

В следующем примере кода показано как `HybridWebView` пользовательский элемент управления может быть поглощен страницы C#:

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

`HybridWebView` Экземпляр будет использоваться для отображения собственного веб-элемент управления на каждой платформе. Он имеет `Uri` задано в HTML-файле, который хранится в каждом проекте под конкретную платформу и отображаемые с собственного веб-элементе управления. Формате HTML просит пользователя ввести свое имя и в функции JavaScript, вызов C# `Action` в ответ на нажатие кнопки HTML.

`HybridWebViewPage` Регистрирует действие, которое необходимо вызывать из JavaScript, как показано в следующем примере кода:

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

Это действие вызывает [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) метод для отображения модального всплывающего окна, которая представляет имя, введенное на странице HTML, отображаемого элементом `HybridWebView` экземпляра.

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для улучшения платформы веб-элементов управления, позволяя код C# можно было вызывать из JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `ViewRenderer<T1,T2>` класс, который выполняет визуализацию пользовательского элемента управления. Первый аргумент типа должен быть пользовательского элемента управления, в данном случае это модуль подготовки отчетов `HybridWebView`. Второй аргумент типа должен быть собственный элемент управления, который будет реализовывать пользовательское представление.
1. Переопределить `OnElementChanged` метода, который отображает пользовательскую логику управления и записи для его настройки. Этот метод вызывается при создании соответствующего пользовательского элемента управления Xamarin.Forms.
1. Добавление `ExportRenderer` атрибут класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для подготовки к просмотру пользовательский элемент управления Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Для большинства элементов Xamarin.Forms является необязательным для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса элемента управления. Однако пользовательские модули подготовки отчетов необходимы в каждом проекте платформы при отрисовке [представление](xref:Xamarin.Forms.View) элемент.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](hybridwebview-images/solution-structure.png "Обязанности проекта HybridWebView пользовательский модуль подготовки отчетов")

`HybridWebView` Пользовательский элемент управления отрисовывается классами платформы модуля подготовки отчетов, которые являются производными от `ViewRenderer` класс для каждой платформы. Это приводит к каждой `HybridWebView` пользовательского элемента управления, отображаемого с платформой веб-элемент управления, как показано на следующем снимке экрана:

![](hybridwebview-images/screenshots.png "HybridWebView на каждой платформе")

`ViewRenderer` Предоставляет `OnElementChanged` метод, который вызывается при создании пользовательского элемента управления Xamarin.Forms для отрисовки соответствующего собственного веб-элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, содержащий `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В примере приложения `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `HybridWebView` экземпляра.

Переопределенная версия `OnElementChanged` метод в каждом классе платформы модуля подготовки отчетов — место для создания экземпляра элемента управления собственного web и настройки. `SetNativeControl` Метод должен использоваться для создания собственного веб-элементе управления, и этот метод также будет назначить ссылку на элемент управления `Control` свойство. Кроме того, можно получить ссылку на элемент управления Xamarin.Forms, который готовится к просмотру с помощью `Element` свойство.

В некоторых случаях `OnElementChanged` метод может вызываться несколько раз. Таким образом для предотвращения утечек памяти, необходимо соблюдать осторожность при создании экземпляра нового собственного элемента управления. В следующем примере кода показан подход, используемый при создании собственного элемента управления в пользовательском отрисовщике:

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

Собственный элемент управления должен создаваться только один раз, когда свойству `Control` задано значение `null`. Элемент управления следует настраивать и подписывать на обработчики событий только при присоединении пользовательского отрисовщика к новому элементу Xamarin.Forms. Аналогично, от всех обработчиков событий, на которые были созданы подписки, следует отписываться только при изменении элемента с подключенным отрисовщиком. Этот подход поможет для создания высокопроизводительных пользовательского средства визуализации, который не страдает от утечек памяти.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя пользовательского элемента управления Xamarin.Forms визуализируемого типа и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются структуры веб-страницы загрузки каждого собственного веб-элемента управления, процесс вызова C# из кода JavaScript и реализует эти операции в каждом классе пользовательское средство отрисовки для конкретной платформы.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Создание веб-страницы

В следующем примере кода показан веб-страницы, которое будет отображаться по `HybridWebView` пользовательского элемента управления:

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

Веб-страницы позволяет пользователю ввести свое имя в `input` элемент и предоставляет `button` элемент, который будет вызывать код C# при щелчке. Для достижения этих целей процесс выглядит следующим образом:

- Когда пользователь нажимает на `button` элемент, `invokeCSCode` вызывается функция JavaScript, со значением `input` элемента, переданного в функцию.
- `invokeCSCode` Вызовы функций `log` функцию для отображения данных, отправляемыми в C# `Action`. Затем он вызывает `invokeCSharpAction` метод, вызываемый в C# `Action`, передача параметров, полученных от `input` элемент.

`invokeCSharpAction` Функцию JavaScript не определен в веб-страницы и внедряются в него каждого пользовательского средства визуализации.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Вызов из JavaScript C#

Процесс вызова C# из JavaScript не идентичны для каждой платформы:

- Пользовательское средство отрисовки создает собственный веб-элемент управления и загружает файл HTML, заданный параметром `HybridWebView.Uri` свойство.
- После загрузки веб-страницы, пользовательское средство отрисовки внедряет `invokeCSharpAction` функции JavaScript в веб-страницу.
- Когда пользователь вводит свои имя и нажимает кнопку на HTML- `button` элемент, `invokeCSCode` вызывается функция, которая в свою очередь вызывает `invokeCSharpAction` функции.
- `invokeCSharpAction` Функция вызывает метод в пользовательское средство отрисовки, в свою очередь вызывает `HybridWebView.InvokeAction` метод.
- `HybridWebView.InvokeAction` Метод вызывает зарегистрированный `Action`.

В следующих разделах будет рассматриваются, как этот процесс реализуется на каждой платформе.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

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

`HybridWebViewRenderer` Класс загружает веб-страницы, указанной в `HybridWebView.Uri` свойство в машинном коде [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) элемента управления и `invokeCSharpAction` функцию JavaScript внедряется в веб-страницы. Когда пользователь вводит свое имя и нажимает кнопку HTML `button` элемент, `invokeCSharpAction` выполняется функция JavaScript, с помощью `DidReceiveScriptMessage` метод, вызываемый после получения сообщения из веб-страницы. В свою очередь, вызывает этот метод `HybridWebView.InvokeAction` метод, который будет вызывать зарегистрированных действий для отображения всплывающего окна.

Это достигается следующим образом:

- При условии, что `Control` свойство `null`, выполняются следующие операции:
  - Объект [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) создается экземпляр, который позволяет размещать сообщения и Включение сценариев пользователя в веб-страницы.
  - Объект [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) внедрить создается экземпляр `invokeCSharpAction` функции JavaScript в веб-страницу после загрузки веб-страницы.
  - [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) Добавляет метод [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) экземпляра к контроллеру содержимого.
  - [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) Метод добавляет обработчик сообщения сценария с именем `invokeAction` для [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) экземпляр, который вызывает функцию JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` должна определяться во всех кадры в все веб-представления, которые будут использовать `WKUserContentController` экземпляра.
  - Объект [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) , создается экземпляр с [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) экземпляр заданием в качестве содержимого контроллера.
  - Объект [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) создается экземпляр элемента управления и `SetNativeControl` вызывается метод, чтобы назначить ссылку на `WKWebView` управления `Control` свойство.
- При условии, что пользовательское средство отрисовки присоединен к новому элементу Xamarin.Forms:
  - [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) Метод загружает HTML-файл, который задается параметром `HybridWebView.Uri` свойство. Код указывает, что файл сохранен в `Content` папку проекта. Если отображается веб-страницы, `invokeCSharpAction` внедряются функции JavaScript в веб-страницы.
- При присоединении элемента для изменения:
  - Ресурсы освобождаются.

> [!NOTE]
> `WKWebView` Класса поддерживается только в iOS 8 и более поздних версий.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

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

`HybridWebViewRenderer` Класс загружает веб-страницы, указанной в `HybridWebView.Uri` свойство в машинном коде [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) элемента управления и `invokeCSharpAction` функцию JavaScript внедряется в веб-страницы, после веб-страницы завершена загрузка, `OnPageFinished` в Переопределите `JavascriptWebViewClient` класса:

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

Когда пользователь вводит свое имя и нажимает кнопку HTML `button` элемент, `invokeCSharpAction` выполняется функция JavaScript. Это достигается следующим образом:

- При условии, что `Control` свойство `null`, выполняются следующие операции:
  - Собственный [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) создается экземпляр, включить JavaScript в элементе управления и `JavascriptWebViewClient` экземпляра задается как реализация `WebViewClient`.
  - `SetNativeControl` Вызывается метод, чтобы назначить ссылку на собственный [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) управления `Control` свойство.
- При условии, что пользовательское средство отрисовки присоединен к новому элементу Xamarin.Forms:
  - [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) Метод вставляет новый `JSBridge` экземпляра в главном фрейме контекста JavaScript WebView, назовите его `jsBridge`. Это позволит методам в `JSBridge` класс должен быть предоставлен доступ из JavaScript.
  - [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) Метод загружает HTML-файл, который задается параметром `HybridWebView.Uri` свойство. Код указывает, что файл сохранен в `Content` папку проекта.
  - В `JavascriptWebViewClient` класс, `invokeCSharpAction` функцию JavaScript внедряется в веб-страницы, после завершения загрузки страницы.
- При присоединении элемента для изменения:
  - Ресурсы освобождаются.

Когда `invokeCSharpAction` выполняется функция JavaScript, в свою очередь вызывает `JSBridge.InvokeAction` метод, который показан в следующем примере кода:

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

Класс должен наследоваться от `Java.Lang.Object`, и методы, предоставляемые в код JavaScript должен быть снабжен атрибутом `[JavascriptInterface]` и `[Export]` атрибуты. Таким образом, когда `invokeCSharpAction` функция JavaScript, внедряется в веб-страницы и выполняется, он вызывает `JSBridge.InvokeAction` метод из-за с атрибутом `[JavascriptInterface]` и `[Export("invokeAction")]` атрибуты. В свою очередь `InvokeAction` вызывает метод `HybridWebView.InvokeAction` метода, которые вызванного зарегистрированных действий для отображения всплывающего окна.

> [!NOTE]
> Проекты, использующие `[Export]` атрибут должен содержать ссылку на `Mono.Android.Export`, или приведет к ошибке компилятора.

Обратите внимание, что `JSBridge` класс поддерживает `WeakReference` для `HybridWebViewRenderer` класса. Это позволяет избежать создания циклическую ссылку между двумя классами. Дополнительные сведения см. в разделе [слабые ссылки](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) на сайте MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского средства визуализации на UWP

В следующем примере кода показано пользовательское средство отрисовки для универсальной платформы Windows:

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

`HybridWebViewRenderer` Класс загружает веб-страницы, указанной в `HybridWebView.Uri` свойство в машинном коде `WebView` элемента управления и `invokeCSharpAction` функцию JavaScript внедряется в веб-страницы, после загрузки веб-страницы с `WebView.InvokeScriptAsync` метод. Когда пользователь вводит свое имя и нажимает кнопку HTML `button` элемент, `invokeCSharpAction` выполняется функция JavaScript, с помощью `OnWebViewScriptNotify` метод, вызываемый после получения уведомления от веб-страницы. В свою очередь, вызывает этот метод `HybridWebView.InvokeAction` метод, который будет вызывать зарегистрированных действий для отображения всплывающего окна.

Это достигается следующим образом:

- При условии, что `Control` свойство `null`, выполняются следующие операции:
  - `SetNativeControl` Метод вызывается для создания экземпляра новой собственный `WebView` управления и назначить ссылку на него в `Control` свойство.
- При условии, что пользовательское средство отрисовки присоединен к новому элементу Xamarin.Forms:
  - Обработчики событий для `NavigationCompleted` и `ScriptNotify` их регистрации. `NavigationCompleted` Событие возникает при любом собственного `WebView` элемент управления загрузил текущего содержимого или неспособность навигации. `ScriptNotify` Событие запускается, когда содержимое в собственной `WebView` элемент управления использует JavaScript для передачи строки в приложение. Веб-странице активируется `ScriptNotify` событие путем вызова `window.external.notify` передавать `string` параметра.
  - `WebView.Source` Свойство имеет значение URI HTML-файла, который задается параметром `HybridWebView.Uri` свойство. В коде предполагается, что файл сохранен в `Content` папку проекта. Если отображается веб-страницы, `NavigationCompleted` событие будет срабатывать и `OnWebViewNavigationCompleted` метод будет вызван. `invokeCSharpAction` Функцию JavaScript затем вставляется в веб-страницу с `WebView.InvokeScriptAsync` метод, при условии, что навигации, успешно завершена.
- При присоединении элемента для изменения:
  - События, отменена.

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательское средство отрисовки для `HybridWebView` пользовательского элемента управления, в котором показано, как улучшить веб-платформы элементов управления, чтобы разрешить коду C# вызываться из JavaScript.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererHybridWebView (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [C# вызов из JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
