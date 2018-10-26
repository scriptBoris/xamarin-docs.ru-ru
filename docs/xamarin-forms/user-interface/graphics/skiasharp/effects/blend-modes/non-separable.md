---
title: Режимы нелинейно разделяемых blend
description: Используйте режимы нелинейно разделяемых blend изменить оттенок, насыщенность и яркость.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 07df66e69186803e3322bd71a4b3b37710655de4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131893"
---
# <a name="the-non-separable-blend-modes"></a>Режимы нелинейно разделяемых blend

Как было показано в этой статье [ **режимы смешения SkiaSharp отделяемых**](separable.md), отделяемых blend режимы выполнения операций над каналов красного, зеленого и синего отдельно. Режимы нелинейно разделяемых blend — нет. Работающие на уровни тона, насыщенности и яркости цвета blend нелинейно разделяемых режимах можно изменить цвета интересными способами.

![Пример нелинейно разделяемых](non-separable-images/NonSeparableSample.png "нелинейно разделяемых образца")

## <a name="the-hue-saturation-luminosity-model"></a>Оттенок-насыщенность-яркость модели

Сведения о режимах нелинейно разделяемых blend, бывает необходимо считать пиксели исходного и конечного цветов в модели оттенок-насыщенность-яркость. (Яркость также называется освещенность.)

Модель цвета HSL, которые обсуждались в этой статье [ **интеграция с Xamarin.Forms** ](../../basics/integration.md) и примера программы в этой статье позволяет Экспериментирование с помощью цвета HSL. Можно создать `SKColor` с использованием значений цветового тона, насыщенности и яркости с помощью статического [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*) метод.

Цветовой тон представляет главный длина волны цвета. Hue диапазон значений от 0 до 360 и переключение между базами данных-источниками сложения и вычитания: красная используется значение 0, желтый — 60, зеленый — 120, голубой 180, синий — 240, пурпурный — 300 и цикл возвращается к красным в 360.

Если цвет отсутствует главный &mdash; приведен пример цвет оттенком серого или черным или белым &mdash; цветовой тон не указано, и обычно присвоено значение 0. 

Значения насыщенность может находиться в диапазоне от 0 до 100 и указывают чистота цвета. Значение насыщенности 100 является рафинированной цветом, а значения меньше 100 привести цвет, который должен стать более grayish. Значение насыщенности 0 приводит к оттенок серого цвета.

Значение яркости (или освещенность) указывает, как яркий цвет. Значение яркости 0 черный независимо от других параметров. Аналогичным образом значение яркости 100 будет белый. 

Значение HSL (0, 100, 50) является значение RGB (FF, 00, 00), который является чисто red. Значение HSL (180, 100, 50) является значение RGB (00, FF, FF), чисто голубой. Насыщенность уменьшается, уменьшается на основной цвет компонента и увеличиваются других компонентов. На уровне насыщенность 0 все компоненты одинаковы и оттенком серого является цвет. Уменьшить яркость, чтобы перейти на черный; Увеличьте яркость, чтобы перейти к белому.

## <a name="the-blend-modes-in-detail"></a>Режимы наложения, подробно

Как и другие режимы blend четыре режима нелинейно разделяемых blend включают в себя назначения (это часто бывает растрового изображения) источник, который чаще всего одного цвета или градиент. Режимы наложения объединить тона, насыщенности и яркости значения из источника и назначения:

| Режим наложения   | Компоненты источника | Компоненты из места назначения |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Цветовой тон                    | Насыщенность и яркость   |
| `Saturation` | Насыщенность             | Цветовой тон и яркость          |
| `Color`      | Оттенок и насыщенность     | Яркость                  | 
| `Luminosity` | Яркость             | Оттенок и насыщенность          | 

См. в разделе W3C [ **композиции и наложения уровня 1** ](https://www.w3.org/TR/compositing-1/) спецификации для алгоритмов.

**Не нелинейно разделяемых режимах Blend** страница содержит `Picker` для выбора одного из этих режимов и трех blend `Slider` представления, чтобы выбрать цвет HSL:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Для экономии места, три `Slider` представления не распознаются в пользовательском интерфейсе программы. Необходимо помнить, что порядок цветового тона, насыщенности и яркости. Два `Label` представлениях в нижней части страницы отображаются значения цвета HSL и RGB.

Файл с выделенным кодом загружает один из ресурсов точечного рисунка, отображает, который максимально на холсте и затем рассматриваются canvas с прямоугольником. Цвет прямоугольника основан на трех `Slider` представления и режим наложения находится выбрана в `Picker`:

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Обратите внимание на то, что программа не отображает значение цвета HSL согласно выбору три ползунки. Вместо этого он создает значение цвета на основе эти ползунки, а затем использует [ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*) метод для получения значений цветового тона, насыщенности и яркости. Это обусловлено `FromHsl` метод преобразует цвета HSL цвета RGB, который хранится внутренним образом в `SKColor` структуры. `ToHsl` Метод преобразует цвета из RGB HSL, но не всегда будет возвращаться результат исходному значению. 

Например `FromHsl` преобразует значение HSL (180, 50, 0) в цвета RGB (0, 0, 0), так как `Luminosity` равно нулю. `ToHsl` Метод преобразует цвета RGB (0, 0, 0) цвета HSL (0, 0, 0), так как значения оттенок и насыщенность неприменимы. При использовании этой программы, это лучше, что отображается представление цвета HSL, в программе используются вместо указанного вами с помощью ползунков.

`SKBlendModes.Hue` Режим наложения используется уровень Hue источника, сохраняя насыщенность и яркость уровни назначения. При тестировании этого режима blend, насыщенности и яркости ползунки должно быть присвоено что-то отличное от 0 или 100 так, как в таком случае цветовой тон не определен однозначно.

[![Режимы нелинейно разделяемых Blend - Hue](non-separable-images/NonSeparableBlendModes-Hue.png "режимов нелинейно разделяемых Blend — Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Если вы используйте установите ползунок на 0 (как в iOS снимок экрана слева), все, что включает красно. Но это не означает, что изображение не полностью зеленый и синий. Очевидно, что существует по-прежнему оттенков в результат. Например, цвета RGB (40, 40, C0) эквивалентен цвета HSL (240, 50, 50). Цветовой тон имеет значение blue, но значение насыщенности 50 указывает, что компоненты красного и зеленого. Если задано значение 0 с цветовой тон `SKBlendModes.Hue`, цвета HSL является (0, 50, 50), которое является цветом RGB (C0, 40, 40). По-прежнему синие и зеленые компоненты, но теперь главный компонент отображается красным цветом.

`SKBlendModes.Saturation` Режим наложения сочетает уровень насыщенности источника с Hue и яркость массива назначения. Как цветового тона насыщенности не является правильно определенным, если яркость равно 0 или 100. В теории любой параметр яркость между этими крайними должен работать. Тем не менее параметр яркость похоже, что влияет на результат больше, чем следует. Отрегулируйте яркость до 50, и вы увидите, как можно задать уровень насыщенности изображения:

[![Режимы нелинейно разделяемых Blend - насыщенности](non-separable-images/NonSeparableBlendModes-Saturation.png "режимов нелинейно разделяемых Blend — насыщенность")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Этот режим blend можно использовать для увеличения цвет насыщенность тусклым изображения, либо уменьшить насыщенность уменьшается до нуля (как показано на снимке экрана iOS слева) для результирующего изображение, составленное из только оттенков серого.

`SKBlendModes.Color` Режим blend сохраняет яркость назначения, но использует оттенок и насыщенность источника. Опять же, это подразумевает, что любой параметр ползунка яркость где-то между высокой должно работать. 

[![Цвета Blend нелинейно разделяемых режимах -](non-separable-images/NonSeparableBlendModes-Color.png "режимы нелинейно разделяемых Blend - цвет")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Вскоре вы увидите приложение этот режим blend.

Наконец `SKBlendModes.Luminosity` режим blend является противоположностью `SKBlendModes.Color`. Она сохраняет оттенок и насыщенность назначения, но использует яркость источника. `Luminosity` Режим blend — самый Загадочный пакета: оттенок и насыщенность ползунки влияет на изображение, но хотя бы на средний яркость изображения не уникален:

[![Режимы нелинейно разделяемых Blend - яркость](non-separable-images/NonSeparableBlendModes-Luminosity.png "режимов нелинейно разделяемых Blend — яркость")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

В теории увеличив или уменьшив яркость изображения должна обеспечивать более светлым или темным. Что интересно [пример яркость в Skia **ссылку SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) очень похож.

Это обычно не так, необходимо использовать один из режимов нелинейно разделяемых blend с источником, состоит из одного цвета, применяемого к изображению всей назначения. Это просто слишком велико. Необходимо ограничить эффект к одной части изображения. В этом случае источника, скорее всего, будет включать transparancy или возможно источник будет ограничен графическое меньшего размера.

## <a name="a-matte-for-a-separable-mode"></a>Подложки для разделяемых режима

Вот один из точечных рисунков, включена как ресурс в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. Имя файла — **Banana.jpg**:

![Банан Monkey](non-separable-images/Banana.jpg "Monkey банан")

Существует возможность создания подложки, охватывающая только банан. Это также к ресурсу в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образца. Имя файла — **BananaMatte.png**:

![Матовый банан](non-separable-images/BananaMatte.png "матовой банан")

Кроме формы банан черным до конца растрового изображения является прозрачным.

**Синий банан** страница использует этот матовой изменить оттенок и насыщенность банан, который владеет monkey, но изменять ничего в образе. 

В следующем `BlueBananaPage` класс, **Banana.jpg** растровое изображение загружается как поле. Загружает конструктор **BananaMatte.png** растрового изображения как `matteBitmap` , но не сохраняет этот объект вне конструктора. Вместо этого третьего точечный рисунок с именем `blueBananaBitmap` создается. `matteBitmap` Рисуется на `blueBananaBitmap` следуют `SKPaint` с его `Color` изменится на синий и его `BlendMode` присвоено `SKBlendMode.SrcIn`. `blueBananaBitmap` В основном остается прозрачным, но с сплошной синей чистого образа банан:

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

`PaintSurface` Обработчик рисует растровое изображение с monkey, удерживая банан. Этот код следует отображать `blueBananaBitmap` с `SKBlendMode.Color`. На поверхности банан оттенок и насыщенность каждый пиксел заменяется сплошными синими, соответствующее значение 240 оттенок и насыщенность значение 100. Яркость, тем не менее, остается тем же, это означает, что банан будет иметь реалистичные текстуры, несмотря на его новый цвет:

[![Синий банан](non-separable-images/BlueBanana.png "синий банан")](non-separable-images/BlueBanana-Large.png#lightbox)

Попробуйте изменить режим наложения для `SKBlendMode.Saturation`. Банан остается желтый, но это более интенсивный желтым. В реальном приложении это может быть эффекта гораздо полезнее, чем включение банан синий.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)