---
title: Xamarin.Forms BoxView
description: В этой статье описывается использование цветного прямоугольника для оформления, графики и взаимодействия в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2017
ms.openlocfilehash: 0a99845b23ee32a00a6894ef60988e61e361805e
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209249"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) Подготавливает к просмотру простой прямоугольник заданную ширину, высоту и цвет. Можно использовать `BoxView` для оформления, ограниченные механизмы графики, а также для взаимодействия с пользователем с помощью сенсорного ввода.

Поскольку Xamarin.Forms не имеет встроенных векторной графики системы, `BoxView` помогает для компенсации. Некоторые примеры программ, описанные в этой статье использует `BoxView` для отображения графики. `BoxView` Можно размера для сходства строки заданной ширины и толщины, а затем поворачивать угол с помощью `Rotation` свойство.

Несмотря на то что `BoxView` может имитировать простой графикой, может потребоваться изучение [с помощью SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) для более сложных требований графики.

В этой статье рассматриваются следующие вопросы:

- **[Настройка размера и цвета BoxView](#colorandsize)**  &ndash; задать `BoxView` свойства.
- **[Оформление текста отрисовки](#textdecorations)**  &ndash; использовать `BoxView` для подготовки к просмотру строк.
- **[Список цветов с BoxView](#listingcolors)**  &ndash; отображения всех системных цветов в `ListView`.
- **[Воспроизведение игры жизни путем создания подкласса BoxView](#subclassing)**  &ndash; реализовать знаменитый сотовой связи машин.
- **[Создание цифровых часов](#digitalclock)**  &ndash; имитировать отображения матричный.
- **[Создание часов аналогом](#analogclock)**  &ndash; преобразовывать и анимировать `BoxView` элементов.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Параметр BoxView цвет и размер

Очень часто можно задать следующие три свойства `BoxView`:

- [`Color`](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) Чтобы задать значение цвета.
- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) Чтобы задать ширину `BoxView` в аппаратно независимых единицах.
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) Чтобы задать высоту `BoxView`.

`Color` Свойство относится к типу `Color`; свойство можно задать для любой `Color` значения, включая 141 статические только для чтения поля с именем цвета в алфавитном порядке от `AliceBlue` для `YellowGreen`.

`WidthRequest` И `HeightRequest` свойства играют роль, только если `BoxView` — *произвольные* в макете. Это случается, когда контейнер макета, должно быть известно дочернего элемента размер, например, при `BoxView` является дочерним ячейки автоматически определяемым размером в `Grid` макета. Объект `BoxView` можно без ограничений, когда его `HorizontalOptions` и `VerticalOptions` свойствам присваиваются значения, отличного от `LayoutOptions.Fill`. Если `BoxView` — без ограничений, но `WidthRequest` и `HeightRequest` не задано, то ширина или высота присваиваются значения по умолчанию 40 единиц или около 1/4 дюйма на мобильных устройствах.

`WidthRequest` И `HeightRequest` свойства игнорируются, если `BoxView` — *ограниченного* в формате, в котором регистр контейнера макета налагает на свой собственный размер `BoxView`.

Объект `BoxView` в одном измерении ограниченное и неограниченное в другой. Например если `BoxView` является потомком вертикальной `StackLayout`, по вертикали `BoxView` — неограниченное и его горизонтальный размер обычно ограничен. Однако существуют исключения для этого измерения горизонтальной: Если `BoxView` имеет его `HorizontalOptions` свойства, значение, отличное от `LayoutOptions.Fill`, то горизонтальный размер также произвольные. Можно также для `StackLayout` сам иметь произвольные горизонтальный размер, в этом случае `BoxView` также будет горизонтально без ограничений.

[ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView) пример отображает один дюйм квадратные неограниченное `BoxView` в центре его страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Ниже приведен результат:

[![Основные BoxView](boxview-images/basicboxview-small.png "основные BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Если `VerticalOptions` и `HorizontalOptions` свойства будут удалены из `BoxView` тег или получают `Fill`, то `BoxView` становится ограничен по размеру страницы и дополняет для заполнения страницы.

Объект `BoxView` также может быть дочерним элементом `AbsoluteLayout`. В этом случае, расположение и размер `BoxView` задаются с использованием `LayoutBounds` присоединенного привязываемые свойства. `AbsoluteLayout` Описывается в статье [ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Вы увидите примеры всех этих случаях в последующих примерах программ.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Подготовка к просмотру оформление текста

Можно использовать `BoxView` для добавления некоторых простых оформление на страницах в виде горизонтальных и вертикальных линий. [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration) примере демонстрируется это. Все визуальные элементы программы определяются в **MainPage.xaml** файл, который содержит несколько `Label` и `BoxView` элементов в `StackLayout` показано ниже:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Все разметка, являющиеся дочерними для `StackLayout`. Эта разметка состоит из нескольких типов декоративного `BoxView` элементы, используемые с `Label` элемента:

[![Оформление текста](boxview-images/textdecoration-small.png "оформления текста")](boxview-images/textdecoration-large.png#lightbox "оформление текста")

Стильная заголовок в верхней части страницы достигается с `AbsoluteLayout` , дочерние элементы которого являются четыре `BoxView` элементы и `Label`, все из которых назначены определенные расположения и размеры:

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

В файле XAML `AbsoluteLayout` следуют `Label` с форматированный текст, который описывает `AbsoluteLayout`.

Можно подчеркнуть текстовой строки, заключив оба `Label` и `BoxView` в `StackLayout` с его `HorizontalOptions` значение на что-то отличное от `Fill`. Ширина `StackLayout` затем регулируется ширина `Label`, который затем применяет эту ширину на `BoxView`. `BoxView` Назначается только точные значения высоты:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Этот метод нельзя использовать на подчеркивание отдельные слова в пределах более текстовые строки или абзаца.

Можно также использовать `BoxView` выглядеть HTML `hr` (горизонтальное правило) элемент. Просто разрешить ширину `BoxView` определяется родительским контейнером, который в данном случае является `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Наконец, можно нарисовать вертикальной линии на одной стороне абзац текста путем заключения оба `BoxView` и `Label` в горизонтальной `StackLayout`. В данном случае высоту `BoxView` совпадает со значением высоты `StackLayout`, который управляется высоту `Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Список цветов с BoxView

`BoxView` Удобен для отображения цветов. Эта программа использует `ListView` Чтобы перечислить все открытые статические только для чтения поля Xamarin.Forms `Color` структуры:

[![Цвета ListView](boxview-images/listviewcolors-small.png "цвета ListView")](boxview-images/listviewcolors-large.png#lightbox "цвета ListView")

[ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/) программа включает класс с именем `NamedColor`. Статический конструктор использует отражение для доступа к все поля `Color` структуры и создать `NamedColor` объекта для каждого из них. Эти значения хранятся в статический `All` свойство:

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Визуальные элементы программы описаны в файле XAML. `ItemsSource` Свойство `ListView` присваивается статический `NamedColor.All` свойство, которое означает, что `ListView` отображает все отдельные `NamedColor` объектов:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

`NamedColor` Объекты форматируются с `ViewCell` объект, заданный как шаблон данных `ListView`. Этот шаблон включает в себя `BoxView` которого `Color` свойство привязано к `Color` свойство `NamedColor` объекта.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Игры путем создания подкласса BoxView жизненного цикла

Игра срока службы является сотовой связи машин изобретенный поля Джон конвея прогноз и Кроме того, на страницах *научных American* в 1970-х годах. Указанное представление в статье Википедии [Конвея игры срока службы](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/) программа определяет класс с именем `LifeCell` , производный от `BoxView`. Этот класс инкапсулирует логику отдельные ячейки в игру срока службы:

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` Добавляет три дополнительные свойства в `BoxView`: `Col` и `Row` свойства хранения позиции ячейки в сетке и `IsAlive` свойство указывает его состояние. `IsAlive` Свойство также задает `Color` свойство `BoxView` в черный, если ячейка находится в активном состоянии и белый Если ячейка не находится в активном состоянии.

`LifeCell` также устанавливает `TapGestureRecognizer` чтобы разрешить пользователю переключение состояния ячеек, коснувшись их. Класс преобразует `Tapped` события из распознаватель жестов в свой собственный `Tapped` событий.

**GameOfLife** также `LifeGrid` класс, который включает большую часть логики игры и `MainPage` класс, который обрабатывает визуальные элементы программы. К ним относятся наложение, который описывает правила игры. Здесь — это программа, в действии, показывающая несколько сотен `LifeCell` объектов на странице:

[![Игра жизненного цикла](boxview-images/gameoflife-small.png "игры жизни")](boxview-images/gameoflife-large.png#lightbox "игры жизненного цикла")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Создание цифровых часов

[ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/) программа создает 210 `BoxView` элементы для имитации точек проверенное отображения матричный 5 по 7. Время в книжном или альбомном режиме можно считывать, но больше, в альбомной ориентации:

[![Матричный часы](boxview-images/dotmatrixclock-small.png "часов матричный")](boxview-images/dotmatrixclock-large.png#lightbox "матричный часов")

В файле XAML немногим больше, чем создать экземпляр `AbsoluteLayout` для часов:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Все остальные встречается в файле кода. Логика отображения матричный упрощено по определению несколько массивов, описывающие точек, каждая из 10 цифр и точкой с запятой:

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Эти поля заканчиваться трехмерный массив `BoxView` элементы для хранения шаблонов точка для шести цифр.

Конструктор создает все `BoxView` элементы для цифры и двоеточия, а также инициализирует `Color` свойство `BoxView` элементы двоеточия:

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Эта программа использует относительное позиционирование и возможность изменения размера `AbsoluteLayout`. Ширина и Высота каждого `BoxView` присваиваются дробных значений, в частности 85% 1, деленное на число точек по горизонтали и вертикали. Позиции также присвоено дробных значений.

Из-за расположения и размеров относительно общего объема `AbsoluteLayout`, `SizeChanged` обработчика необходимо задать только `HeightRequest` из `AbsoluteLayout`:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

Ширина `AbsoluteLayout` автоматически получает значение, так как она растягивается на всю ширину страницы.

Окончательный код в `MainPage` класс обрабатывает обратный вызов таймера и цвета точек каждая цифра. Определение многомерных массивов в начале файла кода помогает сделать эту логику простой части программы:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```
<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>Создание аналогично часам со стрелками

Часы матричный может показаться очевидным приложения `BoxView`, но `BoxView` элементы также поддерживают реализации аналогично часам со стрелками:

[![Часы BoxView](boxview-images/boxviewclock-small.png "часы BoxView")](boxview-images/boxviewclock-large.png#lightbox "BoxView часов")

Все визуальные элементы в [ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/) программы являются потомками `AbsoluteLayout`. Эти элементы имеют размер с помощью `LayoutBounds` присоединенного свойства и повернутый с использованием `Rotation` свойство.

Три `BoxView` элементы для стрелки часов экземпляр в XAML-файле, но не располагается или размера:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

Конструктор для файла кода создается экземпляр 60 `BoxView` элементы для деления вокруг окружности часов:

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

Изменения размера и положения всех `BoxView` элементов происходит в `SizeChanged` обработчик `AbsoluteLayout`. Внутренний класс небольшой структура называется `HandParams` описывает размер каждого из трех руки относительно общего объема часы:

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

`SizeChanged` Обработчик определяет центра и радиус `AbsoluteLayout`и изменяет размер и помещает 60 `BoxView` элементы, используемые в качестве деления. `for` Цикл завершается, задав `Rotation` свойства каждого из этих `BoxView` элементов. В конце `SizeChanged` обработчик, `LayoutHand` метод вызывается с размером и расположением три стрелки часов:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

`LayoutHand` Метод размеров и размещает каждой стрелки, чтобы они указывали непосредственно до позиции, 12:00. В конце метода `AnchorY` свойству позиции, соответствующий центр часов. Это означает центра вращения.

Стрелки поворачиваются в функции обратного вызова таймера:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

Рука секунду обрабатывается немного по-разному: вносить перемещение очевидно, механическими, а не smooth применяется функция плавности анимации. На каждом этапе секунду извлекает немного и затем overshoots места назначения. Добавляет этот небольшого фрагмента кода во многом реалистичности движения.

## <a name="conclusion"></a>Заключение

`BoxView` Может показаться простой в сначала, а как вы уже видели, может быть достаточно гибким и может почти воспроизвести визуальных элементов, которые обычно возможны только с векторная графика. Для более сложных графики, обратитесь к [с помощью SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Связанные ссылки

- [Основные BoxView (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [Оформление текста (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [Цвет ListBox (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [Игра жизненного цикла (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [Матричный часов (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [Часы BoxView (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)
