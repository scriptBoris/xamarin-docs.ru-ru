---
title: Сетка Xamarin.Forms
description: В этой статье объясняется, как использовать класс Xamarin.Forms сетки для отображения представления в сетки, которые обладают строк и столбцов.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 01dd59d5e94b473316b03f9035d38305fad42880
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994506"
---
# <a name="xamarinforms-grid"></a>Сетка Xamarin.Forms

[`Grid`](xref:Xamarin.Forms.Grid) поддерживает упорядочение по строкам и столбцам представлений. Строки и столбцы можно присвоить пропорционально размеры или абсолютные размеры. `Grid` Макет не следует путать с помощью традиционных таблиц и не предназначен для представления табличных данных. `Grid` не поддерживает концепцию строки, столбца или ячейки. В отличие от таблиц HTML `Grid` предназначен исключительно для расположения содержимого.

[![](grid-images/layouts-sml.png "Макеты Xamarin.Forms")](grid-images/layouts.png#lightbox "макеты Xamarin.Forms")

В этой статье рассматривается:

- **[Назначение](#Purpose)**  &ndash; распространенные варианты применения `Grid`.
- **[Использование](#Usage)**  &ndash; способы использования `Grid` для достижения нужного проекта.
  - **[Строки и столбцы](#Rows_and_Columns)**  &ndash; определять строки и столбцы для `Grid`.
  - **[Размещение представления](#Placing_Views)**  &ndash; добавить представления сетки на определенных строках и столбцах.
  - **[Интервал между](#Spacing)**  &ndash; Настройка пробелы между строками и столбцами.
  - **[Диапазоны](#Spans)**  &ndash; настраивать элементы, охватывающая несколько строк или столбцов.

![](grid-images/grid.png "Сетка просмотра")

## <a name="purpose"></a>Цель

`Grid` можно использовать для упорядочивания представлений в сетку. Это полезно в ряде случаев:

- Упорядочивание кнопок в приложение калькулятора
- Мозаично кнопки/вариантов в виде сетки, таких как iOS или Android начальных экранов
- Упорядочивание представлений, чтобы они были одинакового размера, в одном измерении (как в некоторых панелей инструментов)

## <a name="usage"></a>Использование

В отличие от традиционных таблиц `Grid` не выводит число и размеры строк и столбцов на основе содержимого. Вместо этого `Grid` имеет `RowDefinitions` и `ColumnDefinitions` коллекций. Они содержат определения количества строк и столбцов будут размещены. Представления добавляются к `Grid` с указанной строки и столбца индексов, которая определяет строки и столбцы, представления, которые должны находиться в.

<a name="Rows_and_Columns" />

### <a name="rows-and-columns"></a>Строки и столбцы

О строках и столбцах хранятся в `Grid` `RowDefinitions`  &  `ColumnDefinitions` свойства, которые представляют собой каждой коллекции из [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) и [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)соответственно. `RowDefinition` есть одно свойство, `Height`, и `ColumnDefinition` имеется единственное свойство `Width`. Ниже приведены параметры высоты и ширины.

- **Автоматическое** &ndash; автоматически размеры в соответствии с размером в строке или столбце. Указанный в виде [ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) в C# или в качестве `Auto` в XAML.
- **Proportional(*)** &ndash; размеров столбцов и строк в виде пропорциональных долей оставшееся пространство. Значение и `GridUnitType.Star` в C# и как `#*` в XAML, с помощью `#` выполняется нужное значение. Указание одного строками и столбцами с `*` приведет к его, чтобы заполнить доступное пространство.
- **Абсолютный** &ndash; размеров столбцов и строк с конкретного, фиксированные значения высоты и ширины. Значение и `GridUnitType.Absolute` в C# и как `#` в XAML, с помощью `#` выполняется нужное значение.

> [!NOTE]
> Значения ширины для столбцов устанавливаются как "*" по умолчанию в Xamarin.Forms, которое гарантирует, что столбец будет заполнять доступное пространство.

Рассмотрим приложение, которое требуется три строки и два столбца. Нижней строке должен быть ровно 200 пкс высоту и верхней строке должен быть дважды забыв посередине. Левый столбец должен быть достаточно широким, в соответствии с содержимым и правом столбце должен заполнить оставшееся место.

В XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

В C#:

```csharp
var grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
```

<a name="Placing_Views" />

### <a name="placing-views-in-a-grid"></a>Размещение представления в виде сетки

Для размещения представления в `Grid` нужно будет добавить их как дочерние элементы в сетку, а затем укажите, какие строки и столбца принадлежат в.

В XAML, используйте `Grid.Row` и `Grid.Column` на каждого отдельного представления, чтобы указать расположение. Обратите внимание, что `Grid.Row` и `Grid.Column` укажите расположение, на основе отсчитываемый от нуля списков строк и столбцов. Это означает, что в сетку 4 x 4, используется верхняя левая ячейка — (0,0) и в нижнюю правую ячейку (3,3).

`Grid` Показанный ниже содержит четыре ячейки:

![](grid-images/label-grid.png "Сетка с четыре представления")

В XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

В C#:

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

Приведенный выше код создает сетку четыре метки, два столбца и две строки. Обратите внимание на то, что каждая метка будет иметь тот же размер, и что строки будет расширяться, чтобы использовать все доступное пространство.

В приведенном выше примере представления добавляются к [ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children) коллекции с помощью [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/) перегрузку, задающую левом и верхнем аргументы. При использовании [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) перегрузку, которая указывает влево, правой, верхней и нижней аргументы, тогда как слева и top аргументы всегда будет ссылаться в ячейки [ `Grid` ](xref:Xamarin.Forms.Grid), справа и может показаться ссылок на ячейки, которые находятся за пределами нижней аргументы `Grid`. Это обусловлено правый аргумент всегда должно быть больше левый аргумент, и аргумент нижней всегда должно быть больше верхней аргумент. В следующем примере показано эквивалентный код, используя оба `Add` перегрузки:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>Интервал

`Grid` содержит свойства для расстояния между строками и столбцами.  Следующие свойства доступны для настройки `Grid`:

- **ColumnSpacing** &ndash; пространства между столбцами.
- **RowSpacing** &ndash; пространства между строками.

Указывает следующий XAML `Grid` с двумя столбцами, одну строку и 5 точек, расстояние между столбцами:

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinitions Width="*" />
    <ColumnDefinitions Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

В C#:

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Диапазоны

Часто при работе с таблицей, то есть элемент, который должен занимать более одной строки или столбца. Рассмотрим простое приложение калькулятора.

![](grid-images/calculator.png "Calulator приложения")

Обратите внимание на то, что 0 кнопки охватывает два столбца, так же, как на встроенные калькуляторы для каждой платформы. Это достигается с помощью `ColumnSpan` свойство, которое указывает, сколько столбцов элемент должен занимать. XAML для этой кнопки:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

И в C#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Обратите внимание, что в коде, статические методы класса `Grid` класса используются для позиционирования изменения, включая изменения `ColumnSpan` и `RowSpan`. Также Обратите внимание, что, в отличие от других свойств, которые можно задать в любое время, свойства, заданные с помощью статических методов должны уже присутствовать в сетке до их изменения.

Ниже приведен полный XAML для приложения выше калькулятора:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Обратите внимание, что метки в верхней части сетки и ноль кнопки occuping более одного столбца. Несмотря на то, что разметка можно достичь с помощью вложенной сетки, `ColumnSpan`  &  `RowSpan` подход более прост.

Реализация C#:

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>Связанные ссылки

- [Создание мобильных приложений с помощью Xamarin.Forms, Глава 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Сетка](xref:Xamarin.Forms.Grid)
- [Макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Пример BusinessTumble (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
