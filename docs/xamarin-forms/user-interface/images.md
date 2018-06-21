---
title: Образы в Xamarin.Forms
description: Образы могут совместно использоваться платформы с помощью Xamarin.Forms, их можно загрузить специально для каждой платформы, или их можно загрузить для отображения.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: b0fd644f1f3b49a949a3a9ba9aca4c0770f17013
ms.sourcegitcommit: c2d1249cb67b877ee0d9cb8d095ec66fd51d8c31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291355"
---
# <a name="images-in-xamarinforms"></a>Образы в Xamarin.Forms

_Образы могут совместно использоваться платформы с помощью Xamarin.Forms, их можно загрузить специально для каждой платформы, или их можно загрузить для отображения._

Образы являются одной из важнейших приложений навигации, удобство использования и фирменной символики. Xamarin.Forms приложения должны иметь возможность совместного использования образов на всех платформах, но также может отображать различные изображения на каждой платформе.

Образы платформ также необходимы для значки и экраны-заставки; они должны быть настроены отдельно для каждой платформы.

В этом документе рассматриваются следующие вопросы:

- [ **Локальные изображения** ](#Local_Images) -отображение изображений в состав приложения, включая разрешение собственного решения как iOS версии высоким Разрешением Сетчатка, Android или UWP изображения.
- [ **Внедренные изображения** ](#Embedded_Images) -отображение изображений внедренных в качестве ресурса сборки.
- [ **Загрузить изображения** ](#Downloading_Images) — Загрузка и отображение изображений.
- [ **Значков и экранов-заставок** ](#Icons_and_splashscreens) -значки конкретную платформу и изображений при запуске.

## <a name="displaying-images"></a>Отображение изображений

Xamarin.Forms использует [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) представление для отображения изображений на странице. Он имеет два важных свойства:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) - [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) Экземпляр файла, Uri или ресурса, который задает изображение для отображения.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) -Как размер изображения в пределах границы отображаются внутри (как для stretch, обрезать или letterbox).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) экземпляры можно получить с помощью статических методов для каждого типа источника изображения.

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) -Требуется имя файла или путь к файлу, которые могут быть разрешены на каждой платформе.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Требуется объект Uri, например.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) — Требуется идентификатор ресурса в файл изображения, внедренной в приложение или проект библиотеки .NET Standard, **действие построения: EmbeddedResource**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) -Требует поток, предоставляющий данные изображения.

[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) Свойство определяет, как изображение будет масштабироваться в соответствии с отображаемой области:

- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.Fill/) -Увеличение изображения, чтобы полностью и точно заполнить область отображения. Это может привести в образе искажен.
- [`AspectFill`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFill/) -Обрезает изображение, чтобы он заполняет область отображения, сохраняя пропорции (т. е. без искажений).
- [`AspectFit`](https://developer.xamarin.com/api/field/Xamarin.Forms.Aspect.AspectFit/) -Letterboxes изображения (при необходимости), чтобы все изображение помещается в области отображения, с помощью пустого пространства, добавляемого в верхней и нижней границ или сторон, в зависимости от того, является ли изображение ширину или высоту.

Изображения можно загрузить из [локальный файл](#Local_Images_in_Xaml), [внедренный ресурс](#embedded_images), или [загрузки](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Локальные изображения

Можно добавлять в каждый проект приложения и ссылка из кода Xamarin.Forms общих файлов изображений. Для использования одного образа для всех приложений *тем же именем файла необходимо использовать на любой платформе*, и должно быть допустимое Android имя ресурса (т. е. допускаются только строчные буквы, цифры, знаки подчеркивания и периода).

- **iOS** — предпочтительный способ управления и поддерживает образы, так как iOS 9 — использовать **наборов изображений каталога активов**, который должен содержать все версии изображения, необходимые для поддержки различных устройств и масштабировать факторы, приложение. Дополнительные сведения см. в разделе [Добавление изображения, задаваемое активов каталога изображения](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -размещать изображения в **ресурсы или drawable** каталог с **действие при построении: AndroidResource**. Версии с высоким и низким Разрешением изображения могут быть также заданы (в с именем **ресурсов** подкаталоги, такие как **drawable ldpi**, **drawable hdpi**и **drawable xhdpi**).
- **Универсальной платформы Windows (UWP)** -размещать изображения в корневом каталоге приложения с **действие при построении: содержимого**.

> [!IMPORTANT]
> До iOS 9 образы обычно были помещены в **ресурсов** папка с **действие при построении: BundleResource**. Тем не менее работа с изображениями в приложения iOS этот метод является устаревшим компанией Apple. Дополнительные сведения см. в разделе [размеры изображения и имена файлов](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Выполнение этих правил для имени файла и размещения предоставляет следующий код XAML для загрузки и отображения изображения на всех платформах:

```xaml
<Image Source="waterfront.jpg" />
```

Эквивалентный код C# выглядит следующим образом:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Следующих снимках экрана отобразится результат отображение локального изображения на каждой платформе:

[![Локальный ImageSource](images-images/local-sml.png "образец приложения, отображение локального изображения")](images-images/local.png#lightbox "образец приложения, отображение локального изображения")

Для большей гибкости `Device.RuntimePlatform` свойство может использоваться для выбора другого файла изображения или путь для некоторых или всех платформ, как показано в следующем примере кода:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Для использования тем же именем файла изображения для всех платформ, имя должно быть допустимым на всех платформах. Android drawables имеют ограничения по именованию – допускаются только строчные буквы, цифры, подчеркивания и период — и для обеспечения совместимости между различными платформами это следует указать на других платформах слишком. Пример имени файла **waterfront.png** выполняет правила, но примеры недопустимые имена файлов включают «вода front.png», «WaterFront.png», «вода front.png» и «wåterfront.png».

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Собственного решения (Сетчатка и высоким Разрешением)

iOS, Android и UWP включают поддержку разрешение другое изображение, где операционная система выбирает соответствующий образ во время выполнения в зависимости от возможностей устройства. Xamarin.Forms использует интерфейсы API для собственных платформ для загружает локальный изображения, чтобы она автоматически поддерживает альтернативные способы их устранения, если файлы с именем и находится в проекте правильно.

Предпочтительный способ управления образами, так как iOS 9 — перетащите изображения для каждого разрешения, необходимые набор изображения каталога соответствующие средства. Дополнительные сведения см. в разделе [Добавление изображения, задаваемое активов каталога изображения](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

До iOS 9 Сетчатка версии образа может быть размещен в **ресурсов** папку - второй и третий раз разрешение с **@2x** или **@3x**суффиксы на основе имени файла перед расширением файла (например) **myimage@2x.png**). Тем не менее работа с изображениями в приложения iOS этот метод является устаревшим компанией Apple. Дополнительные сведения см. в разделе [размеры изображения и имена файлов](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Android альтернативный разрешение изображения должен быть помещен в [специально с именем каталоги](http://developer.android.com/guide/practices/screens_support.html) в проект Android, как показано на следующем снимке экрана:

[![Расположение образа Android различных разрешениях](images-images/xs-highdpisolution-sml.png "расположение Android несколько разрешение изображения")](images-images/xs-highdpisolution.png#lightbox "Android разрешения нескольких изображений")

Имена файлов изображений UWP [может заканчиваться с `.scale-xxx` перед расширением файла](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), где `xxx` масштабирование применяется к активу, например процент **myimage.scale 200.png**. Изображения можно указывать в коде или в XAML без модификатора масштабирования, например просто **myimage.png**. Платформа будет выбирать ближайшего масштаб соответствующего ресурса, в зависимости от текущего отображения точек на ДЮЙМ.

### <a name="additional-controls-that-display-images"></a>Дополнительные элементы управления, отображение изображений

Некоторые элементы управления имеют свойства, которые отображать изображения, такие как:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) -Любой страницы тип, производный от `Page` имеет [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) и [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) свойства, которые можно назначить ссылку на локальный файл. В некоторых случаях, например когда [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) отображает [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), значок будет отображаться, если поддерживается платформой.

  > [!IMPORTANT]
  > На iOS [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) свойство не может быть заполнен из образа в наборе средств каталога изображений. Вместо этого загрузить изображения значков для `Page.Icon` свойство из **ресурсов** папку в проекте iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) — Имеет [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) свойство, которое может быть присвоено ссылку на локальный файл.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) — Имеет [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) получить свойство, которое может быть присвоено изображения из локального файла, внедренного ресурса или URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Внедренные изображения

Внедренные изображения также поставляются вместе с приложением (например, локальный), но вместо копирования образа в каждое приложение структура файла изображения файл внедрен в сборку как ресурс. Распространение образов этот метод хорошо подходит для создания компонентов, как изображения вместе с кодом.

Внедрение изображения в проект, правой кнопкой мыши и выберите изображение/s, который вы хотите добавить новые элементы. По умолчанию изображение будет иметь **действие при построении: нет**; это должен быть установлен в **действие при построении: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Укажите действие при построении: EmbeddedResource")

**Действие при построении** можно просмотреть и изменить в **свойства** окна для файла.

В этом примере — идентификатор ресурса **WorkingWithImages.beach.jpg**.
Интегрированной среды разработки создал эту настройку по умолчанию, объединяя **пространство имен по умолчанию** для этого проекта с именем файла, используя точку (.), между значениями.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Укажите действие при построении: EmbeddedResource")

**Действие построения** можно также просмотреть и изменить в **свойства** pad для файла.
Такая панель показывает **идентификатор ресурса** , используемый для ссылки на ресурс в коде. На снимке экрана ниже **идентификатор ресурса** — **WorkingWithImages.beach.jpg**.
Интегрированной среды разработки создал эту настройку по умолчанию, объединяя **пространство имен по умолчанию** для этого проекта с именем файла, используя точку (.), между значениями.
Этот идентификатор можно изменить в **свойства** планшета, но в этих примерах значение **WorkingWithImages.beach.jpg** будет использоваться.

![](images-images/xs-embeddedproperties.png "Панель свойств EmbeddedResource")

-----

Если поместить внедренные изображения в папки в проекте, имена папок также разделенных точками (.) в его идентификатором ресурса. Перемещение **beach.jpg** изображения в папку с названием **MyImages** приведет к появлению идентификатор ресурса **WorkingWithImages.MyImages.beach.jpg**

Код для загрузки внедренное изображение просто передает **идентификатор ресурса** для [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) метода, как показано ниже:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> Для поддержки отображения внедренные изображения в режиме выпуска на универсальной платформе Windows, необходимо использовать перегруженный `ImageSource.FromResource` , указывающий источник сборки, в которой для поиска изображения.

В настоящее время нет неявного преобразования для идентификаторов ресурсов. Вместо этого необходимо использовать [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) или `new ResourceImageSource()` для загрузки внедренных изображений.

Следующих снимках экрана отобразится результат отображения внедренного изображения на каждой платформе:

[![ResourceImageSource](images-images/resource-sml.png "образец приложения, отображение внедренного изображения")](images-images/resource.png#lightbox "образец приложения, отображение внедренного изображения")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>С помощью XAML

Так как отсутствует встроенный преобразователь типа не из `string` для `ResourceImageSource`, эти типы образы не могут быть загружены изначально XAML. Вместо этого простого пользовательского расширения разметки XAML могут записываться для загрузки изображений с помощью **идентификатор ресурса** указанные в XAML:

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> Для поддержки отображения внедренные изображения в режиме выпуска на универсальной платформе Windows, необходимо использовать перегруженный `ImageSource.FromResource` , указывающий источник сборки, в которой для поиска изображения.

Для использования этого расширения добавить пользовательское `xmlns` в языке XAML, используя правильные значения пространства имен и сборки для проекта. Затем можно создать источник изображения с помощью этого синтаксиса: `{local:ImageResource WorkingWithImages.beach.jpg}`. Ниже приводится полный пример, в XAML.

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshooting-embedded-images"></a>Устранение неполадок внедренные изображения

<a name="Debugging_Embedded_Images" />

#### <a name="debugging-code"></a>Отладка кода

Поскольку иногда бывает трудно понять, почему не загружаемого ресурса конкретный образ, следующий код отладки можно добавить временно в приложение, чтобы гарантировать, что ресурсы настроены правильно. Будут выведены все известные ресурсы, встроенные в данной сборке для <span class="UIItem">консоли</span> для отладки проблемы загрузки ресурсов.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Изображения, внедренные в других проектах

По умолчанию `ImageSource.FromResource` метод выполняет только для изображений в той же сборке, как код, вызывающий `ImageSource.FromResource` метод. С помощью отладки кода выше можно определить, какие сборки содержат конкретный ресурс, изменив `typeof()` инструкции `Type` известно, в каждой сборке.

Тем не менее, заданное исходной сборки, поиск внедренного изображения в качестве аргумента для `ImageSource.FromResource` метод:

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

<a name="Downloading_Images" />

## <a name="downloading-images"></a>Загрузка изображений

Изображения можно автоматически загружаться для отображения, как показано в следующем коде XAML.

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

Эквивалентный код C# выглядит следующим образом:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

[ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) Метод требует `Uri` объекта и возвращает новый [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) , считывающий данные из `Uri`.

Имеется также неявное преобразование строк URI, поэтому этот пример также будет работать:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Следующих снимках экрана отобразится результат отображение удаленного изображения на каждой платформе:

[![Загрузить ImageSource](images-images/download-sml.png "образец приложения, отображение загруженный образ")](images-images/download.png#lightbox "образец приложения, отображение загруженный образ")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>Кэширование загруженный образ

Объект [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) также поддерживает кэширование загруженных образов, настроить с помощью следующих свойств:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -Ли кэширование включено (`true` по умолчанию).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` , определяющий, как долго изображение будет сохранен локально.

Кэширование включено по умолчанию и сохраняется локально на 24 часа. Чтобы отключить кэширование для конкретного изображения, создать экземпляр источник изображения следующим образом:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

Чтобы задать точку конкретного кэша (например, 5 дней) создать экземпляр источник изображения следующим образом:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Встроенного кэширования позволяет очень легко для поддержки сценариев, таких как прокрутка списков изображений, где можно установить (или привязки) изображение в каждой ячейке и позволить встроенного кэша заниматься повторной загрузки изображения, когда ячейка вернуться в области просмотра.

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Значков и экранов-заставок

Хотя не относятся к [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) представление, приложения значков и экранов-заставок также являются важное применение изображений в проектах Xamarin.Forms.

Настройка значков и экранов-заставок для Xamarin.Forms приложений осуществляется в каждом из проектов приложений. Это означает, что создание правильно размера изображений для iOS, Android и UWP. Эти изображения следует с именем и находится в соответствии с требованиями каждой платформы.

## <a name="icons"></a>Значки

В разделе [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md), [Google иллюстрациях](http://developer.android.com/design/style/iconography.html), и [рекомендации по ресурсы плиток и значков](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) Дополнительные сведения о создании этих ресурсов приложения.

## <a name="splashscreens"></a>Заставки

Только для iOS и приложения UWP, требующие экран-заставка (также называемые образ загрузки экрана или значение по умолчанию).

Обратитесь к документации для [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md) и [экранов-заставок](/windows/uwp/launch-resume/splash-screens/) в центре разработчиков Windows.

## <a name="summary"></a>Сводка

Xamarin.Forms предоставляет ряд способов включения изображений в кросс платформенные приложения, включая тот же образ для использования на платформах или платформой изображения должен быть задан. Также автоматически кэшируются загруженными изображениями, автоматизация чаще кодирования.

Изображения значков, так и экран-заставка приложения, настройки и настроен для приложений, не являющихся Xamarin.Forms - те же правила, используемые для конкретной платформы приложений.

## <a name="related-links"></a>Связанные ссылки

- [WorkingWithImages (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md)
- [Android иллюстрациях](http://developer.android.com/design/style/iconography.html)
- [Рекомендации по ресурсы плиток и значков](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
