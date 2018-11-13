---
title: Часть 2. Синтаксис Essential XAML
description: В этой статье объясняется, самых важных особенностей синтаксиса XAML из элементов свойства и вложенные свойства.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 985ca3b34b4b85ef234f12fe3f25edd1d1556e23
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563242"
---
# <a name="part-2-essential-xaml-syntax"></a>Часть 2. Синтаксис Essential XAML

_XAML предназначен главным образом для создания и инициализации объектов. Но часто необходимо задать свойства для сложных объектов, которые нельзя легко представить в виде строки XML, и иногда на дочерний класс, в котором необходимо задать свойства, определенные в одном классе. Эти две задачи требуют самых важных особенностей синтаксиса XAML элементов свойства и вложенные свойства._

## <a name="property-elements"></a>Свойства элементов

В XAML обычно задаются свойства классов как атрибуты XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Тем не менее есть альтернативный способ задать свойство в XAML. Для реализации этого подхода с `TextColor`, сначала удалите существующий `TextColor` параметр:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Откройте пустых элементов `Label` тег, разделив его на открывающий и закрывающий теги:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

В этих тегов добавьте открывающий и закрывающий теги, которые состоят из имени класса и имя свойства, разделенные точкой.

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Задайте значение свойства как содержимое этих новых тегов, следующим образом:

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

Эти два способа задания `TextColor` свойство функционально эквивалентны, но не использовать два способа для одного свойства, так как бы эффективно путем установки свойства дважды и могут быть неоднозначными.

С помощью этого нового синтаксиса могут быть введены под рукой термины:

-  `Label` — *объектного элемента*. Это объекта Xamarin.Forms, выраженный в виде XML-элемента.
-  `Text`, `VerticalOptions`, `FontAttributes` и `FontSize` являются *атрибуты свойства*. Они являются свойствами Xamarin.Forms выразить в виде атрибутов XML.
-  В этот последний фрагмент `TextColor` стала *элемент свойства*. Это свойство Xamarin.Forms, однако сейчас это XML-элемента.


Определение свойства, элементы могут в сначала кажется воспринимает как нарушение синтаксиса XML, но это не так. Период не имеет специального значения в формате XML. Декодеру XML `Label.TextColor` — просто обычный дочерний элемент.

В XAML этот синтаксис очень специфический. Одно из правил для свойств элементов — что ничего может отображаться в `Label.TextColor` тега. Значение свойства всегда определяется как содержимое между элемента свойства открывающих и закрывающих тегах.

Можно использовать синтаксис элемента свойства на более чем одно свойство:

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

На первый синтаксис элемента свойства может показаться замещающего ненужные длиннющее для сравнительно довольно простой и в этих примерах это определенно так.

Однако синтаксис элемента свойства становится жизненно важным, когда значение свойства слишком сложен, представлены в виде простой строки. Внутри тегов элемента свойства можно создать экземпляр другому объекту и задать его свойства. Например, можно явно задать свойство например `VerticalOptions` для `LayoutOptions` значение с параметрами свойств:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Еще один пример: `Grid` имеет два свойства с именем `RowDefinitions` и `ColumnDefinitions`. Эти два свойства имеют тип `RowDefinitionCollection` и `ColumnDefinitionCollection`, которые представляют собой коллекции `RowDefinition` и `ColumnDefinition` объектов. Необходимо использовать синтаксис элемента свойства для задания этих коллекций.

Здесь — это начало файла XAML для `GridDemoPage` класс, показывающий теги элемента свойства для `RowDefinitions` и `ColumnDefinitions` коллекций:

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

Обратите внимание, что сокращенный синтаксис для определения автоматическим размером ячейки, ячейки пикселей ширины и высоты и параметры типа "звезда".

## <a name="attached-properties"></a>Вложенные свойства

Вы увидели, `Grid` требует элементы свойства для `RowDefinitions` и `ColumnDefinitions` коллекций для определения строк и столбцов. Тем не менее, также необходимо каким-либо образом для программиста для указания строки и столбца, где каждый дочерний элемент элемента `Grid` находится.

Внутри тега для каждого дочернего элемента `Grid` указываются в строке и столбце этого дочернего элемента, используя следующие атрибуты:

-  `Grid.Row`
-  `Grid.Column`

Значения по умолчанию эти атрибуты являются 0. Можно также указать, если дочерний элемент охватывает более одной строки или столбца со следующими атрибутами:

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

`Grid.Row` И `Grid.Column` параметры 0 не являются обязательными, но обычно включаются для большей ясности.

Вот, как выглядит на всех трех платформах:

[![](essential-xaml-syntax-images/griddemo.png "Макет сетки")](essential-xaml-syntax-images/griddemo-large.png#lightbox "макет сетки")

Судя исключительно из синтаксиса, они `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, и `Grid.ColumnSpan` атрибуты отображаются как статические поля или свойства `Grid`, но интересно, `Grid` не определяет никаких данных с именем `Row`, `Column`, `RowSpan`, или `ColumnSpan`.

Вместо этого `Grid` определяет четыре привязываемые свойства с именем `RowProperty`, `ColumnProperty`, `RowSpanProperty`, и `ColumnSpanProperty`. Это специальные типы привязки свойств, известный как *присоединенных свойств*. Они определяются `Grid` класс, но задать для дочерних элементов `Grid`.

Если вы хотите использовать эти вложенные свойства в коде, `Grid` класс предоставляет статические методы с именем `SetRow`, `GetColumn`, и т. д. Но в XAML, эти вложенные свойства заданы как атрибуты в дочерние элементы `Grid` использование имен простых свойств.

Присоединенные свойства являются всегда распознается в файлы XAML как атрибуты, содержащий класс и имя свойства, разделенные точкой. Они называются *присоединенных свойств* так, как они определяются одного класса (в этом случае `Grid`), но присоединен к другим объектам (в данном случае дочерних элементов `Grid`). Во время структурирования `Grid` можно запросить значения этих вложенных свойств, чтобы знать, куда поместить каждый дочерний элемент.

`AbsoluteLayout` Класс определяет два подключаемых свойства с именем `LayoutBounds` и `LayoutFlags`. Вот шахматной реализовать с помощью пропорционально позиционирования и возможности изменения размера `AbsoluteLayout`:

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

А вот она:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Абсолютный макет")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "абсолютный макет")

Для нечто подобное могут возникнуть вопросы на подсказку с помощью XAML. Конечно повторение и непрерывную `LayoutBounds` прямоугольник предполагает, что он может лучше реализовать в коде.

Это определенно обоснованная озабоченность, и нет никаких проблем с балансировкой работу с кодом и разметкой, при определении интерфейсов пользователя. Это легко определить определенных визуальных элементов в XAML и затем использовать конструктор файл с выделенным кодом для добавления некоторые дополнительные визуальные элементы, которые могут быть созданы лучше в циклах.

## <a name="content-properties"></a>Свойства содержимого

В предыдущих примерах `StackLayout`, `Grid`, и `AbsoluteLayout` объектов задается по `Content` свойство `ContentPage`, и они легко они фактически элементов в `Children` коллекции. Но эти `Content` и `Children` свойства являются нигде не обрабатываются в файле XAML.

Конечно, можно включить `Content` и `Children` свойства как свойства элементов, таких как **XamlPlusCode** пример:

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

Является возник такой вопрос: Почему представляют собой эти элементы свойства *не* в файле XAML требуется?

Элементы, определенные в Xamarin.Forms для использования в XAML должен иметь одно свойство, отмеченные в `ContentProperty` атрибут класса. Если просмотреть `ContentPage` класс в электронной документации Xamarin.Forms, вы увидите этот атрибут:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Это означает, что `Content` теги элементов свойства не являются обязательными. Любое содержимое XML, расположенный между начальной и конечной `ContentPage` предполагается назначить теги `Content` свойство.

 `StackLayout`, `Grid`, `AbsoluteLayout`, и `RelativeLayout` являются производными от класса `Layout<View>`, и если просмотреть `Layout<T>` в документации по Xamarin.Forms, вы увидите другой `ContentProperty` атрибут:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Позволяющий содержимое макета автоматически добавляться `Children` коллекции без явного `Children` теги элемента свойства.

Другие классы также имеют `ContentProperty` атрибут определения. Например, свойство содержимого `Label` является `Text`. Проверьте документацию по API для других пользователей.

## <a name="platform-differences-with-onplatform"></a>Различия между платформами с OnPlatform

В одностраничных приложений характерно для задания `Padding` свойство на странице, чтобы избежать перезаписи строки состояния iOS. В коде, можно использовать `Device.RuntimePlatform` свойства для этой цели:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Вы также можете выполнить что-то подобное в XAML с помощью `OnPlatform` и `On` классы. Во-первых, включите элементы свойства для `Padding` свойство в верхней части страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

В этих тегах включают `OnPlatform` тега. `OnPlatform` — Это универсальный класс. Необходимо указать аргумент универсального типа, в этом случае `Thickness`, который является типом `Padding` свойство. К счастью, есть атрибут XAML, в частности, для определения универсальных аргументов в вызове `x:TypeArguments`. Это должен совпадать с типом свойства, которое вы изменяете:

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

`OnPlatform` имеет свойство, именуемое `Platforms` то есть `IList` из `On` объектов. Используйте теги элементов свойства для этого свойства:

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

Можно упростить эту разметку. Свойство содержимого `OnPlatform` является `Platforms`, поэтому можно удалить эти теги элемента свойства:

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

`Platform` Свойство `On` имеет тип `IList<string>`, поэтому если значения совпадают, и вы можете включить несколько платформ:

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

Так как Android и UWP присваивается значение по умолчанию `Padding`, что тег можно будет удалить:

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

Это стандартный способ задать зависящее от платформы `Padding` свойства в XAML. Если `Value` параметр не может быть представлено одной строки, свойств элементов можно определить для него:

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

С помощью свойств элементов и вложенные свойства большая часть основной синтаксис XAML установлено. Тем не менее иногда необходимо задать свойства к объектам косвенных способом, например, из словаря ресурсов. Этот подход описан в следующей части часть [3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Часть 5. От привязки данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
