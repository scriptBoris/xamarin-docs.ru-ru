---
title: Образы в Xamarin.Forms
description: Образы могут совместно использоваться платформах с помощью Xamarin.Forms, могут быть загружены специально для каждой платформы, или их можно загрузить для отображения.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: ea7ec0d297e144966f8ff7545e6b12160f66c9b0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935544"
---
# <a name="images-in-xamarinforms"></a>Образы в Xamarin.Forms

_Образы могут совместно использоваться платформах с помощью Xamarin.Forms, могут быть загружены специально для каждой платформы, или их можно загрузить для отображения._

Образы являются одной из важнейших приложений навигации, удобство использования и фирменной символики. Приложения Xamarin.Forms должны иметь возможность совместного использования образов на всех платформах, но также может отображать различные изображения на каждой платформе.

Образы платформы также требуются для значки и экраны-заставки; они должны быть настроены отдельно для каждой платформы.

В этом документе рассматриваются следующие темы:

- [ **Локальным образам** ](#Local_Images) -отображение изображений в состав приложения, включая разрешение собственного разрешения, например iOS Retina, Android и универсальной платформы Windows версий высоким Разрешением изображения.
- [ **Внедренные изображения** ](#Embedded_Images) -отображение изображений внедрены в качестве ресурса сборки.
- [ **Загрузить изображения** ](#Downloading_Images) — Загрузка и отображение изображений.
- [ **Значки и экраны-заставки** ](#Icons_and_splashscreens) -платформой значков и изображений при запуске.

## <a name="displaying-images"></a>Отображение изображений

Xamarin.Forms использует [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) представление для отображения изображений на странице. Он имеет два важных свойства:

- [`Source`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) Экземпляра, файл, Uri или ресурса, который задает изображения для отображения.
- [`Aspect`](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) — Как для изменения размера изображения в пределах границ, в котором он отображается в (следует ли stretch, обрезка или letterbox).

[`ImageSource`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) экземпляры можно получить с помощью статических методов для каждого типа источника изображения:

- [`FromFile`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromFile/p/System.String/) — Требует имя файла или путь к файлу, который может быть разрешен на каждой платформе.
- [`FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) -Требуется объект Uri, например.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) -Требуется идентификатор ресурса для файла изображения, внедренного в приложение или проект библиотеки .NET Standard, с помощью **построения действие: EmbeddedResource**.
- [`FromStream`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromStream/p/System.Func%7BSystem.IO.Stream%7D/) — Требует поток, который предоставляет данные изображения.

[ `Aspect` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Aspect/) Свойство определяет, как изображение будет масштабироваться в соответствии с отображаемой области:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Растягивает изображения полностью и точности заполняла отображаемую область. Это может привести в образе искажен.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Обрезает изображение, чтобы он заполняет область отображения сохраняя пропорции (т. е. без искажений).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -Горизонтальные рамки образа (при необходимости) так, чтобы все изображение поместилось в области отображения, с помощью пустого пространства, добавляемого в верх или низ или сторон в зависимости от того, является ли изображение шириной или высотой.

Образы можно загружать с [локальный файл](#Local_Images_in_Xaml), [внедренный ресурс](#embedded_images), или [загрузки](#Downloading_Images).

<a name="Local_Images" />

## <a name="local-images"></a>Локальные изображения

Можно добавить в каждый проект приложения и ссылок в коде Xamarin.Forms общих файлов изображений. Для использования одного образа для всех приложений, *тем же именем файла необходимо использовать на всех платформах*, и должно быть допустимое Android имя ресурса (т. е. допускаются только строчные буквы, цифры, подчеркивания и период).

- **iOS** — предпочтительный способ управления и поддерживает образы, так как iOS 9 заключается в использовании **наборы изображений каталога активов**, который должен содержать все версии изображения, которые необходимы для поддержки различных устройств и коэффициенты для масштабирования приложение. Дополнительные сведения см. в разделе [Добавление изображения в ресурс каталога образа группу](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -размещать изображения в **ресурсы/drawable** каталог с **действие при построении: AndroidResource**. Версии с высоким и низким Разрешением изображения могут быть также заданы (в элементе управления **ресурсы** подкаталоги, такие как **drawable ldpi**, **drawable hdpi**и **drawable xhdpi**).
- **Универсальной платформы Windows (UWP)** -размещать изображения в корневом каталоге приложения с **действие при построении: содержимого**.

> [!IMPORTANT]
> До iOS 9, образы обычно были помещены в **ресурсы** папка с **действие при построении: BundleResource**. Тем не менее этот метод работы с изображениями в приложение iOS является устаревшим компанией Apple. Дополнительные сведения см. в разделе [размеры изображений и имена файлов](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Выполнение этих правил именования файлов и размещения предоставляет следующий XAML для загрузки и отображения изображения на всех платформах:

```xaml
<Image Source="waterfront.jpg" />
```

Эквивалентный код на C# выглядит следующим образом:

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

На следующих снимках экрана показано результат отображения локальный образ на каждой платформе:

[![Локальный ImageSource](images-images/local-sml.png "пример приложения, отображающего локальный образ")](images-images/local.png#lightbox "пример приложения, отображающего локальный образ")

Для большей гибкости `Device.RuntimePlatform` свойство может использоваться для выбора другой файл изображения или путь для некоторых или всех платформ, как показано в следующем примере кода:

```csharp
image.Source = Device.RuntimePlatform == Device.Android ? ImageSource.FromFile("waterfront.jpg") : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Чтобы использовать это имя образа на всех платформах имя должно быть допустимым на всех платформах. Android прорисовываемых ресурсов имеют ограничения по именованию – допускаются только строчные буквы, цифры, подчеркивания и период — и для кросс платформенной совместимости это необходимо выполнить на всех остальных платформах слишком. Пример имени файла **waterfront.png** выполняет правила, но примеры недопустимые имена файлов включают «воды front.png», «WaterFront.png», «вода front.png» и «wåterfront.png».

<a name="Native_Resolutions" />

### <a name="native-resolutions-retina-and-high-dpi"></a>Собственного решения (Retina и высокого DPI)

для Android, iOS и универсальной платформы Windows включают поддержку разрешение другой образ, где операционная система выбирает подходящий образ во время выполнения в зависимости от возможностей устройства. Xamarin.Forms использует интерфейсы API собственных платформ для загрузки локальных образов, поэтому он автоматически поддерживает альтернативные способы их устранения, если файлы правильно с именем и находится в проекте.

Предпочтительный способ управления образами, поскольку iOS 9, перетащите изображения для каждого разрешения, требуется набор изображений каталога соответствующие средства. Дополнительные сведения см. в разделе [Добавление изображения в ресурс каталога образа группу](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Retina версии образа до iOS 9, можно поместить в **ресурсы** папка — второй и третий раз разрешения с помощью **@2x** или **@3x**суффиксы в имени файла перед расширением (например) **myimage@2x.png**). Тем не менее этот метод работы с изображениями в приложение iOS является устаревшим компанией Apple. Дополнительные сведения см. в разделе [размеры изображений и имена файлов](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Образы Android альтернативное решение должно находиться в [специально с именем каталоги](http://developer.android.com/guide/practices/screens_support.html) в проекте Android, как показано на следующем снимке экрана:

[![Расположение образа Android различных разрешениях](images-images/xs-highdpisolution-sml.png "расположение образа Android различных разрешениях")](images-images/xs-highdpisolution.png#lightbox "расположение Android различных разрешениях образа")

Имен файлов изображений UWP [можно дополнять суффиксом `.scale-xxx` перед расширением](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), где `xxx` процент масштабирования к ресурсу, например применен **myimage.scale 200.png**. Образы, затем можно ссылаться в коде или XAML без модификатора масштабирования, например сразу же **myimage.png**. Платформа выберет ближайшего масштаб соответствующего ресурса, исходя из текущего отображения точек на ДЮЙМ.

### <a name="additional-controls-that-display-images"></a>Дополнительные элементы управления, отображение изображений

Некоторые элементы управления имеют свойства, которые отображают изображения, такие как:

- [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) — Любой странице тип, производный от `Page` имеет [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) и [ `BackgroundImage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.BackgroundImage/) свойства, которые можно назначить ссылку на локальный файл. При определенных обстоятельствах, например, когда [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) отображает [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), отображается значок, если поддерживается платформой.

  > [!IMPORTANT]
  > В iOS [ `Page.Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) свойство не может быть заполнен из образа в наборе средств каталога изображений. Вместо этого загрузки изображений значков для `Page.Icon` свойства из **ресурсы** папки в проекте iOS.

- [`ToolbarItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) — Имеет [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ToolbarItem.Icon/) свойство, которое можно присвоить ссылку на локальный файл.
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) — Имеет [ `ImageSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ImageCell.ImageSource/) свойство, которое может быть присвоено изображения, полученные из локального файла, внедренного ресурса или URI.

<a name="embedded_images" />

## <a name="embedded-images"></a>Внедренные изображения

Внедренные изображения также поставляются с приложением (например, локальных образов), но вместо копирования образа в структуре файлов каждого приложения изображение файл внедряется в сборку как ресурс. Распространение образов этот метод подходит для создания компонентов, как изображение входит в состав код.

Внедрение изображения в проекте, щелкните правой кнопкой для добавления новых элементов и выберите образ/s, который вы хотите добавить. По умолчанию в изображении будут **действие при построении: нет**; это имя должно быть присвоено **действие при построении: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](images-images/vs-buildaction.png "Задайте действие сборки: EmbeddedResource")

**Действие при построении** можно просмотреть и изменить в **свойства** окна для файла.

В этом примере идентификатор ресурса имеет **WorkingWithImages.beach.jpg**.
Интегрированная среда разработки создала это значение по умолчанию путем объединения **пространство имен по умолчанию** для этого проекта с именем файла, используя точку (.) между значениями.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![](images-images/xs-buildaction.png "Задайте действие сборки: EmbeddedResource")

**Действие при сборке** можно также просмотреть и изменить в **свойства** панели для файла.
Такая панель показывает **идентификатор ресурса** , используемый для ссылки на ресурс в коде. На снимке экрана ниже **идентификатор ресурса** — **WorkingWithImages.beach.jpg**.
Интегрированная среда разработки создала это значение по умолчанию путем объединения **пространство имен по умолчанию** для этого проекта с именем файла, используя точку (.) между значениями.
Этот идентификатор можно изменить в **свойства** панель, но в этих примерах значение **WorkingWithImages.beach.jpg** будет использоваться.

![](images-images/xs-embeddedproperties.png "EmbeddedResource панели свойств")

-----

Если поместить внедренные изображения в папки в проекте, имена папок также разделенных точками (.) в идентификатор ресурса. Перемещение **beach.jpg** образ в папку с именем **MyImages** приведет к идентификатор ресурса **WorkingWithImages.MyImages.beach.jpg**

Код для загрузки внедренное изображение просто передает **идентификатор ресурса** для [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) метод, как показано ниже:

```csharp
var embeddedImage = new Image { Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(EmbeddedImages).GetTypeInfo().Assembly) };
```

> [!NOTE]
> Для поддержки отображения внедренные изображения в режиме выпуска на универсальной платформе Windows, бывает необходимо использовать перегрузку `ImageSource.FromResource` , задающий сборку источника, в котором следует искать изображение.

В настоящее время отсутствует неявное преобразование для идентификаторов ресурсов. Вместо этого необходимо использовать [ `ImageSource.FromResource` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromResource/p/System.String/) или `new ResourceImageSource()` для загрузки внедренных изображений.

На следующих снимках экрана показано результат отображения внедренного изображения на каждой платформе:

[![ResourceImageSource](images-images/resource-sml.png "пример приложения, отображающего внедренного изображения")](images-images/resource.png#lightbox "пример приложения, отображающего внедренное изображение")

<a name="Embedded_Images_in_Xaml" />

### <a name="using-xaml"></a>С помощью XAML

Так как отсутствует встроенный преобразователь типа не из `string` для `ResourceImageSource`, эти типы образов может быть загружен в собственном коде XAML. Вместо этого простого пользовательского расширения разметки XAML могут записываться для загрузки изображений с помощью **идентификатор ресурса** указан в XAML:

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
> Для поддержки отображения внедренные изображения в режиме выпуска на универсальной платформе Windows, бывает необходимо использовать перегрузку `ImageSource.FromResource` , задающий сборку источника, в котором следует искать изображение.

Для использования этого расширения добавить пользовательское `xmlns` для XAML, используя правильные значения пространства имен и сборки для проекта. Затем можно задать источник изображения с помощью этого синтаксиса: `{local:ImageResource WorkingWithImages.beach.jpg}`. Ниже приведен полный пример XAML:

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

Так как иногда сложно понять, почему не загружаемых ресурсов определенного образа, следующий код отладки можно временно добавляется приложения чтобы убедиться, что ресурсы настроены правильно. Будут выведены все известные ресурсов, внедренных в заданной сборке для <span class="UIItem">консоли</span> для отладки проблем загрузки ресурсов.

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

По умолчанию `ImageSource.FromResource` метод ищет только для изображений в той же сборке, что и у вызывающего кода `ImageSource.FromResource` метод. Используя код отладки выше можно определить, какие сборки содержат определенного ресурса, изменив `typeof()` инструкцию, чтобы `Type` известно, в каждой сборке.

Тем не менее, можно указать исходной сборки, поиск внедренного изображения в качестве аргумента `ImageSource.FromResource` метод:

```csharp
var imageSource = ImageSource.FromResource("filename.png", typeof(MyClass).GetTypeInfo().Assembly);
```

<a name="Downloading_Images" />

## <a name="downloading-images"></a>Загрузка изображений

Образы могут автоматически загружаться для отображения, как показано в следующем XAML:

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

Эквивалентный код на C# выглядит следующим образом:

```csharp
var webImage = new Image { Source = ImageSource.FromUri(new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")) };
```

[ `ImageSource.FromUri` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) Метод требует `Uri` объекта и возвращает новый [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) , считывающий данные из `Uri`.

Имеется также неявное преобразование для строки URI, поэтому следующий пример также будет работать:

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

На следующих снимках экрана показано результат отображения удаленного изображения на каждой платформе:

[![Скачать ImageSource](images-images/download-sml.png "пример приложения, отображающего загруженный образ")](images-images/download.png#lightbox "пример приложения, отображающего загруженный образ")

<a name="Image_Caching" />

### <a name="downloaded-image-caching"></a>Кэширование загруженный образ

Объект [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/) также поддерживает кэширование загруженных изображений, настроенные с помощью следующих свойств:

- [`CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) -Ли кэширование включено (`true` по умолчанию).
- [`CacheValidity`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) -A `TimeSpan` , определяющий, как долго изображения будут храниться локально.

Кэширование включено по умолчанию и сохраняется локально на 24 часа. Чтобы отключить кэширование для конкретного изображения, создать экземпляр источник изображения следующим образом:

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri="http://server.com/image" };
```

Чтобы задать периодом указанным кэшем (например, 5 дней) следует создать источник изображения следующим образом:

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Встроенные кэширование упрощает для поддержки сценариев, таких как прокрутка списки изображений, где можно задать (или привязка) изображение в каждой ячейке и встроенные кэша берем на себя повторной загрузке образа, когда ячейка прокручена обратно в представление.

<a name="Icons_and_splashscreens" />

## <a name="icons-and-splashscreens"></a>Значки и экраны-заставки

Хотя не относятся к [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) представление, значков приложений и экраны-заставки, также важное применение образов в проектах Xamarin.Forms.

Настройка значков и экраны-заставки для приложений Xamarin.Forms выполняется в каждом из проектов приложений. Это означает, что создание правильно размера изображения для iOS, Android и универсальной платформы Windows. Эти образы должны с именем и находится в соответствии с требованиями каждой платформы.

## <a name="icons"></a>Значки

См. в разделе [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md), [Google иллюстрациях](http://developer.android.com/design/style/iconography.html), и [плитки и значок ресурсы, касающиеся](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) Дополнительные сведения о создании ресурсов приложения.

## <a name="splashscreens"></a>Экраны-заставки

Только приложения iOS и универсальной платформы Windows требуют экран-заставка (также называемый образ запуска экрана или по умолчанию).

Обратитесь к документации для [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md) и [экранов-заставок](/windows/uwp/launch-resume/splash-screens/) в центре разработчиков Windows.

## <a name="summary"></a>Сводка

Xamarin.Forms предоставляет ряд различных способов добавлять изображения в кроссплатформенных приложений, позволяя для одного образа для использования на платформах или для образов платформы должны быть указаны. Также автоматически кэшируются загруженных изображений, автоматизация распространенный сценарий кодирования.

Изображения значков и экран-заставка приложения настройки и настроен и для приложений не Xamarin.Forms - выполните то же руководство, используемое для конкретных платформ приложений.

## <a name="related-links"></a>Связанные ссылки

- [WorkingWithImages (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithImages/)
- [iOS работа с образами](~/ios/app-fundamentals/images-icons/index.md)
- [Android иллюстрациях](http://developer.android.com/design/style/iconography.html)
- [Рекомендации по плитки и значок ресурсов](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)
