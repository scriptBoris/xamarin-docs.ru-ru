---
title: Часть 2. Синтаксис Essential XAML
description: В этой статье описаны важные функции синтаксис XAML свойства элементов и вложенных свойств.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: ea448708403478cdaf565bc7abbed2af28c7a9bf
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268853"
---
# <a name="part-2-essential-xaml-syntax"></a>Часть 2. Синтаксис Essential XAML

_XAML главным образом предназначен для создания и инициализации объектов. Но часто, необходимо задать свойства для сложных объектов, которые невозможно легко представить в виде строки XML и иногда в дочернем классе, в котором необходимо задать свойства, определенные один класс. Эти два потребности требуют основные функции проверки синтаксиса XAML свойства элементов и вложенных свойств._

## <a name="property-elements"></a>Свойства элементов

В языке XAML обычно задаются свойства классов как атрибуты XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Тем не менее является альтернативным способом задания свойства в XAML. Чтобы повторить этот вариант с `TextColor`, сначала удалите существующие `TextColor` параметр:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Откройте пустой элемент `Label` тег по разделению открывающий и закрывающий теги:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

В эти теги добавьте открывающий и закрывающий теги, содержащая имя класса и имя свойства, разделенных точкой.

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Установите значение свойства в качестве содержимого этих новых тегов, следующим образом:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Эти два способа указать `TextColor` свойство функционально эквивалентны, но не использовать два способа для одного свойства, поскольку, будет фактически путем установки свойства дважды, а также могут быть неоднозначными.

С помощью этого нового синтаксиса могут быть введены под рукой термины:

-  `Label` — *элемента объекта*. Это Xamarin.Forms объекта, выраженный в виде XML-элемента.
-  `Text`, `VerticalOptions`, `FontAttributes` и `FontSize` , *атрибуты свойства*. Они являются свойствами Xamarin.Forms выразить в виде атрибутов XML.
-  В этот последний фрагмент `TextColor` стала *элемент свойства*. Это свойство Xamarin.Forms, но теперь он является элементом XML.


Определение свойства, которое может элементы в сначала казаться, что нарушение синтаксис XML, но это не так. Период не имеет особого смысла в формате XML. Декодер XML `Label.TextColor` — просто обычный дочерний элемент.

В языке XAML этот синтаксис является очень специальных. Одно из правил для свойств элементов — что ничего могут появиться в `Label.TextColor` тег. Значение свойства всегда определяется как содержимое между элемента свойства открывающих и закрывающих тегах.

Можно использовать синтаксис элемента свойства на более одного свойства:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Или можно использовать синтаксис элемента свойства для всех свойств:

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

Сначала синтаксис элемента свойства может показаться ненужной long-winded замены для сравнительно довольно простой, и в этих примерах это определенно так.

Однако синтаксис элемента свойства становится essential, если значение свойства слишком сложен для быть выражен как простая строка. В тегах элемента свойства можно создать другого объекта и задать его свойства. Например, можно задать свойство например `VerticalOptions` для `LayoutOptions` значение с параметрами свойств:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Другой пример: `Grid` имеет два свойства с именем `RowDefinitions` и `ColumnDefinitions`. Эти свойства имеют тип `RowDefinitionCollection` и `ColumnDefinitionCollection`, которые представляют собой коллекции `RowDefinition` и `ColumnDefinition` объектов. Необходимо использовать синтаксис элемента свойства для задания этих коллекций.

Ниже приведен в начало файла XAML для `GridDemoPage` классов, показывающая теги элементов свойства для `RowDefinitions` и `ColumnDefinitions` коллекции:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Обратите внимание, сокращенный синтаксис для определения размера автоматически ячейки, ячейки пикселей ширины и высоты и параметры типа «звезда».

## <a name="attached-properties"></a>Вложенные свойства

Было показано, `Grid` требуются свойства элементов для `RowDefinitions` и `ColumnDefinitions` коллекций для определения строк и столбцов. Тем не менее, также должен быть способ программиста для указания строк и столбцов где каждого дочернего элемента `Grid` находится.

Внутри тега для каждого дочернего элемента `Grid` укажите строку и столбец этого дочернего элемента, используя следующие атрибуты:

-  `Grid.Row`
-  `Grid.Column`

Значения этих атрибутов по умолчанию — 0. Можно также указать, если дочерний элемент охватывает более одной строки или столбца со следующими атрибутами:

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Эти атрибуты имеют значения по умолчанию 1.

Ниже приведен полный файл GridDemoPage.xaml.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

`Grid.Row` И `Grid.Column` параметры 0 не являются обязательными, но включены для большей ясности.

Вот, как оно выглядит на всех трех платформах:

[![](essential-xaml-syntax-images/griddemo.png "Макет сетки")](essential-xaml-syntax-images/griddemo-large.png#lightbox "макет сетки")

Судя исключительно с помощью синтаксиса, они `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, и `Grid.ColumnSpan` атрибуты отображаются как статические поля или свойства `Grid`, но что интересно, `Grid` не определяет никаких действий с именем `Row`, `Column`, `RowSpan`, или `ColumnSpan`.

Вместо этого `Grid` определяет четыре привязываемые свойства с именем `RowProperty`, `ColumnProperty`, `RowSpanProperty`, и `ColumnSpanProperty`. Специальные типы привязываемые свойства, известный как *вложенные свойства*. Они определяются `Grid` класс, но установить дочерними объектами `Grid`.

Если требуется использовать вложенные свойства в коде, `Grid` класс предоставляет статические методы с именем `SetRow`, `GetColumn`, и т. д. Но в XAML, эти вложенные свойства заданы как атрибуты в дочерних `Grid` с использованием имен простых свойств.

Вложенные свойства всегда находятся распознаваемые файлы XAML как атрибуты, содержащий класс и имя свойства, разделенных точкой. Они называются *вложенные свойства* так, как они определяются один класс (в этом случае `Grid`), но вложен в другие объекты (в данном случае дочерних элементов `Grid`). Во время структурирования `Grid` можно запрашивать значения этих вложенных свойств, чтобы знать, куда поместить каждый дочерний элемент.

`AbsoluteLayout` Класс определяет две вложенные свойства с именем `LayoutBounds` и `LayoutFlags`. Ниже приведен шахматной реализуется с помощью пропорционально позиционирования и возможности изменения размера `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

И здесь:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Абсолютный макета")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "абсолютного макета")

Для примерно следующим образом могут возникнуть вопросы на подсказку с помощью XAML. Конечно повторение и непрерывную `LayoutBounds` прямоугольник предполагает, что он может лучше реализовать в коде.

Это определенно допустимые значения и нет ничего страшного балансировки использования кода и разметки, при определении интерфейсов пользователя. Можно легко определить некоторые визуальные элементы в XAML, а затем использовать конструктор для файла кода программной Добавление больше визуальных элементов, которые могут быть созданы в циклах лучше.

## <a name="content-properties"></a>Свойства содержимого

В предыдущих примерах `StackLayout`, `Grid`, и `AbsoluteLayout` объектов задается по `Content` свойство `ContentPage`, и эти структуры они фактически элементов в `Children` коллекции. Но эти `Content` и `Children` свойства являются нигде в XAML-файле.

Конечно, могут включать `Content` и `Children` свойства как свойства элементов, таких как **XamlPlusCode** образца:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Является вопрос: почему такие элементы этих свойств *не* в XAML-файл?

Элементы, определенные в Xamarin.Forms для использования в языке XAML, могут иметь одно свойство, отмеченные в `ContentProperty` атрибут в классе. Если просмотреть `ContentPage` класса в электронную документацию Xamarin.Forms, вы увидите этот атрибут:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Это означает, что `Content` теги элементов свойства не являются обязательными. Все содержимое XML, расположенный между начальной и конечной `ContentPage` теги предполагается назначить `Content` свойство.

 `StackLayout`, `Grid`, `AbsoluteLayout`, и `RelativeLayout` являются производными от `Layout<View>`, и если можно искать `Layout<T>` в документации по Xamarin.Forms, вы увидите другой `ContentProperty` атрибута:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Что позволяет содержимому структуры, который будет автоматически добавляться в `Children` коллекции без явного `Children` теги элементов свойства.

Другие классы также имеют `ContentProperty` определения атрибута. Например, свойство содержимого `Label` — `Text`. Проверьте документацию по API для других пользователей.

## <a name="platform-differences-with-onplatform"></a>Различия между платформами с OnPlatform

В приложениях на одной странице, являются общими для задания `Padding` свойство на странице, чтобы избежать перезаписи в строке состояния операций ввода-вывода. В коде, можно использовать `Device.RuntimePlatform` свойства для этой цели:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Также можно выполнить аналогичное действие в XAML с помощью `OnPlatform` и `On` классы. Сначала включить элементы свойства для `Padding` свойство в верхней части страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

В этих тегах включают `OnPlatform` тег. `OnPlatform` — Это универсальный класс. Необходимо указать аргумент универсального типа, в этом случае `Thickness`, который является типом `Padding` свойство. К счастью, имеется атрибут XAML, в частности, для определения универсальных аргументов в вызове `x:TypeArguments`. Это должно совпадать с типом свойства, которое выполняется:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` имеет свойство с именем `Platforms` , `IList` из `On` объектов. Используйте теги элементов свойства для этого свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Теперь добавьте `On` элементов. Для каждого из них значение `Platform` свойство и `Value` свойство разметке для `Thickness` свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Эта разметка может быть упрощено. Свойство содержимого `OnPlatform` — `Platforms`, поэтому можно удалить эти теги элементов свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`Platform` Свойство `On` относится к типу `IList<string>`, поэтому, если значения совпадают, может включать несколько платформ:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Так как значение по умолчанию значение Android и UWP `Padding`, что можно удалить тег:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Это стандартный способ задать зависят от платформы `Padding` свойства в XAML. Если `Value` параметр не может быть представлен одной строки, свойств элементов можно определить для него:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

## <a name="summary"></a>Сводка

С помощью свойств элементов и присоединенные свойства большая часть основной синтаксис XAML было установлено. Однако иногда необходимо задать свойства к объектам косвенных способом, например, из словаря ресурсов. Этот подход, описанные в следующей части часть [3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Часть 5. Из привязка данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
