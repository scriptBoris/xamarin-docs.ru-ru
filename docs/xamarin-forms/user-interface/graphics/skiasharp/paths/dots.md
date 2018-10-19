---
title: Точки и тире в SkiaSharp
description: В этой статье описано, как к Освойте тонкости механизмов рисования в SkiaSharp пунктирная и пунктирные линии и демонстрирует это с помощью примера кода.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a28e4bbaae28befd91278ac5c2b9e7c9c0b522b9
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615422"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Точки и тире в SkiaSharp

_Освойте тонкости механизмов рисования в SkiaSharp пунктирная и пунктирных линий_

SkiaSharp позволяет рисовать линии, которые не сплошной, но вместо этого состоят из точки и тире.

![](dots-images/dottedlinesample.png "Пунктирная линия")

Это сделать с помощью *эффект пути*, который является экземпляром [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) класс, который задается для [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) свойство `SKPaint`. Можно создать путь эффект (или эффекты объединения пути), с помощью одного из статических создания методов, определенных `SKPathEffect`. (`SKPathEffect` один из шести эффектов поддерживается SkiaSharp; остальные описаны в разделе [ **эффект SkiaSharp**](../effects/index.md).)

Для рисования точечных или пунктирных линий, следует использовать [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) статический метод. Существует два аргумента: это массив `float` значений, обозначающих длину точек и тире и длину пробелов между ними. Этот массив должен иметь четное число элементов и должно быть по крайней мере два элемента. (Может быть ноль элементов в массиве, но этот приводит сплошная линия.) При наличии двух элементов, первый представляет собой длину точка или тире и второй — длина пробела перед Далее точка или тире. Если имеется более двух элементов, то они находятся в следующем порядке: тире длины, длина временного промежутка, тире, длина временного промежутка и т. д.

Как правило нужно будет сделать длинами тире и свойство gap кратно ширина штриха. Если ширина мазков 10 точек, например, массив {10, 10} будет нарисуйте пунктирной линией, точек и пробелов которых же длины, толщина штриха.

Тем не менее `StrokeCap` параметр `SKPaint` объекта также влияет на эти точки и тире. Как вы вскоре увидите, что оказывает влияние на элементы исходного массива.

Разделенные точками и демонстрируются пунктирные линии на **точек и тире** страницы. [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) файл создает два `Picker` просматривает, один для возможности выбирать наконечник штриха, а второй для выбора массив dash:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 Первые три элемента в `dashArrayPicker` предполагается, что ширина мазков 10 точек. {10, 10} массив предназначен для пунктирной линией {30, 10} — пунктирная линия и {10, 10, 30, 10} для точки и штриховой линией. (Другие будут рассмотрены чуть позже.)

[ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) Содержит файл с выделенным кодом `PaintSurface` обработчик события, а также ряд вспомогательные процедуры для доступа к `Picker` представления:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint); 
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

На следующих снимках экрана на экране iOS слева показано пунктирной линией.

[![](dots-images/dotsanddashes-small.png "Тройной снимок экрана страницы точки и тире")](dots-images/dotsanddashes-large.png#lightbox "тройной снимок экрана страницы точки и тире")

Тем не менее экран Android также должна показать пунктирной линией, с помощью массива {10, 10}, но вместо этого строки является сплошная заливка. Что произошло? Проблема в том, что экран Android также имеет значение caps stroke `Square`. При этом расширяется все дефисы на половину толщины штриха, они заполняют пробелы.

Чтобы обойти эту проблему, при использовании наконечник штриха из `Square` или `Round`, необходимо уменьшить dash длины массива длиной stroke (иногда приводит к dash длиной 0) и увеличения длины промежуток, длина штриха. Это как окончательный три тире массивов в `Picker` рассчитаны в файле XAML:

- {10, 10} становится {0, 20} пунктирная линия
- {30, 10} становится {20, 20} для пунктирная линия
- {10, 10, 30, 10} становится {0, 20, 20, 20} для пунктирная и пунктирные линии

Ограничение экрана показывает универсальной платформы Windows, разделенные точками и пунктирных линий для штриха из `Round`. `Round` Наконечник часто дает наилучший внешний вид точки и дефисы в толстой линии.

Пока не упоминается стала второго параметра для `SKPathEffect.CreateDash` метод. Этот параметр называется `phase` и он ссылается на смещение в пунктирная-шаблон для начала строки. Например, если массив dash {10, 10} и `phase` равно 10, то строка начинается с пробел, а не точки.

Одним из интересных применений `phase` параметр находится в анимации. **Анимировано спирали** страница аналогична **спирали Archimedean** странице, за исключением случаев, [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) анимируемый классом `phase` параметра с помощью Xamarin.Forms `Device.Timer` метод:


```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

Конечно вам придется фактически работающих с программой для анимации см. в разделе:

[![](dots-images/animatedspiral-small.png "Тройной снимок экрана страницы анимировано спирали")](dots-images/animatedspiral-large.png#lightbox "тройной снимок экрана страницы анимировано спирали")

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
