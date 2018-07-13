---
title: Использование расширения разметки XAML
description: В этой статье описывается использование расширения разметки XAML Xamarin.Forms для улучшения возможности и гибкость XAML, позволяя атрибутов элемента из различных источников.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 6f0c15976871129362fb3d6d3287215d1fba2cb9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995986"
---
# <a name="consuming-xaml-markup-extensions"></a>Использование расширения разметки XAML

Расширения разметки XAML для повышения возможности и гибкость XAML, позволяя атрибутов элемента из различных источников. Несколько расширений разметки XAML являются частью спецификации XAML 2009 г. Они отображаются в файлах XAML с обычной `x` префикс пространства имен того они часто ссылаются с этого префикса. Они описаны в следующих разделах:

- [`x:Static`](#static) &ndash; ссылаться на статические свойства, поля или члены перечисления.
- [`x:Reference`](#reference) &ndash; ссылка на именованные элементы на странице.
- [`x:Type`](#type) &ndash; значение атрибута `System.Type` объекта.
- [`x:Array`](#array) &ndash; Создает массив объектов определенного типа.
- [`x:Null`](#null) &ndash; значение атрибута `null` значение.

Три других расширений разметки XAML Исторически поддерживаются в других реализациях XAML и также поддерживаются в Xamarin.Forms. Более подробно они описаны в других статьях:

- `StaticResource` &ndash; ссылок на объекты из словаря ресурсов, как описано в статье [ **словари ресурсов**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; реагирование на изменения в объекты в словаре ресурсов, как описано в статье [ **динамические стили**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; установить связь между свойствами из двух объектов, как описано в статье [ **привязки данных**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; выполняет привязку данных на основе шаблона элемента управления, как описано в статье [**привязки из шаблона элемента управления**] / направляющие/xamarin-forms/приложения основы/шаблоны/управления шаблонов/привязки шаблонов /)

[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) Макета используется пользовательское расширение разметки [ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression). В этой статье описан данного расширения разметки [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Расширение разметки x:Static

`x:Static` Расширение разметки поддерживается [ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension) класса. Класс имеет одно свойство с именем [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) типа `string` присвоено имя общедоступная константа, статическое свойство, статическое поле или член перечисления.

Один из распространенных способов использования `x:Static` является сначала определить класс с некоторыми константы или статические переменные, такие как эта маленькая `AppConstants` в класс [ **расширений MarkupExtension** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/) программы:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

**X: Static Демонстрация** страница демонстрирует несколько способов использования `x:Static` расширение разметки. Создает самый детальный подход `StaticExtension` класса между `Label.FontSize` теги элемента свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

Средство синтаксического анализа XAML также позволяет `StaticExtension` класса, чтобы сократить до `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Это можно упростить еще дальше, но изменение вводит некоторые новый синтаксис: он состоит из помещения `StaticExtension` классов и членов, установив в фигурные скобки. Результирующее выражение имеет значение непосредственно в `FontSize` атрибут:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Обратите внимание, что *не* кавычки в фигурные скобки. `Member` Свойство `StaticExtension` больше не является XML-атрибут. Вместо этого он является частью выражения для расширения разметки.

Так же, как вы можете сократить `x:StaticExtension` для `x:Static` при использовании как объектный элемент, вы также можете сократить его в выражение в фигурных скобках:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

`StaticExtension` Класс имеет `ContentProperty` атрибут ссылки на свойство `Member`, что означает пометку это свойство как свойство содержимого класса по умолчанию. Для расширения разметки XAML, выраженными с помощью фигурных скобок, то можно исключить `Member=` часть выражения:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Это наиболее распространенная форма `x:Static` расширение разметки.

**Статический Демонстрация** страница содержит два примера. Корневой тег файла XAML содержит объявление пространства имен XML для .NET `System` пространство имен:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Это позволяет `Label` размер шрифта равным этому статическому полю `Math.PI`. Результаты шрифтом довольно мала, поэтому `Scale` свойство имеет значение `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Последний пример отображает `Device.RuntimePlatform` значение. `Environment.NewLine` Статическое свойство используется для вставки символа новой строки между двумя `Span` объектов:

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Ниже приведен пример, запущенный на всех трех платформах.

[![Демонстрация x: Static](consuming-images/staticdemo-small.png "Демонстрация x: Static")](consuming-images/staticdemo-large.png#lightbox "Демонстрация x: Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>расширение разметки x:Reference

`x:Reference` Расширение разметки поддерживается [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) класса. Класс имеет одно свойство с именем [ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) типа `string` присвоено имя элемента на странице, которая дала имя с `x:Name`. Это `Name` свойство является свойством содержимого из `ReferenceExtension`, поэтому `Name=` является не обязательным, если `x:Reference` отображается в фигурных скобках.

`x:Reference` Расширение разметки используется исключительно с привязки данных, которые описаны более подробно в статье [ **привязки данных**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

**X: Reference Демонстрация** страницы показано два варианта использования `x:Reference` с привязками данных, первый, где он используется для задания `Source` свойство `Binding` объекта, а второе — где она используется для задания `BindingContext` свойство для привязки данных:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Оба `x:Reference` выражения используйте сокращенную версию `ReferenceExtension` имя класса и исключить `Name=` часть выражения. В первом примере `x:Reference` расширение разметки, внедренного в `Binding` расширение разметки. Обратите внимание, что `Source` и `StringFormat` параметров разделяются запятыми. Вот ее запуск на всех трех платформах:

[![Демонстрация x: Reference](consuming-images/referencedemo-small.png "Демонстрация x: Reference")](consuming-images/referencedemo-large.png#lightbox "Демонстрация x: Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>Расширение разметки x:Type

`x:Type` Расширение разметки является эквивалентом XAML C# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) ключевое слово. Этот режим поддерживается [ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension) класс, который определяет одно свойство с именем [ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) типа `string` присваивается имя класса или структуры. `x:Type` Возвращает расширение разметки [ `System.Type` ](xref:System.Type) объект этого класса или структуры. `TypeName` является свойством содержимого из `TypeExtension`, поэтому `TypeName=` является не обязательным, если `x:Type` отображается с фигурными скобками.

В Xamarin.Forms, имеют несколько свойств, имеющие аргументы типа `Type`. К ним относятся [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство `Style`и [x: TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) атрибут, используемый для указания аргументов в универсальных классах. Тем не менее, средство синтаксического анализа XAML выполняет `typeof` операции автоматически и `x:Type` расширения разметки не используется в таких случаях.

Централизованный где `x:Type` *—* требуется — с помощью `x:Array` расширения разметки, который описан в [разделу](#array).

`x:Type` Расширение разметки удобно использовать при создании меню, где каждый элемент меню соответствует объекта определенного типа. Можно связать `Type` с каждым пунктом меню и затем создать экземпляр объекта, при выборе пункта меню.

Это как меню навигации в `MainPage` в **расширения разметки** программы works. **MainPage.xaml** файл содержит `TableView` с каждым `TextCell` соответствующий определенной страницы в программе:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Ниже приведен на главную страницу открытия в **расширения разметки**:

[![Main страницы](consuming-images/mainpage-small.png "Main страницы")](consuming-images/mainpage-large.png#lightbox "главной страницы")

Каждый `CommandParameter` свойству `x:Type` расширение разметки, которое ссылается на один из других страниц. `Command` Свойство привязано к свойству с именем `NavigateCommand`. Это свойство определено в `MainPage` файл с выделенным кодом:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

`NavigateCommand` Свойство `Command` объектом, реализующим команду execute с аргументом типа `Type` &mdash; значение `CommandParameter`. Данный метод использует `Activator.CreateInstance` для создания экземпляра на странице, а затем переходит к нему. Конструктор завершает, задав `BindingContext` страницы к самому себе, что позволяет `Binding` на `Command` для работы. См. в разделе [ **привязки данных** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) статьи и особенно [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) Дополнительные сведения об этом типе кода.

**X: Type Демонстрация** странице используется похожий прием для создания экземпляра элементов Xamarin.Forms и добавить их в `StackLayout`. Файл XAML, изначально состоит из трех `Button` элементы с их `Command` свойства значения `Binding` и `CommandParameter` свойства, заданные для типов три представления Xamarin.Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

Определяет файл с выделенным кодом и инициализирует `CreateCommand` свойство:

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

Метод, который выполняется при `Button` нажата создает новый экземпляр аргумента, задает его `VerticalOptions` свойство и добавляет ее к `StackLayout`. Три `Button` элементы затем используют страницу с динамически созданным представления:

[![Демонстрация x: Type](consuming-images/typedemo-small.png "Демонстрация x: Type")](consuming-images/typedemo-large.png#lightbox "Демонстрация x: Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>Расширение разметки x:Array

`x:Array` Расширение разметки можно определить массив в разметке. Этот режим поддерживается [ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension) класс, который определяет два свойства:

- `Type` типа `Type`, который указывает тип элементов в массиве.
- `Items` типа `IList`, который представляет коллекцию сами элементы. Это свойство содержимого `ArrayExtension`.

`x:Array` Расширение разметки, сам никогда не отображается в фигурных скобках. Вместо этого `x:Array` открывающий и закрывающий теги в качестве разделителей элементов. Задайте `Type` свойства `x:Type` расширение разметки.

**X: Array Демонстрация** страницы показан способ использования `x:Array` для добавления элементов к `ListView` , задав `ItemsSource` свойство в массив:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

`ViewCell` Создается простой `BoxView` для каждой записи цвет:

[![Демонстрация x: Array](consuming-images/arraydemo-small.png "Демонстрация x: Array")](consuming-images/arraydemo-large.png#lightbox "Демонстрация x: Array")

Существует несколько способов, чтобы указать отдельные `Color` элементов в этом массиве. Можно использовать `x:Static` расширение разметки:

```xaml
<x:Static Member="Color.Blue" />
```

Можно также использовать `StaticResource` для извлечения цвета из словаря ресурсов:

```xaml
<StaticResource Key="myColor" />
```

В конце этой статьи вы увидите пользовательское расширение разметки XAML, которое также создает новое значение цвета:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

При определении массивов распространенных типов, таких как строки или числа, используйте теги, отображаемые в [ **передав аргументы конструктора** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) статьи для разграничения значений.

<a name="null" />

## <a name="xnull-markup-extension"></a>Расширение разметки x:NULL

`x:Null` Расширение разметки поддерживается [ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension) класса. Она не имеет свойств и является просто XAML эквивалент C# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) ключевое слово.

`x:Null` Редко требуются и редко используемые расширение разметки, но если вы найдете потребность в его, вы будете довольны, что, он существует.

**X: Null Демонстрация** страницы показан один сценарий при `x:Null` может быть удобным. Предположим, определены неявным `Style` для `Label` , включающий `Setter` , задает `FontFamily` свойства зависят от платформы имя семейства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

Затем вы обнаружите, что для одного из `Label` элементов, требуется, чтобы все значения свойств в явном `Style` за исключением `FontFamily`, который вы хотите иметь значение по умолчанию. Вы можете определить другой `Style` для этой цели, но более простой подход заключается в задании `FontFamily` свойство конкретного `Label` для `x:Null`, как показано в центре `Label`.

Ниже приведен программу на трех платформ.

[![Демонстрация x: Null](consuming-images/nulldemo-small.png "Демонстрация x: Null")](consuming-images/nulldemo-large.png#lightbox "Демонстрация x: Null")

Обратите внимание, что, четыре из `Label` элементы имеют шрифт serif, но центре `Label` имеет шрифта без засечек по умолчанию.

## <a name="define-your-own-markup-extensions"></a>Определить собственные расширения разметки

Если возникла необходимость расширения разметки XAML, которые не доступны в Xamarin.Forms, вы можете [создать свой собственный](creating.md).


## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Глава расширения разметки XAML из Xamarin.Forms (книга)](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Словари ресурсов](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Динамические стили](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Привязка данных](~/xamarin-forms/app-fundamentals/data-binding/index.md)
