---
title: Разрешение зависимостей в Xamarin.Forms
description: В этой статье объясняется, как вставить метод разрешения зависимостей в Xamarin.Forms, таким образом, чтобы контейнера внедрения зависимостей приложения имеет контроль над конструкции и временем существования пользовательские модули подготовки отчетов, эффекты и DependencyService реализации.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/23/2018
ms.openlocfilehash: 8235b6fb5fda2a414cf192dd66007d1d02f232aa
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242385"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Разрешение зависимостей в Xamarin.Forms

_В этой статье объясняется, как вставить метод разрешения зависимостей в Xamarin.Forms, таким образом, чтобы контейнера внедрения зависимостей приложения имеет контроль над конструкции и временем существования пользовательские модули подготовки отчетов, эффекты и DependencyService реализации. В примерах кода взяты из [разрешение зависимостей](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/) образца._

Контейнер внедрения зависимостей в контексте приложения Xamarin.Forms, использующая шаблон Model-View-ViewModel (MVVM), можно использовать для регистрации и разрешения на просмотр моделей, а также для регистрации служб и вставляя их в модели представления. Во время создания модели представления контейнер внедряет зависимые компоненты, которые необходимы. Если не были созданы эти зависимости, создает контейнер и сначала устраняет зависимости. Дополнительные сведения о внедрения зависимостей, включая примеры внедрение зависимостей в модели представления, см. в разделе [внедрения зависимостей](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Контроль над созданием и временем существования типов в проектах платформы обычно осуществляется Xamarin.Forms, который использует `Activator.CreateInstance` метод, чтобы создать экземпляры пользовательских модулей подготовки отчетов, эффекты, и [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) в реализации. К сожалению это ограничивает контроль разработчика над созданием и временем существования этих типов, а также возможность внедрения зависимостей в них. Тем не менее это поведение можно изменить, внедряя метод разрешения зависимостей в Xamarin.Forms, который определяет, как будут созданы типы — или контейнера внедрения зависимостей приложения, по Xamarin.Forms.

> [!NOTE]
> Необязательно для вставки метода разрешения зависимостей на Xamarin.Forms. Xamarin.Forms будет продолжать создавать и управлять временем существования типов в проектах платформы, если метод разрешения зависимостей не вставлен.

## <a name="injecting-a-dependency-resolution-method"></a>Добавление метода разрешения зависимостей

[ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver) Предоставляет возможность внедрить метод разрешения зависимостей в Xamarin.Forms, используя один из [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) методы. Затем когда Xamarin.Forms требуется экземпляр определенного типа, этот метод разрешения зависимостей присваивается возможность предоставить экземпляр. Если этот метод разрешения зависимостей возвращает `null` для запрошенного типа, возвращается Xamarin.Forms к попытке создать тип экземпляр с помощью `Activator.CreateInstance` метод.

В следующем примере показано, как установить метод разрешения зависимостей с [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) метод:

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

В этом примере метод разрешения зависимостей имеет значение лямбда-выражения и использует Autofac контейнера внедрения зависимостей, чтобы разрешить все типы, которые были зарегистрированы с контейнером. В противном случае `null` будет возвращаться, что приведет к попытке разрешения типа Xamarin.Forms.

> [!NOTE]
> API, используемые контейнер внедрения зависимостей относится к контейнеру. В примерах кода в этой статье использовать Autofac в качестве контейнера внедрения зависимостей, который предоставляет `IContainer` и `ContainerBuilder` типов. Контейнеры для введения зависимостей альтернативные столь же может использоваться, но используются разные интерфейсы API, чем представлены здесь.

Обратите внимание, что не требуется, чтобы установить метод разрешения зависимостей во время запуска приложения. Его можно установить в любое время. Единственное ограничение — это что Xamarin.Forms необходимо знать о метод разрешения зависимостей, время, когда приложение пытается использовать типы, хранящиеся в контейнер внедрения зависимостей. Таким образом Если в контейнер внедрения зависимостей, который требует наличия приложения во время запуска службы, этот метод разрешения зависимостей будет устанавливаться на ранних этапах жизненного цикла приложения. Аналогичным образом Если контейнер внедрения зависимостей управляет созданием и временем существования конкретного [ `Effect` ](xref:Xamarin.Forms.Effect), необходимо знать о методе разрешения зависимостей, перед попыткой создания представления Xamarin.Forms, использует его `Effect`.

> [!WARNING]
> Регистрация и разрешение типов с помощью контейнера внедрения зависимостей имеет к снижению производительности из-за контейнера использование отражения для создания каждого типа, особенно в том случае, если зависимости воссоздаются для каждого Навигация по страницам в приложении. Если существует много или всех зависимостей, затраты на создание может значительно возрасти.

## <a name="registering-types"></a>Регистрация типов

Типы должны быть зарегистрированы с помощью контейнера внедрения зависимостей, перед разрешением их через метод разрешения зависимостей. В следующем примере кода показаны методы регистрации, что пример приложения предоставляет в `App` класс для контейнер Autofac:

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Когда приложение использует метод разрешение зависимостей для разрешения типов из контейнера, тип регистрации обычно выполняются из проектов платформы. Это позволяет платформы проектов зарегистрировать типы для пользовательских модулей подготовки отчетов, эффекты, и [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) реализаций.

После регистрации типа в проекте платформы `IContainer` объекта должны быть построены, который выполняется путем вызова метода `BuildContainer` метод. Этот метод вызывает метод в Autofac `Build` метод `ContainerBuilder` экземпляр, который создает новый контейнер внедрения зависимостей, содержащий регистрации, которые были внесены.

В последующих разделах `Logger` класс, который реализует `ILogger` интерфейсом, внедряется в конструкторы класса. `Logger` Функциональность реализует простую регистрацию класса с помощью `Debug.WriteLine` метод и используется для демонстрации того, как службы могут внедряться в пользовательские отрисовщики, эффекты, и [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) реализаций.

### <a name="registering-custom-renderers"></a>Регистрация пользовательских модулей подготовки отчетов

Образец приложения включает страницы, который играет веб-видео, источником которого XAML показан в следующем примере:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

`VideoPlayer` Представление реализуется на каждой платформе, `VideoPlayerRenderer` класса, который предоставляет функциональные возможности для воспроизведения видео. Дополнительные сведения об этих классах пользовательское средство отрисовки см. в разделе [реализация видеопроигрывателя](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

В iOS и универсальной Windows платформы (UWP) `VideoPlayerRenderer` классы имеют следующий конструктор, который требует `ILogger` аргумент:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

На всех трех платформах, осуществляется регистрация типа с помощью контейнера внедрения зависимостей `RegisterTypes` метод, который вызывается до загрузки приложения с платформы `LoadApplication(new App())` метод. В следующем примере показан `RegisterTypes` метод на платформе iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

В этом примере `Logger` конкретный тип зарегистрирован с помощью сопоставления с его тип интерфейса и `VideoPlayerRenderer` напрямую без сопоставления интерфейс зарегистрирован тип. Когда пользователь переходит на страницу, содержащую `VideoPlayer` представление, этот метод разрешения зависимостей будет вызываться для разрешения `VideoPlayerRenderer` типа из контейнера внедрения зависимостей, которая также устранить и внедрить `Logger` введите в `VideoPlayerRenderer` конструктор.

`VideoPlayerRenderer` Конструктор на платформе Android немного сложнее, так как требует `Context` аргумент в дополнение к `ILogger` аргумент:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

В следующем примере показан `RegisterTypes` метод на платформе Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

В этом примере `App.RegisterTypeWithParameters` регистры метод `VideoPlayerRenderer` с помощью контейнера внедрения зависимостей. Метод регистрации гарантирует, что `MainActivity` экземпляр внедряются как `Context` аргумента и создает `Logger` тип внедряются как `ILogger` аргумент.

### <a name="registering-effects"></a>Регистрация эффекты

Образец приложения включает страницы, использующей отслеживания эффект сенсорного ввода для перетаскивания [ `BoxView` ](xref:Xamarin.Forms.BoxView) экземпляры по странице. [ `Effect` ](xref:Xamarin.Forms.Effect) Добавляется `BoxView` следующим образом:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

`TouchEffect` Класс является [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) , реализованный на каждой платформе, `TouchEffect` класс, который имеет `PlatformEffect`. Платформа `TouchEffect` класс предоставляет функциональные возможности для перетаскивания `BoxView` вокруг страницы. Дополнительные сведения об этих классах эффект см. в разделе [вызов события из эффекты](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

На всех трех платформах `TouchEffect` класс имеет следующий конструктор, который требует `ILogger` аргумент:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

На всех трех платформах, осуществляется регистрация типа с помощью контейнера внедрения зависимостей `RegisterTypes` метод, который вызывается до загрузки приложения с платформы `LoadApplication(new App())` метод. В следующем примере показан `RegisterTypes` метод на платформе Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

В этом примере `Logger` конкретный тип зарегистрирован с помощью сопоставления с его тип интерфейса и `TouchEffect` напрямую без сопоставления интерфейс зарегистрирован тип. Когда пользователь переходит на страницу, содержащую [ `BoxView` ](xref:Xamarin.Forms.BoxView) экземпляра, включающего `TouchEffect` подключенные к ней, этот метод разрешения зависимостей будет вызываться для решения на платформу `TouchEffect` тип из зависимостей Внедрение контейнера, который также устранить и внедрить `Logger` введите в `TouchEffect` конструктор.

### <a name="registering-dependencyservice-implementations"></a>Регистрация реализаций DependencyService

Образец приложения включает страницы, использующей [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) реализаций на каждой платформе, чтобы разрешить пользователю выбрать снимок из библиотеки рисунков устройства. `IPhotoPicker` Интерфейс определяет функциональность, которая реализуется `DependencyService` реализации и показано в следующем примере:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

В каждом проекте платформы `PhotoPicker` класс реализует `IPhotoPicker` интерфейса с помощью API платформы. Дополнительные сведения об этих службах зависимостей см. в разделе [комплектации фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

На всех трех платформах `PhotoPicker` класс имеет следующий конструктор, который требует `ILogger` аргумент:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

На всех трех платформах, осуществляется регистрация типа с помощью контейнера внедрения зависимостей `RegisterTypes` метод, который вызывается до загрузки приложения с платформы `LoadApplication(new App())` метод. В следующем примере показан `RegisterTypes` метод для универсальной платформы Windows:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

В этом примере `Logger` конкретный тип зарегистрирован с помощью сопоставления с его тип интерфейса и `PhotoPicker` типа также регистрируется через сопоставление интерфейса. Когда пользователь переходит на страницу выбора фотографии и выбирает фотографию, `OnSelectPhotoButtonClicked` выполняется обработчик:

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

Когда [ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) вызывается метод, вызывается метод разрешения зависимостей для разрешения `PhotoPicker` типа из контейнера внедрения зависимостей, которая также устранить и внедрить `Logger` типа в `PhotoPicker` конструктора.

> [!NOTE]
> [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) Метод должен использоваться при разрешении типа из контейнера внедрения зависимостей приложения с помощью [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

## <a name="related-links"></a>Связанные ссылки

- [Разрешение зависимостей (пример)](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/)
- [Внедрение зависимостей](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Реализация видеопроигрывателя](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Вызов событий из эффектов](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Выбрав фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
