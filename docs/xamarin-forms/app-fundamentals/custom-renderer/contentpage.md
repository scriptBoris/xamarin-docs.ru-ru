---
title: Настройка ContentPage
description: ContentPage — это визуальный элемент, который отображает одно представление и занимает большую часть экрана. В этой статье показано, как создать пользовательское средство отрисовки страницы ContentPage, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы.
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995746"
---
# <a name="customizing-a-contentpage"></a>Настройка ContentPage

_ContentPage — это визуальный элемент, который отображает одно представление и занимает большую часть экрана. В этой статье показано, как создать пользовательское средство отрисовки страницы ContentPage, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы._

Каждый элемент управления Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) отображается в приложении Xamarin.Forms в iOS `PageRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `UIViewController` элемента управления. На платформе Android `PageRenderer` создает экземпляр класса `ViewGroup` элемента управления. В универсальной платформы Windows (UWP), `PageRenderer` создает экземпляр класса `FrameworkElement` элемента управления. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) и соответствующие собственные элементы управления, которые реализуют его:

![](contentpage-images/contentpage-classes.png "Отношение между классом ContentPage и реализации собственных элементов управления")

Процесс подготовки отчета можно задействуем преимущества реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Xamarin.Forms_Page) страницы Xamarin.Forms.
1. [Использовать](#Consuming_the_Xamarin.Forms_Page) страницы из Xamarin.Forms.
1. [Создание](#Creating_the_Page_Renderer_on_each_Platform) пользовательское средство отрисовки для страницы на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации `CameraPage` , предоставляющий динамической камеры и собирать фотографии.

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>Создание страницы Xamarin.Forms

Неизмененный [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) можно добавить в общий проект Xamarin.Forms, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

Аналогичным образом, файл с выделенным кодом для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) также должны оставаться без изменений, как показано в следующем примере кода:

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

В следующем примере кода показано, как в C#, в котором можно создавать страницы:

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

Экземпляр `CameraPage` будет использоваться для отображения динамического камеры на каждой платформе. Настройка элемента управления будет осуществляться в пользовательское средство отрисовки, поэтому нет дополнительных реализации не требуется в `CameraPage` класса.

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>Использование страницы Xamarin.Forms

Пустой `CameraPage` должен отображаться с приложением Xamarin.Forms. Это происходит, если кнопки на `MainPage` шифрованию экземпляр, который в свою очередь выполняет `OnTakePhotoButtonClicked` метод, как показано в следующем примере кода:

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

Этот код просто переходит к `CameraPage`, на какие пользовательские модули подготовки отчетов будет настраивать внешний вид страницы, на каждой платформе.

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>Создание страницы модуля подготовки отчетов на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `PageRenderer` класса.
1. Переопределить `OnElementChanged` метод, который использует собственный логику страницы и запись для настройки страницы. `OnElementChanged` Метод вызывается, когда создается соответствующий элемент управления Xamarin.Forms.
1. Добавление `ExportRenderer` атрибута к классу страницы модуля подготовки отчетов, чтобы указать, что он будет использоваться для визуализации страницы Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Это необязательно для предоставления в модуль подготовки страницы в каждом проекте платформы. Если не зарегистрировано в модуль подготовки страницы, будет использоваться модуль подготовки отчетов по умолчанию для страницы.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](contentpage-images/solution-structure.png "Обязанности проекта CameraPage пользовательский модуль подготовки отчетов")

`CameraPage` Экземпляра обрабатывается платформой `CameraPageRenderer` классы, которые являются производными от `PageRenderer` класса для данной платформы. Это приводит к каждой `CameraPage` экземпляра, отрисовываемого с камеры в реальном времени веб-канал, как показано на следующем снимке экрана:

![](contentpage-images/screenshots.png "CameraPage на каждой платформе")

`PageRenderer` Предоставляет `OnElementChanged` метод, который вызывается при создании страницы Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, содержащий `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В примере приложения `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `CameraPage` экземпляра.

Переопределенная версия `OnElementChanged` метод в `CameraPageRenderer` класс — это место для выполнения настройки собственного страницы. Можно получить ссылку на экземпляр страницы Xamarin.Forms, которое отображается с помощью `Element` свойство.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя типа для отображаемой странице Xamarin.Forms и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматривается реализация `CameraPageRenderer` пользовательского средства визуализации для каждой платформы.

### <a name="creating-the-page-renderer-on-ios"></a>Создание страницы модуля подготовки отчетов в iOS

В следующем примере кода показано средство отрисовки страницы для платформы iOS:

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

Вызов базового класса `OnElementChanged` метод создает экземпляр iOS `UIViewController` элемента управления. Поток динамической камеры отображается только условии, что модуль подготовки отчетов, уже не присоединяется к существующему элементу Xamarin.Forms, и условии, что существует экземпляр страницы, готовится к просмотру модулем подготовки отчетов настраиваемые.

Страницы затем настраивается путем ряд методов, использующих `AVCapture` API-интерфейсы для предоставления потоковой трансляции с камеры и собирать фотографии.

### <a name="creating-the-page-renderer-on-android"></a>Создание страницы модуля подготовки отчетов в Android

В следующем примере кода показано средство отрисовки страницы для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

Вызов базового класса `OnElementChanged` метод создает экземпляр Android `ViewGroup` элемент управления, который представляет собой группу представления. Поток динамической камеры отображается только условии, что модуль подготовки отчетов, уже не присоединяется к существующему элементу Xamarin.Forms, и условии, что существует экземпляр страницы, готовится к просмотру модулем подготовки отчетов настраиваемые.

Страницы затем настраивается путем вызова ряд методов, использующих `Camera` API, чтобы обеспечить потоковую трансляцию с камеры и собирать фотографии, прежде чем `AddView` метод вызывается для добавления динамической камеры поток пользовательского интерфейса для `ViewGroup`. Обратите внимание, что в Android также требуется переопределять `OnLayout` метод для выполнения операций мер и макет в представлении. Дополнительные сведения см. в разделе [образец модуля подготовки отчетов ContentPage](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/).

### <a name="creating-the-page-renderer-on-uwp"></a>Создание страницы модуля подготовки отчетов на UWP

В следующем примере кода показано, модуль подготовки отчетов страницы для универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

Вызов базового класса `OnElementChanged` метод создает экземпляр `FrameworkElement` элемента управления, на котором визуализации страницы. Поток динамической камеры отображается только условии, что модуль подготовки отчетов, уже не присоединяется к существующему элементу Xamarin.Forms, и условии, что существует экземпляр страницы, готовится к просмотру модулем подготовки отчетов настраиваемые. Страницы затем настраивается путем вызова ряд методов, использующих `MediaCapture` API, чтобы обеспечить потоковую трансляцию с камеры и собирать фотографии, перед добавлением индивидуализированные страницы `Children` коллекции для отображения.

При реализации пользовательского средства визуализации, производный от `PageRenderer` на UWP, `ArrangeOverride` метод также должен быть реализован для упорядочения элементов управления страницы, так как базовый модуль подготовки отчетов не знает, что с ними делать. В противном случае — пустая страница результатов. Таким образом, в этом примере `ArrangeOverride` вызовы методов `Arrange` метод `Page` экземпляра.

> [!NOTE]
> Важно остановить и удалить объекты, которые обеспечивают доступ к камере в приложении универсальной платформы Windows. Невыполнение этого требования может препятствовать с другими приложениями, которые пытаются получить доступ к камеры устройства. Дополнительные сведения см. в разделе [камеры предварительного просмотра](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access).

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательское средство отрисовки для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) страницы, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы. Объект `ContentPage` — это визуальный элемент, который отображает одно представление и занимает большую часть экрана.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererContentPage (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)
