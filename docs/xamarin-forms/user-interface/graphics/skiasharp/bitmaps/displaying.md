---
title: Отображение точечных рисунков SkiaSharp
description: Узнайте, как отобразить SkiaSharp растровые изображения в пикселях, размеры и расширены для заполнения прямоугольника с сохранением пропорций.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f4cc13a5e8794eb5f2f883f35d6a0e4d34788507
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615318"
---
# <a name="displaying-skiasharp-bitmaps"></a>Отображение точечных рисунков SkiaSharp

Субъект точечных рисунков SkiaSharp была представлена в статье  **[основы растрового изображения в SkiaSharp](../basics/bitmaps.md)**. В этой статье показали три способа загрузки точечные рисунки и три способа отображения растровых изображений. В этой статье рассматривает методы для загрузки точечных рисунков, которые вступают глубже в использование `DrawBitmap` методы `SKCanvas`.

![Отображение примера](displaying-images/DisplayingSample.png "Отображение примера")

`DrawBitmapLattice` И `DrawBitmapNinePatch` в этой статье рассматриваются методы  **[сегментированное отображение точечных рисунков SkiaSharp показателей](segmented.md)**.

Примеры на этой странице взяты из **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложения. На домашней странице приложения выберите **точечные рисунки SkiaSharp**и затем перейдите к разделу **отображение точечных рисунков** раздел.

## <a name="loading-a-bitmap"></a>Загрузка растрового изображения

Растровое изображение, используемое приложением SkiaSharp, обычно берутся из одного из трех различных источников:

- Из Интернета
- Из ресурсов, внедренных в исполняемый файл
- В библиотеке фотографий пользователя

Можно также для приложения SkiaSharp, чтобы создать новое растровое изображение, а затем нарисовать на нем или алгоритмически задать биты растрового изображения. Эти методы описаны в статьях **[создания и рисования в SkiaSharp точечные рисунки](drawing.md)** и **[доступ к пикселов точечного рисунка SkiaSharp](pixel-bits.md)**.

В следующих трех примерах кода загрузки растрового изображения, класс предполагается, что содержит поле типа `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Как и статья **[основы растрового изображения в SkiaSharp](../basics/bitmaps.md)** уже говорилось, лучший способ загрузки точечного рисунка в Интернете, — с [ `HttpClient` ](xref:System.Net.Http.HttpClient) класса. Один экземпляр класса может быть определена как поле:

```csharp
HttpClient httpClient = new HttpClient();
```

При использовании `HttpClient` с помощью приложений iOS и Android, необходимо задать свойства проекта, как описано в документах на  **[уровня безопасности транспорта (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Код, использующий `HttpClient` часто включает в себя `await` оператор, поэтому он должен храниться в `async` метод:

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

Обратите внимание, что `Stream` полученный из `GetStreamAsync` копируется в `MemoryStream`. Android не поддерживает `Stream` из `HttpClient` обрабатываемых основным потоком за исключением в асинхронные методы. 

[ `SKBitmap.Decode` ](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) Выполняет массу работы: `Stream` переданный ему объект ссылается на блок памяти, содержащий весь точечный рисунок в одной из распространенных форматов файлов точечного рисунка, обычно JPEG, PNG или GIF. `Decode` Метод должен определить формат и затем декодировать файл точечного рисунка в SkiaSharp в собственный формат внутреннего растрового изображения.

После код вызывает метод `SKBitmap.Decode`, возможно, сделает `CanvasView` таким образом, чтобы `PaintSurface` обработчик может отображать вновь загруженных растрового изображения.

Второй способ загрузки точечного рисунка, включив точечного рисунка в качестве внедренного ресурса в библиотеке .NET Standard ссылаются проекты для отдельных платформ. Ресурс, передается идентификатор [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) метод. Этот идентификатор ресурса состоит из имени сборки, имя папки и имя файла ресурсов, разделенных точками.

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

Файлы растровых изображений также могут храниться как ресурсы в проект отдельные платформы iOS, Android и универсальной платформы Windows (UWP). Однако загрузка этих маленьких точечных изображений требуется код, который находится в проекте платформы.

Третий подход для получения растрового изображения является из библиотеки рисунков пользователя. В следующем коде используется служба зависимость, которая включается в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** приложения. **SkiaSharpFormsDemo** библиотеки .NET Standard включает в себя `IPhotoLibrary` интерфейс, хотя каждый из проектов платформа содержит `PhotoLibrary` класс, реализующий этот интерфейс.

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

Как правило, такой код также делает недействительным `CanvasView` таким образом, чтобы `PaintSurface` обработчик можно отобразить новое растровое изображение.

`SKBitmap` Класс определяет несколько полезных свойств, включая [ `Width` ](xref:SkiaSharp.SKBitmap.Width) и [ `Height` ](xref:SkiaSharp.SKBitmap.Height), который отобразить размеры точечного рисунка, а также множество методов, включая методы для создания точечных рисунков, чтобы скопировать их и предоставлять битов пикселов. 

## <a name="displaying-in-pixel-dimensions"></a>Отображение в пикселах

SkiaSharp [ `Canvas` ](xref:SkiaSharp.SKCanvas) класс определяет четыре `DrawBitmap` методы. Эти методы позволяют растровые изображения для отображения двух фундаментально различными способами: 

- Указание `SKPoint` значение (или за его пределами `x` и `y` значения) отображает точечный рисунок в его размеры в пикселах. Пиксели точечного рисунка непосредственно сопоставляются пикселей экрана.
- Задание прямоугольник приводит растровое изображение растягиваться по размер и форму элемента управления rectangle. 

Отобразить точечный рисунок в его размеры с помощью [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) с `SKPoint` параметр или [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) с отдельными `x` и `y` параметры:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Эти два метода функционально идентичны. Указанная точка указывает на расположение верхнего левого угла растрового изображения относительно холста. Так как на разрешение мобильных устройств, настолько велико, меньшего размера растровых изображений обычно отображаются очень маленькими на этих устройствах.

Необязательный `SKPaint` параметр позволяет отобразить точечный рисунок с помощью прозрачности. Чтобы сделать это, создайте `SKPaint` и задайте `Color` одну `SKColor` значение с альфа-канал меньше, чем 1. Пример:

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

0x80, переданный в качестве последнего аргумента указывает 50% прозрачности. Можно также задать альфа-канал на один из предварительно определенных цветов:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Тем не менее цвет, сам не имеет значения. Только альфа-канал проверяется при использовании `SKPaint` объекта в `DrawBitmap` вызова.

`SKPaint` Объекта также играет роль при отображении растровых изображений с помощью режимы смешения или фильтрации эффекты. Они описаны в статьях [SkiaSharp композиции и режимы наложения](../effects/blend-modes/index.md) и [фильтры образа SkiaSharp](../effects/image-filters.md).

**Пикселах** странице в **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** пример программы отображает ресурса точечного рисунка, который составляет 320 пикселей 240 пикселей:

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

`PaintSurface` Обработчик центров точечного рисунка, вычисляя `x` и `y` значения на основе размеры поверхность отображения и размеры точечного рисунка:

[![Размеры](displaying-images/PixelDimensions.png "пикселей")](displaying-images/PixelDimensions-Large.png#lightbox)

Если необходимо отобразить растровое изображение в его левом верхнем углу приложения, он бы передал просто координаты (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Метод для загрузки ресурсов растровых изображений

Многие из примеров, дальше необходимо загрузить ресурсы растрового изображения. Статический `BitmapExtensions` в класс **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** решение содержит метод, чтобы помочь:

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

Обратите внимание, что `Type` параметра. Это может быть `Type` объект, связанный с любым типом в сборке, которая хранит ресурса точечного рисунка.

Это `LoadBitmapResource` метод будет использоваться в все последующие образцов, использующих ресурсы растрового изображения.

## <a name="stretching-to-fill-a-rectangle"></a>Растяжение для заливки прямоугольника

`SKCanvas` Класс также определяет [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) метода, который отображает точечный рисунок для прямоугольника, а другой — [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) метода, который отображает прямоугольную часть точечного рисунка для прямоугольник:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

В обоих случаях точечный рисунок растягивается для заполнения прямоугольника, именуемого `dest`. Во втором методе `source` прямоугольник позволяет выбрать подмножество растрового изображения. `dest` Прямоугольник определяется относительно устройство вывода; `source` прямоугольник определяется относительно растрового изображения.

**Заливки прямоугольника** страница демонстрирует первый из этих двух методов, отобразив этот же рисунок служить в предыдущем примере в прямоугольнике тот же размер холста: 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Обратите внимание на использование нового `BitmapExtensions.LoadBitmapResource` метод, чтобы задать `SKBitmap` поля. Полученный прямоугольник назначения из [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) свойство `SKImageInfo`, который описывает размер поверхность отображения:

[![Заполнения прямоугольника](displaying-images/FillRectangle.png "заполнения прямоугольника")](displaying-images/FillRectangle-Large.png#lightbox)

Обычно это _не_ желаемого. Изображение искажается растягивается по-разному в горизонтальном и вертикальном направлениях. При отображении растрового изображения в отличные от его размер в пикселях, обычно нужно сохранить пропорции исходного растрового изображения.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Растяжение при сохранении пропорций

Растяжение растрового изображения при сохранении пропорций — это процесс также называется _универсальный масштабирование_. Этот термин предлагаются алгоритма. Одно из возможных решений приведен на **универсальный масштабирование** страницы:

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

`PaintSurface` Вычисляет обработчик `scale` фактор, — это минимальная доля ширины и высоты отображаемой в высоту и ширину растрового изображения. `x` И `y` затем можно вычислить значения для центрирования масштабированное точечного рисунка в отображаемых ширины и высоты. Прямоугольник назначения имеет левого верхнего угла `x` и `y` и нижний правый угол эти значения, а также масштабированной ширины и высоты точечного рисунка:

[![Универсальный код масштабирования](displaying-images/UniformScaling.png "универсальный масштабирование")](displaying-images/UniformScaling-Large.png#lightbox)

Повернуть телефон набок, чтобы увидеть растровое изображение растягивается для этой области:

[![Универсальный альбомная масштабирование](displaying-images/UniformScaling-Landscape.png "универсальный масштабирование альбомная")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

Преимущество использования это `scale` коэффициент становится очевидным, если вы хотите реализовать немного другой алгоритм. Предположим, что вы хотите сохранить соотношение сторон растрового изображения, но также заполняет прямоугольник назначения. Это можно сделать единственным способом является обрезка часть изображения, но можно реализовать алгоритм, просто изменив `Math.Min` для `Math.Max` в приведенном выше коде. Ниже приведен результат. 

[![Универсальный код вместо масштабирование](displaying-images/UniformScaling-Alternative.png "альтернативный универсальный код масштабирования")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Растрового изображения пропорции сохраняются, но обрезаются области слева и справа от растрового изображения.

## <a name="a-versatile-bitmap-display-function"></a>Функция отображения универсальный растрового изображения

Программирования среды на основе XAML (например, UWP и Xamarin.Forms) имеют возможность увеличивать и уменьшать размер точечных рисунков, сохраняя их пропорций. Несмотря на то, что SkiaSharp не поддерживает эту функцию, вы можете реализовать его самостоятельно. `BitmapExtensions` Классов, включенных в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) приложения показано как. Этот класс определяет два новых `DrawBitmap` методы, выполняющие вычисления пропорции. Эти новые методы являются методами расширения `SKCanvas`.

Новый `DrawBitmap` методов включают параметр типа `BitmapStretch`, перечисление, определенное в **BitmapExtensions.cs** файла:

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

`None`, `Fill`, `Uniform`, И `UniformToFill` члены являются такие же, как в приложениях UWP [ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) перечисления. Аналогично Xamarin.Forms [ `Aspect` ](xref:Xamarin.Forms.Aspect) перечисление определяет члены `Fill`, `AspectFit`, и `AspectFill`.

**Универсальный масштабирование** страницу, показанную выше центров растрового изображения в прямоугольник, но при необходимости другие параметры, такие как расположение растрового изображения в левой или правой стороны прямоугольника, или на верхний или нижний. Это назначение `BitmapAlignment` перечисления:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Параметры выравнивания не оказывают влияния при использовании с `BitmapStretch.Fill`.

Первый `DrawBitmap` функцию расширения содержит прямоугольник назначения, но не исходного прямоугольника. Параметры по умолчанию определены таким образом, если требуется, чтобы растровое изображение по центру, требуется только указать `BitmapStretch` член:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

Основное назначение этого метода является для вычисления коэффициента с именем `scale` , применяется к высоту и ширину растрового изображения при вызове `CalculateDisplayRect` метод. Это метод, который вычисляет прямоугольник для отображения растрового изображения, на основании выравнивания по горизонтали и вертикали:

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

`BitmapExtensions` Класс содержит дополнительный `DrawBitmap` метод с исходного прямоугольника для указания подмножества растрового изображения. Этот метод похож на первую, за исключением того, что коэффициент масштабирования вычисляется на основе `source` прямоугольник, а затем применяются к `source` прямоугольник в вызове `CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

Первый из этих двух новых `DrawBitmap` демонстрируется методы в **режимы масштабирования** страницы. Файл XAML содержит три `Picker` элементы, которые позволяют выбрать элементы `BitmapStretch` и `BitmapAlignment` перечисления:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

Файл с выделенным кодом, просто делает недействительным `CanvasView` при любой `Picker` элемент изменился. `PaintSurface` Обработчик получает доступ к трем `Picker` представления для вызова `DrawBitmap` метод расширения:

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

Ниже приведены некоторые сочетания параметров.

[![Режимы масштабирования](displaying-images/ScalingModes.png "режимы масштабирования")](displaying-images/ScalingModes-Large.png#lightbox)

**Подмножества прямоугольник** страница практически имеет тот же файл XAML **режимы масштабирования**, но файл с выделенным кодом определяет прямоугольную часть точечного рисунка, выданный `SOURCE` поля: 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

Этот источник прямоугольник изолирует monkey head, как показано на эти снимки экрана:

[![Прямоугольник подмножества](displaying-images/RectangleSubset.png "подмножества прямоугольника")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

