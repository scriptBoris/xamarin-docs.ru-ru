---
title: Реализация представления
description: В этой статье объясняется, как создать пользовательское средство отрисовки для пользовательского элемента управления Xamarin.Forms, который используется для отображения в предварительной версии видеопоток с камеры устройства.
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994706"
---
# <a name="implementing-a-view"></a>Реализация представления

_Элементы управления Xamarin.Forms настраиваемый пользовательский интерфейс должен быть производным от класса представления, который используется для размещения макеты и элементы управления на экране. В этой статье показано, как создать пользовательское средство отрисовки для пользовательского элемента управления Xamarin.Forms, который используется для отображения в предварительной версии видеопоток с камеры устройства._

Каждое представление Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `View` ](xref:Xamarin.Forms.View) отображается в приложении Xamarin.Forms в iOS, `ViewRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `UIView` элемента управления. На платформе Android `ViewRenderer` класс создает экземпляр собственного `View` элемента управления. В универсальной платформы Windows (UWP), `ViewRenderer` класс создает экземпляр собственного `FrameworkElement` элемента управления. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `View` ](xref:Xamarin.Forms.View) и соответствующие собственные элементы управления, которые реализуют его:

![](view-images/view-classes.png "Отношение между классом представления и его реализации собственных классов")

Процесс подготовки отчета можно использовать для реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `View` ](xref:Xamarin.Forms.View) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Control) пользовательского элемента управления Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Control) пользовательского элемента управления с Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательское средство отрисовки для элемента управления на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации `CameraPreview` модуль подготовки отчетов, отображаются в предварительной версии видеопоток с камеры устройства. Коснувшись поток видео будет остановите и запустите ее.

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>Создание пользовательского элемента управления

Пользовательский элемент управления, которые могут создаваться путем создания подклассов [ `View` ](xref:Xamarin.Forms.View) класса, как показано в следующем примере кода:

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

`CameraPreview` Пользовательский элемент управления создается в проекта переносимой библиотеки классов (PCL) и определяет API для элемента управления. Пользовательский элемент управления предоставляет `Camera` свойство, используемое для управления ли поток видео должно быть взяты из передней или задней камеры на устройстве. Если значение не указано для `Camera` свойства при создании элемента управления, по умолчанию используется для указания задней камеры.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Использование пользовательского элемента управления

`CameraPreview` Пользовательского элемента управления можно ссылаться в XAML в проект переносимой библиотеки Классов, объявление пространства имен для его расположения и используя префикс пространства имен в элементе пользовательского элемента управления. В следующем примере кода показано как `CameraPreview` пользовательский элемент управления может быть поглощен страницу XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны соответствовать данные пользовательского элемента управления. После объявления пространства имен, префикс используется для ссылки на пользовательский элемент управления.

В следующем примере кода показано как `CameraPreview` пользовательский элемент управления может быть поглощен страницы C#:

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

Экземпляр `CameraPreview` пользовательский элемент управления будет использоваться для отображения поток предварительного просмотра видео с камеры устройства. Помимо при необходимости указав значение для `Camera` свойства настройки элемента управления будет осуществляться в пользовательское средство отрисовки.

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для создания платформы камерой для предварительной версии.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `ViewRenderer<T1,T2>` класс, который выполняет визуализацию пользовательского элемента управления. Первый аргумент типа должен быть пользовательского элемента управления, в данном случае это модуль подготовки отчетов `CameraPreview`. Второй аргумент типа должен быть собственный элемент управления, который будет реализован пользовательский элемент управления.
1. Переопределить `OnElementChanged` метода, который отображает пользовательскую логику управления и записи для его настройки. Этот метод вызывается, когда создается соответствующий элемент управления Xamarin.Forms.
1. Добавление `ExportRenderer` атрибут класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для подготовки к просмотру пользовательский элемент управления Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Для большинства элементов Xamarin.Forms является необязательным для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса элемента управления. Однако пользовательские модули подготовки отчетов необходимы в каждом проекте платформы при отрисовке [представление](xref:Xamarin.Forms.View) элемент.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](view-images/solution-structure.png "Обязанности проекта CameraPreview пользовательский модуль подготовки отчетов")

`CameraPreview` Пользовательский элемент управления отрисовывается классами платформы модуля подготовки отчетов, которые являются производными от `ViewRenderer` класс для каждой платформы. Это приводит к каждой `CameraPreview` пользовательского элемента управления, отображаемого в элементах управления платформы, как показано на следующем снимке экрана:

![](view-images/screenshots.png "CameraPreview на каждой платформе")

`ViewRenderer` Предоставляет `OnElementChanged` метод, который вызывается при создании пользовательского элемента управления Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, содержащий `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В приложении-примере `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `CameraPreview` экземпляра.

Переопределенная версия `OnElementChanged` метод в каждом классе платформы модуля подготовки отчетов — место для выполнения при создании собственного элемента управления и настройки. `SetNativeControl` Метод должен использоваться для создания экземпляра собственного элемента управления, и этот метод также будет назначить ссылку на элемент управления `Control` свойство. Кроме того, можно получить ссылку на элемент управления Xamarin.Forms, который готовится к просмотру с помощью `Element` свойство.

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

Собственный элемент управления должен создаваться только один раз, когда свойству `Control` задано значение `null`. Элемент управления следует настраивать и подписывать на обработчики событий только при присоединении пользовательского отрисовщика к новому элементу Xamarin.Forms. Аналогичным образом все обработчики событий, которые были созданы подписки должно быть отменена при изменении элемента, к которой подключен модуль подготовки отчетов. Этот подход поможет для создания высокопроизводительных пользовательского средства визуализации, который не страдает от утечек памяти.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя пользовательского элемента управления Xamarin.Forms визуализируемого типа и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются реализацию каждого класса пользовательского средства визуализации для конкретной платформы.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

При условии, что `Control` свойство `null`, `SetNativeControl` метод вызывается для создания нового `UICameraPreview` управления и присваивает ей ссылку на него в `Control` свойство. `UICameraPreview` Элемент управления является платформой пользовательского элемента управления, который использует `AVCapture` API-интерфейсы для предоставления поток предварительного просмотра из камеры. Он предоставляет `Tapped` событие, которое обрабатывается `OnCameraPreviewTapped` метод для остановки и запуска предварительного просмотра видеоматериала его шифрованию. `Tapped` Подписана событий при пользовательское средство отрисовки к новому элементу Xamarin.Forms и отписались от только при присоединении элемента модуля подготовки отчетов к изменениям.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

При условии, что `Control` свойство `null`, `SetNativeControl` метод вызывается для создания нового `CameraPreview` управления и назначить ссылку на него в `Control` свойство. `CameraPreview` Элемент управления является платформой пользовательского элемента управления, который использует `Camera` API, чтобы обеспечить поток предварительного просмотра из камеры. `CameraPreview` Элемент управления затем настроен, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Эта конфигурация требует создания нового собственный `Camera` для доступа к конкретной аппаратной камеры и регистрации обработчика событий для обработки `Click` событий. В свою очередь этот обработчик будет останавливать и запускать предварительного просмотра видеоматериала, при ее нажатии. `Click` Событие не отменена элементу Xamarin.Forms модуль подготовки отчетов, подключенного к изменениям.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского средства визуализации на UWP

В следующем примере кода показано пользовательское средство отрисовки для универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

При условии, что `Control` свойство `null`, новый `CaptureElement` создается и `SetupCamera` вызывается метод, который использует `MediaCapture` API, чтобы обеспечить поток предварительного просмотра из камеры. `SetNativeControl` Присвоить ссылку затем вызывается метод `CaptureElement` экземпляр `Control` свойство. `CaptureElement` Управления предоставляет `Tapped` событие, которое обрабатывается `OnCameraPreviewTapped` метод для остановки и запуска предварительного просмотра видеоматериала его шифрованию. `Tapped` Подписана событий при пользовательское средство отрисовки к новому элементу Xamarin.Forms и отписались от только при присоединении элемента модуля подготовки отчетов к изменениям.

> [!NOTE]
> Важно остановить и удалить объекты, которые обеспечивают доступ к камере в приложении универсальной платформы Windows. Невыполнение этого требования может препятствовать с другими приложениями, которые пытаются получить доступ к камеры устройства. Дополнительные сведения см. в разделе [камеры предварительного просмотра](/windows/uwp/audio-video-camera/simple-camera-preview-access/).

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательское средство отрисовки для пользовательского элемента управления Xamarin.Forms, который используется для отображения в предварительной версии видеопоток с камеры устройства. Элементы управления Xamarin.Forms настраиваемый пользовательский интерфейс должен быть производным от [ `View` ](xref:Xamarin.Forms.View) класс, который используется для размещения макеты и элементы управления на экране.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererView (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)
