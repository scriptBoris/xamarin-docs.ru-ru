---
title: Xamarin.Forms BoxView
description: В этой статье описываются способы использования цветным прямоугольником для оформления, графику и взаимодействие в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
ms.openlocfilehash: 3ae2fb8110b7e0a5c6c85c489897acc1a03be8d8
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "38997056"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[`BoxView`](xref:Xamarin.Forms.BoxView) Отображает простой прямоугольник заданную ширину, высоту и цвет. Можно использовать `BoxView` для оформления, элементарные графики, а также для взаимодействия с пользователем с помощью сенсорного ввода.

Поскольку Xamarin.Forms не имеет встроенных вектор графической системе, `BoxView` помогает соответствующим образом. Некоторые из примеров программ, описанные в этой статье подразумевают использование `BoxView` для рендеринга графики. `BoxView` Можно размера, чтобы он напоминал строки заданной ширины и толщины, а затем (повернутый) с помощью любой угол `Rotation` свойство.

Несмотря на то что `BoxView` может имитировать простой графикой, может потребоваться изучение [использование SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) для более сложных требований графики.

В этой статье рассматриваются следующие темы:

- **[Параметр BoxView цвет и размер](#colorandsize)**  &ndash; задать `BoxView` свойства.
- **[Оформление текста отрисовки](#textdecorations)**  &ndash; использовать `BoxView` для отрисовки линий.
- **[Список цветов с BoxView](#listingcolors)**  &ndash; отображения всех системных цветов в `ListView`.
- **[Воспроизведение игры жизнь путем создания подклассов BoxView](#subclassing)**  &ndash; реализовать знаменитые сотовой связи недетерминированного.
- **[Создание цифровых часов](#digitalclock)**  &ndash; имитировать отображения матричный.
- **[Создание часов аналоговый](#analogclock)**  &ndash; преобразовывать и анимировать `BoxView` элементов.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Параметр BoxView цвет и размер

Обычно необходимо настроить следующие свойства `BoxView`:

- [`Color`](xref:Xamarin.Forms.BoxView.Color) Чтобы указать ее цвет.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) Чтобы задать его радиус углов.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Чтобы задать ширину `BoxView` в аппаратно независимых единицах.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Чтобы задать высоту `BoxView`.

`Color` Свойство имеет тип `Color`; свойство можно присвоить любой `Color` значение, включая 141 статические поля только для чтения класса с именем цвета в алфавитном порядке от `AliceBlue` для `YellowGreen`.

`CornerRadius` Свойство имеет тип [ `CornerRadius` ](xref:Xamarin.Forms.CornerRadius); свойство может быть задано в одном `double` uniform значение радиуса угла, или `CornerRadius` структуры, определяемый четырьмя `double` значения, которые применяются к вверху слева, сверху справа, снизу слева и нижней правой части `BoxView`.

`WidthRequest` И `HeightRequest` свойства играют роль, только если `BoxView` — *неограниченного* в макете. Это происходит, когда контейнер макета должен знать дочернего элемента размер, например, при `BoxView` является дочерним элементом автоматическим размером ячейки в `Grid` макета. Объект `BoxView` тоже без ограничений, когда его `HorizontalOptions` и `VerticalOptions` свойства присваиваются значения, отличные от `LayoutOptions.Fill`. Если `BoxView` — без ограничений, но `WidthRequest` и `HeightRequest` свойства не заданы, то ширина или высота присваиваются значения по умолчанию 40 единиц или примерно 1/4 дюйма на мобильных устройствах.

`WidthRequest` И `HeightRequest` свойства учитываются, если `BoxView` — *ограниченного* в макете, в котором случае контейнер макета налагает на свой собственный размер `BoxView`.

Объект `BoxView` может быть ограничен в одно измерение и без ограничений в другой. Например если `BoxView` является дочерним для вертикального `StackLayout`, по вертикали `BoxView` — без ограничений, и его горизонтальный размер обычно ограничен. Однако существуют исключения для горизонтального измерения: Если `BoxView` имеет его `HorizontalOptions` свойство, значение, отличное от `LayoutOptions.Fill`, горизонтальный размер также будет без ограничений. Существует также возможность `StackLayout` сам иметь неограниченный горизонтальный размер, в этом случае `BoxView` также будет горизонтально без ограничений.

[ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView) пример отображает один дюймовый квадрат без ограничений `BoxView` в центре его страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Ниже приведен результат.

[![Основные BoxView](boxview-images/basicboxview-small.png "основные BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Если `VerticalOptions` и `HorizontalOptions` свойства будут удалены из `BoxView` тег или приводятся к `Fill`, то `BoxView` становится ограничением размера страницы и разворачивается для заполнения страницы.

Объект `BoxView` также может быть дочерним элементом `AbsoluteLayout`. В этом случае, расположение и размер `BoxView` задаются с помощью `LayoutBounds` присоединенного связываемые свойства. `AbsoluteLayout` Рассматривается в статье [ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Вы увидите примеры всех этих случаях в последующих примерах программ.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Подготовка к просмотру оформление текста

Можно использовать `BoxView` для добавления некоторых простых оформление на страницах в виде горизонтальных и вертикальных линий. [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration) в нем демонстрируется это. Все визуальные элементы программы определенные в **MainPage.xaml** файл, который содержит несколько `Label` и `BoxView` элементов в `StackLayout` показано ниже:

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

Все разметка, являющиеся дочерними для `StackLayout`. Эта разметка состоит из нескольких типов фигурных `BoxView` элементы, используемые с `Label` элемент:

[![Оформление текста](boxview-images/textdecoration-small.png "оформление текста")](boxview-images/textdecoration-large.png#lightbox "оформление текста")

Стильный заголовка в верхней части страницы достигается за счет `AbsoluteLayout` дочерние элементы которого четыре `BoxView` элементов и `Label`, все из которых назначен определенные расположения и размеры:

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

Можно подчеркнуть строку текста путем заключения оба `Label` и `BoxView` в `StackLayout` с его `HorizontalOptions` значение значение, отличное от `Fill`. Ширина `StackLayout` затем регулируется ширина `Label`, который затем налагает это на `BoxView`. `BoxView` Назначается только точные значения высоты:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Этот метод нельзя использовать на подчеркивание отдельные слова в пределах более длинные строки текста или абзаца.

Можно также использовать `BoxView` , чтобы он напоминал HTML `hr` элемент (горизонтальная линия). Просто разрешить ширину `BoxView` определяется его родительского контейнера, который в данном случае является `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Наконец, можно нарисовать вертикальную линию на одной стороне абзац текста путем заключения оба `BoxView` и `Label` в горизонтальной `StackLayout`. В данном случае высоту `BoxView` совпадает со значением высоты `StackLayout`, который регулируется высота `Label`:

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

`BoxView` Удобное отображение цветов. Эта программа использует `ListView` Чтобы вывести список все открытые статические только для чтения поля Xamarin.Forms `Color` структуры:

[![Цвета ListView](boxview-images/listviewcolors-small.png "цвета ListView")](boxview-images/listviewcolors-large.png#lightbox "цвета ListView")

[ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/) программа включает класс с именем `NamedColor`. Статический конструктор использует отражение для доступа к все поля `Color` структурировать и создать `NamedColor` объекта для каждого из них. Эти значения хранятся в статический `All` свойство:

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

Визуальные элементы программы, описаны в файле XAML. `ItemsSource` Свойство `ListView` присваивается статический `NamedColor.All` свойство, которое означает, что `ListView` отображает все отдельные `NamedColor` объектов:

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

`NamedColor` Объекты форматируются с `ViewCell` объект, который задан в качестве шаблона данных `ListView`. Этот шаблон включает `BoxView` которого `Color` свойство привязано к `Color` свойство `NamedColor` объекта.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Играя в игру существования путем создания подклассов BoxView

Игра срока службы является сотовой связи недетерминированного разработанная компанией по имени математика Джорджа Конвея Джона и Кроме того, на страницах *научных American* в 1970-х годах. О чем идет речь, предоставляемые статье Википедии [Конвея игры of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/) программа определяет класс с именем `LifeCell` , наследуемый от класса `BoxView`. Этот класс инкапсулирует логику отдельную ячейку в игре of Life:

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

`LifeCell` Добавляет три дополнительные свойства для `BoxView`: `Col` и `Row` свойства хранения положение ячейки в сетке и `IsAlive` свойство указывающее его состояние. `IsAlive` Также задает свойство `Color` свойство `BoxView` черный цвет, если ячейка находится в активном состоянии и белым Если ячейка не содержится в активном состоянии.

`LifeCell` также устанавливает `TapGestureRecognizer` разрешение на переключение состояния ячеек, коснувшись их. Этот класс преобразует `Tapped` событий из средства распознавания в свой собственный `Tapped` событий.

**GameOfLife** также `LifeGrid` класс, который инкапсулирует большую часть логики игры и `MainPage` класс, который обрабатывает визуальные элементы программы. К ним относятся наложение, описывающий правила игры. Вот программа в действии, показывающий несколько сотен `LifeCell` объектов на странице:

[![Игра жизни](boxview-images/gameoflife-small.png "игры жизненного цикла")](boxview-images/gameoflife-large.png#lightbox "игры жизненного цикла")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Создание цифровых часов

[ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/) программа создает 210 `BoxView` элементы для имитации точки доброе отображения матричный 5 по 7. Вы найдете время в книжном или альбомном режиме, но он больше в альбомной ориентации:

[![Часы матричный](boxview-images/dotmatrixclock-small.png "часы матричный")](boxview-images/dotmatrixclock-large.png#lightbox "матричный часов")

Файл XAML немногим больше, чем создать экземпляр `AbsoluteLayout` для часов:

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

Все остальное происходит в файле кода. Логику отображения матричный значительно упростилось по определению несколько массивов, которые описывают точек, каждая из 10 цифр и двоеточие:

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

Эти поля завершу статью трехмерный массив `BoxView` элементов для хранения шаблонов точка для шести цифр.

Конструктор создает все `BoxView` элементы для цифр и двоеточие, а также инициализирует `Color` свойство `BoxView` элементы для него:

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

Эта программа использует относительное позиционирование и функция изменения размера `AbsoluteLayout`. Ширина и Высота каждого `BoxView` присваивается дробных значений, в частности 85% 1, деленное на количество точек. Положения также присвоено дробные значения использовать нельзя.

Так как все положения и размеры задаются относительно общего объема `AbsoluteLayout`, `SizeChanged` обработчика необходимо только установить `HeightRequest` из `AbsoluteLayout`:

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

Последний код в `MainPage` класс обрабатывает обратный вызов таймера и цвета точек каждая цифра. Определение многомерных массивов в начале файла кода помогает сделать эту логику самой простой частью программы:

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

Часы матричный может показаться очевидным применения `BoxView`, но `BoxView` элементы также способны реализации аналогично часам со стрелками:

[![Часы BoxView](boxview-images/boxviewclock-small.png "часы BoxView")](boxview-images/boxviewclock-large.png#lightbox "BoxView часов")

Все визуальные элементы в [ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/) программы являются дочерними элементами `AbsoluteLayout`. Эти элементы имеют размер с помощью `LayoutBounds` вложенного свойства зависимостей и повернутый с использованием `Rotation` свойство.

Три `BoxView` для стрелки часов создан в файле XAML, но не расположен или элементов размера:

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

Конструктор файла кода создает 60 `BoxView` элементы для делений вокруг длины окружности часов:

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

Определения размеров и положения всех `BoxView` элементы происходит в `SizeChanged` обработчик для `AbsoluteLayout`. Внутренний класс небольшая структура называется `HandParams` описывает размер каждого из трех руки относительно общего объема часы:

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

`SizeChanged` Обработчик определяет центра и радиус `AbsoluteLayout`и изменяет размер и помещает 60 `BoxView` элементы, используемые в качестве меток делений. `for` Цилк, задав `Rotation` свойства каждого из этих `BoxView` элементов. В конце `SizeChanged` обработчик, `LayoutHand` был вызван с размером и расположением три стрелки часов:

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

`LayoutHand` Метод размерами и положениями каждой стрелки, чтобы они указывали непосредственно до позиции, 12:00. В конце метода `AnchorY` свойству положение, соответствующее center часов. Это указывает центр вращения.

Руки меняются в функции обратного вызова таймера:

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

Немного по-разному обрабатывается секундной стрелки: функция для реалистичной анимации анимация применяется к задать перемещение кажется, механическими, а не smooth. На каждом этапе секундной стрелки стягивает немного и затем overshoots места назначения. Этот небольшой фрагмент кода добавляет гораздо реализм движения.

## <a name="conclusion"></a>Заключение

`BoxView` Может показаться простой в, во-первых, но как вы убедились, это может быть весьма гибким, и можно почти воспроизведения визуальных элементов, которые обычно возможны только с векторную графику. Для более сложных рисунков, обратитесь к [использование SkiaSharp в Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Связанные ссылки

- [Основные BoxView (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [Оформление текста (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [Цвет ListBox (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [Игра жизненного цикла (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [Матричный часов (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [Часы BoxView (пример)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](xref:Xamarin.Forms.BoxView)
