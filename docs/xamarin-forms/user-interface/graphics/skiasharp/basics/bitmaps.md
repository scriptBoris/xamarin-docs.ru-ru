---
title: Основы растрового изображения в SkiaSharp
description: В этой статье объясняется, как загружать точечные рисунки в SkiaSharp из различных источников и отображать их в приложениях Xamarin.Forms и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: cbce6f414586597dc2b2788aa18b03228c128018
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130963"
---
# <a name="bitmap-basics-in-skiasharp"></a>Основы растрового изображения в SkiaSharp

_Загрузка точечных рисунков из различных источников и отобразить их._

Поддержка точечных рисунков в SkiaSharp является достаточно большим. В этой статье рассматриваются только основные &mdash; как загружать точечные рисунки и способ их отображения:

![](bitmaps-images/bitmapssample.png "Отображение двух точечных рисунков")

Битовая карта SkiaSharp — это объект типа [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Существует много способов создания растрового изображения, но в этой статье задействует, чтобы [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/System.IO.Stream/) метод, который загружает точечный рисунок из .NET `Stream` объекта.

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

Для загрузки точечного рисунка по URL-АДРЕСУ, можно использовать [ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) класса. Следует создавать экземпляр только один экземпляр `HttpClient` и повторно использовать ее, поэтому сохраните его как поле:

```csharp
HttpClient httpClient = new HttpClient();
```

При использовании `HttpClient` с помощью приложений iOS и Android, необходимо задать свойства проекта, как описано в документах на  **[уровня безопасности транспорта (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Так как это наиболее удобный для использования `await` оператор с `HttpClient`, код не может быть выполнен в `BasicBitmapsPage` конструктор. Вместо этого он является частью `OnAppearing` переопределить. URL-адрес указывает на область веб-сайте Xamarin с некоторые образцы рисунков. Пакет веб-сайте позволяет добавления спецификацию для изменения размеров точечного рисунка для конкретного ширины:


```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

При использовании Android приведет к появлению исключения `Stream` возвращаемые `GetStreamAsync` в `SKBitmap.Decode` метод так как он выполняет длительных операций на основной поток. По этой причине содержимое файла точечного рисунка копируются `MemoryStream` с помощью `CopyToAsync`.

Статический `SKBitmap.Decode` метод отвечает за декодирование файлы растровых изображений. Она работает в JPEG, PNG, GIF и нескольких других форматов популярных растровое изображение и сохраняет результаты во внутреннем формате SkiaSharp. На этом этапе `SKCanvasView` необходимо сделать недействительным, чтобы разрешить `PaintSurface` обработчик для обновления отображения. 

## <a name="loading-a-bitmap-resource"></a>Загрузка ресурса точечного рисунка

С точки зрения кода самый простой подход к загрузке растровые изображения — включая ресурса точечного рисунка непосредственно в приложении. **SkiaSharpFormsDemos** программа включает в себя папку с именем **мультимедиа** содержащего точечный рисунок файл с именем **monkey.png**. В **свойства** диалоговое окно для этого файла необходимо предоставить такой файл **действие при построении** из **внедренный ресурс**!

Каждый внедренный ресурс имеет *идентификатор ресурса* , состоящее из имени проекта, папку и имя файла, всех подключенных по периодам: **SkiaSharpFormsDemos.Media.monkey.png**. Вы можете получить доступ к этому ресурсу, указав этот ресурс идентификатор в качестве аргумента для [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) метод [ `Assembly` ](xref:System.Reflection.Assembly) класса:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Это `Stream` объект можно передать непосредственно в `SKBitmap.Decode` метод.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Загрузка точечный рисунок из Медиатеки

Можно также для пользователя для загрузки фотографии из библиотеки рисунков устройства. Эта функция позволяет Xamarin.Forms, сам не предоставляется. Задание требует службы зависимостей, подобного описанному в статье [комплектации фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

**IPhotoLibrary.cs** файл **SkiaSharpFormsDemos** проекта, а также три **PhotoLibrary.cs** файлов в проектах платформы была взята из этой статьи. Кроме того, если в Android **MainActivity.cs** файл был изменен таким образом, как описано в статье, и проект iOS предоставлены разрешения на доступ к библиотеке фотографий с помощью двух строк в конце **info.plist**  файл.

`BasicBitmapsPage` Конструктор добавляет `TapGestureRecognizer` для `SKCanvasView` для получения уведомлений об касания. При получении касание `Tapped` обработчик получает доступ к рисунок выбора зависимостей службы и вызывает `GetImageStreamAsync`. Если `Stream` , то возвращается объект, а затем содержимое копируется в `MemoryStream`, как требуется в некоторых платформ. Остальной код похож на двух других методов:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
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
