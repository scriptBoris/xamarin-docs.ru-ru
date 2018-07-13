---
title: Основы растрового изображения в SkiaSharp
description: В этой статье объясняется, как загружать точечные рисунки в SkiaSharp из различных источников и отображать их в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: dec6fa1534f14836ae98677ad33e280ff510fb97
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995193"
---
# <a name="bitmap-basics-in-skiasharp"></a>Основы растрового изображения в SkiaSharp

_Загрузка точечных рисунков из различных источников и отобразить их._

Поддержка точечных рисунков в SkiaSharp является достаточно большим. В этой статье рассматриваются только основные &mdash; как загружать точечные рисунки и способ их отображения:

![](bitmaps-images/bitmapssample.png "Отображение двух точечных рисунков")

Битовая карта SkiaSharp — это объект типа [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Существует много способов создания растрового изображения, но в этой статье задействует, чтобы [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/) метод, который загружает точечный рисунок из [ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/) объект, который ссылается на файл точечного рисунка. Его удобно использовать [ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/) класс, производный от `SKStream` так как он имеет конструктор, принимающий .NET [ `Stream` ](xref:System.IO.Stream) объекта.

**Основные точечные рисунки** странице в **SkiaSharpFormsDemos** программа демонстрирует, как загрузить точечных рисунков из трех различных источников:

- Из Интернета
- Из ресурсов, внедренных в исполняемый файл
- В библиотеке фотографий пользователя

Три `SKBitmap` объекты для этих трех источников определены как поля в [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) класса:

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>Загрузка точечный рисунок из Интернета

Для загрузки точечного рисунка по URL-АДРЕСУ, можно использовать [ `WebRequest` ](xref:System.Net.WebRequest) класса, как показано в следующем коде выполняется `BasicBitmapsPage` конструктор. URL-адрес указывает на область веб-сайте Xamarin с некоторые образцы рисунков. Пакет веб-сайте позволяет добавления спецификацию для изменения размеров точечного рисунка для конкретного ширины:

```csharp
Uri uri = new Uri("http://developer.xamarin.com/demo/IMG_3256.JPG?width=480");
WebRequest request = WebRequest.Create(uri);
request.BeginGetResponse((IAsyncResult arg) =>
{
    try
    {
        using (Stream stream = request.EndGetResponse(arg).GetResponseStream())
        using (MemoryStream memStream = new MemoryStream())
        {
            stream.CopyTo(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            using (SKManagedStream skStream = new SKManagedStream(memStream))
            {
                webBitmap = SKBitmap.Decode(skStream);
            }
        }
    }
    catch
    {
    }

    Device.BeginInvokeOnMainThread(() => canvasView.InvalidateSurface());

}, null);
```

Растровое изображение после успешной загрузки, метод обратного вызова передается `BeginGetResponse` выполнения метода. `EndGetResponse` Вызов должен быть в `try` блокировки в случае, если произошла ошибка. `Stream` Полученный из `GetResponseStream` не подходит на некоторых платформах, поэтому содержимое растрового изображения копируются в `MemoryStream` объекта. На этом этапе `SKManagedStream` объект может быть создан. Теперь ссылается на файл точечного рисунка, который вероятнее всего, файл JPEG или PNG. `SKBitmap.Decode` Метод декодирует файл точечного рисунка и сохраняет результаты во внутреннем формате SkiaSharp.

Метод обратного вызова передается `BeginGetResponse` выполняется после завершения конструктора выполнения, т. е `SKCanvasView` необходимо сделать недействительным, чтобы разрешить `PaintSurface` обработчик для обновления отображения. Тем не менее `BeginGetResponse` обратного вызова выполняется дополнительный поток выполнения, поэтому необходимо использовать `Device.BeginInvokeOnMainThread` для запуска `InvalidateSurface` метод в потоке пользовательского интерфейса.

## <a name="loading-a-bitmap-resource"></a>Загрузка ресурса точечного рисунка

С точки зрения кода самый простой подход к загрузке растровые изображения — включая ресурса точечного рисунка непосредственно в приложении. **SkiaSharpFormsDemos** программа включает в себя папку с именем **мультимедиа** содержащего точечный рисунок файл с именем **monkey.png**. В **свойства** диалоговое окно для этого файла необходимо предоставить такой файл **действие при построении** из **внедренный ресурс**!

Каждый внедренный ресурс имеет *идентификатор ресурса* , состоящее из имени проекта, папку и имя файла, всех подключенных по периодам: **SkiaSharpFormsDemos.Media.monkey.png**. Вы можете получить доступ к этому ресурсу, указав этот ресурс идентификатор в качестве аргумента для [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) метод [ `Assembly` ](xref:System.Reflection.Assembly) класса:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

Это `Stream` объекта могут быть преобразованы прямо в `SKManagedStream` объекта.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Загрузка точечный рисунок из Медиатеки

Можно также для пользователя для загрузки фотографии из библиотеки рисунков устройства. Эта функция позволяет Xamarin.Forms, сам не предоставляется. Задание требует службы зависимостей, подобного описанному в статье [комплектации фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

**IPicturePicker.cs** файл и три **PicturePickerImplementation.cs** различные проекты были скопированы файлы из этой статьи **SkiaSharpFormsDemos**решения и новые имена пространств имен. Кроме того, если в Android **MainActivity.cs** файл был изменен таким образом, как описано в статье, и проект iOS предоставлены разрешения на доступ к библиотеке фотографий с помощью двух строк в конце **info.plist**  файл.

`BasicBitmapsPage` Конструктор добавляет `TapGestureRecognizer` для `SKCanvasView` для получения уведомлений об касания. При получении касание `Tapped` обработчик получает доступ к рисунок выбора зависимостей службы и вызывает `GetImageStreamAsync`. Если `Stream` , то возвращается объект, а затем содержимое копируется в `MemoryStream`, как требуется в некоторых платформ. Остальной код похож на двух других методов:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPicturePicker picturePicker = DependencyService.Get<IPicturePicker>();

    using (Stream stream = await picturePicker.GetImageStreamAsync())
    {
        if (stream != null)
        {
            using (MemoryStream memStream = new MemoryStream())
            {
                stream.CopyTo(memStream);
                memStream.Seek(0, SeekOrigin.Begin);

                using (SKManagedStream skStream = new SKManagedStream(memStream))
                {
                    libraryBitmap = SKBitmap.Decode(skStream);
                }
            }
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Обратите внимание, что `Tapped` вызовов обработчика `InvalidateSurface` метод `SKCanvasView` объекта. При этом создается новый вызов к `PaintSurface` обработчика.

## <a name="displaying-the-bitmaps"></a>Отображение точечных рисунков

`PaintSurface` Обработчик должен отображать три растровых изображений. Обработчик предполагается, что телефон находится в книжной ориентации и делит холст по вертикали на три равные части.

Первый растровое изображение будет отображаться с самых простых [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/) метод. Все, что вам нужно указать приведены координаты X и Y, где будет размещаться верхнего левого угла растрового изображения.

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Несмотря на то что `SKPaint` определен параметр, он имеет значение по умолчанию `null` и ее можно игнорировать. Пиксели точечного рисунка, просто передаются пикселы поверхность отображения с взаимно-однозначное сопоставление.

Программа может получить размеры точечного рисунка с [ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/) и [ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/) свойства. Эти свойства позволяют рассчитать координаты, чтобы разместить растровое изображение в центре треть верхней части холста:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

Два других точечные рисунки, отображаются с версией [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/) с `SKRect` параметр:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Третья версия [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) имеет два `SKRect` аргументы для указания прямоугольную часть точечного рисунка для отображения, но эта версия не используется в этой статье.

Ниже приведен код для отображения растрового изображения, загруженные из внедренного ресурса точечного рисунка.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

Точечный рисунок растягивается до размеров прямоугольника, поэтому monkey растягивается по горизонтали на этих снимках экрана:

[![](bitmaps-images/basicbitmaps-small.png "Тройной снимок экрана страницы основные точечные рисунки")](bitmaps-images/basicbitmaps-large.png#lightbox "тройной снимок экрана страницы основные точечных рисунков")

Третье изображение &mdash; вы увидите, только если вы запустите программу и Загружаем фотографию из библиотеки рисунков &mdash; также отображается внутри прямоугольника, но прямоугольника положение и размер корректируются будет сохраняться соотношение сторон растрового изображения. Этот расчет является немного сложнее, поскольку она требует вычисление на основе размера точечного рисунка и целевого прямоугольника коэффициент масштабирования и выравнивание по центру прямоугольника в этой области:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

Если точечный рисунок отсутствует еще были загружены из библиотеки рисунков, а затем `else` блок отображает текст выводится на экран.


## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Выбрав фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
