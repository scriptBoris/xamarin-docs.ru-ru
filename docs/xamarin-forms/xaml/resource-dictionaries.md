---
title: Словари ресурсов
description: Ресурсы XAML — это объекты, которые совместно и повторно используются в приложении Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c2e6a5624baba251061bcd324fcb849e3d95ebfd
ms.sourcegitcommit: c2d1249cb67b877ee0d9cb8d095ec66fd51d8c31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291039"
---
# <a name="resource-dictionaries"></a>Словари ресурсов

_Ресурсы XAML — определения объектов, которые совместно и повторно используются в приложении Xamarin.Forms._

Эти объекты ресурсов хранятся в словаре ресурсов. Это статье описывается, как создать и использовать словарь ресурсов и объединение словарей ресурсов.

## <a name="overview"></a>Обзор

Объект [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) является репозиторием для ресурсов, используемых приложением Xamarin.Forms. Стандартные ресурсы, которые хранятся в `ResourceDictionary` включают [стили](~/xamarin-forms/user-interface/styles/index.md), [шаблоны элементов управления](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [шаблоны данных](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), цвета и преобразователи типов.

В языке XAML, ресурсов, хранящихся в `ResourceDictionary` можно получить и применить к элементам с помощью `StaticResource` расширения разметки. В C#, ресурсы можно также определять в `ResourceDictionary` получить и применяется к элементам с помощью индексатора, на основе строки. Однако нет большой пользы, с помощью `ResourceDictionary` в C#, как общие объекты можно просто хранятся в виде полей или свойств и использовать непосредственно, без необходимости первого получения их из словаря.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Создание и использование ResourceDictionary

Ресурсы определяются в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) , затем присвойте одно из следующих `Resources` свойства:

- [ `Resources` ](xref:Xamarin.Forms.Application.Resources) Свойство класса, производного от [`Application`](xref:Xamarin.Forms.Application)
- [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) Свойство класса, производного от [`VisualElement`](xref:Xamarin.Forms.Application)

Программа Xamarin.Forms содержит только один класс, производный от `Application` , но часто используется во многих классах, производных от `VisualElement`, в том числе элементов управления, страниц и макетов. Любые из этих объектов могут иметь его `Resources` свойство `ResourceDictionary`. Выбор места для размещения конкретного `ResourceDictionary` влияет на использование ресурсов:

- Ресурсы в `ResourceDictionary` , присоединен к представлению, таких как `Button` или `Label` может применяться только для конкретного объекта, поэтому это не очень удобен.
- Ресурсы в `ResourceDictionary` присоединенного к макету, таких как `StackLayout` или `Grid` может применяться к макет и все дочерние элементы макета.
- Ресурсы в `ResourceDictionary` определены на странице уровня могут применяться для страницы и всех его дочерних узлов.
- Ресурсы в `ResourceDictionary` определенный уровень приложения может применяться в приложении.

Следующий пример XAML ресурсы, определенные в уровне приложения `ResourceDictionary` в **App.xaml** файл, созданный в рамках стандартной программы Xamarin.Forms:

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Это `ResourceDictionary` определяет три [ `Color` ](xref:Xamarin.Forms.Color) ресурсы и [ `Style` ](xref:Xamarin.Forms.Style) ресурсов. Дополнительные сведения о `App` см. в описании [класса приложения](~/xamarin-forms/app-fundamentals/application-class.md).

Начиная с версии 3.0 Xamarin.Forms, явные `ResourceDictionary` теги не являются обязательными. `ResourceDictionary` Автоматически создается объект и ресурсы можно вставить непосредственно между `Resources` теги элементов свойства:

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

Каждый ресурс имеет ключ, который задается с помощью `x:Key` атрибут, который становится его ключ словаря в `ResourceDictionary`. Этот ключ используется для извлечения ресурсов из `ResourceDictionary` по [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) расширения разметки, как показано в следующем примере кода XAML, показывающий дополнительные ресурсы, определенные в `StackLayout`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

Первый [ `Label` ](xref:Xamarin.Forms.Label) экземпляр получает и использует `LabelPageHeadingStyle` ресурсов, которые определены на уровне приложения `ResourceDictionary`, со вторым `Label` экземпляра получение и использование `LabelNormalStyle`ресурсов, которые определены на уровне элемента управления `ResourceDictionary`. Аналогичным образом [ `Button` ](xref:Xamarin.Forms.Button) экземпляр получает и использует `NormalTextColor` ресурсов, которые определены на уровне приложения `ResourceDictionary`и `MediumBoldText` ресурсов, которые определены на уровне элемента управления `ResourceDictionary`. Это приводит к появлению показано на следующем снимке экрана:

[![](resource-dictionaries-images/screenshots-sml.png "Потребление ресурсов ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "потребление ресурсов ResourceDictionary")

> [!NOTE]
> Ресурсы, относящиеся к одной странице не должно быть включено в приложения уровне словаря ресурсов, таким образом ресурсы, затем можно проанализировать при запуске приложения, а не требуется для страницы. Дополнительные сведения см. в разделе [уменьшить размер словаря ресурсов приложения](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Переопределение ресурсы

Когда `ResourceDictionary` ресурсы используют `x:Key` значений атрибутов, ресурсы, определенные ниже в иерархии представления имеют приоритет над указанных выше вверх. Например, установка `PageBackgroundColor` ресурс `Blue` приложения уровень, будут переопределяться уровне страниц `PageBackgroundColor` ресурс `Yellow`. Аналогичным образом, уровень страницы `PageBackgroundColor` ресурса будет переопределено уровень управления `PageBackgroundColor` ресурсов. Этот приоритет демонстрируется в следующем примере кода XAML:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

Исходный `PageBackgroundColor` и `NormalTextColor` экземпляров, определенные на уровне приложения, будут переопределяться `PageBackgroundColor` и `NormalTextColor` экземпляров, определенные на уровне страницы. Таким образом, цвет фона страницы становится синим, а текст на странице yellow, как показано на следующих снимках экрана:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Переопределение ресурсы ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "переопределение ResourceDictionary ресурсы")

Тем не менее, обратите внимание, что в фоновом режиме окна [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) по-прежнему желтый, так как [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) свойству присвоено значение `PageBackgroundColor` ресурсов, определенных в приложении уровень `ResourceDictionary`.

Другим способом, нужно принять во внимание `ResourceDictionary` приоритет: средство синтаксического анализа XAML при обнаружении `StaticResource`, он выполняет поиск совпадающего ключа, передаваемых вверх по дереву visual, с помощью первого совпадения находит. Если поиск заканчивается на странице, ключ еще не удалось найти средство синтаксического анализа XAML выполняет `ResourceDictionary` присоединяется к `App` объекта. Если ключ не найден, возникает исключение.

## <a name="stand-alone-resource-dictionaries"></a>Словари ресурсов автономного

Класс, производный от `ResourceDictionary` могут также находиться в отдельном файле автономного. (Точнее, класс, производный от `ResourceDictionary` обычно требуется _пары_ файлов, поскольку ресурсы определяются в файле XAML, но файл кода с `InitializeComponent` вызов также необходим.) Итоговый файл может быть предоставлена приложений.

Чтобы создать такой файл, добавьте новый **представление содержимого** или **страницы содержимого** в проект (, но не **представление содержимого** или **страницы содержимого** с только файл C#). В XAML-файл и файл C#, измените имя базового класса из `ContentView` или `ContentPage` для `ResourceDictionary`. В XAML-файле имя базового класса является элементом верхнего уровня.

В следующем примере показан XAML [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) с именем `MyResourceDictionary`:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Это `ResourceDictionary` содержит один ресурс, который представляет собой объект типа `DataTemplate`.

Можно создать экземпляр `MyResourceDictionary` , поместив его между парой `Resources` элемент свойства теги, например, в `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Экземпляр `MyResourceDictionary` равно `Resources` свойство `ContentPage` объекта.

Однако такой подход имеет ряд ограничений: `Resources` свойство `ContentPage` ссылается только этого класса, `ResourceDictionary`. В большинстве случаев требуется параметр, в том числе другие `ResourceDictionary` экземпляров и возможно других ресурсов, а также.

Эта задача требует объединенные словари ресурсов.

## <a name="merged-resource-dictionaries"></a>Объединенные словари ресурсов

Объединенные словари ресурсов объединения одного или нескольких `ResourceDictionary` экземпляры в другой `ResourceDictionary`. Можно сделать это в XAML-файле, установив [ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) свойства словари ресурсов, которые будут объединены в приложение, страницы или уровень управления `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` также определяет [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) свойство. Не используйте это свойство; она считается устаревшей начиная с Xamarin.Forms 3.0.

И экземпляр `MyResourceDictionary` могут быть объединены в любое приложение, страницы или уровень управления `ResourceDictionary`. В следующем примере кода XAML отображает их в уровне страниц `ResourceDictionary` с помощью `MergedDictionaries` свойство:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Эта разметка показан только экземпляр `MyResourceDictionary` , добавляемый `ResourceDictionary` , но может также ссылаться другие `ResourceDictionary` экземпляров в `MergedDictionary` теги элементов свойства и другим ресурсам за пределами этих тегов:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Может быть только один `MergedDictionaries` статьи `ResourceDictionary`, но ее можно поместить столько `ResourceDictionary` экземпляров в него необходимо.

При слиянии [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) ресурсы используют одинаковые `x:Key` значений атрибутов, Xamarin.Forms используется следующий приоритет ресурса:

1. Ресурсы локального словарь ресурсов.
1. Ресурсы, содержащиеся в словаре ресурсов, которая была объединена через устаревшие [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) свойство.
1. Ресурсы, содержащиеся в словарях ресурсов, которые были объединены через `MergedDictionaries` коллекции, в обратном порядке, указанном в `MergedDictionaries` свойство.

> [!NOTE]
> Поиск в словарях ресурсов может быть вычислительных задач, если приложение содержит несколько словарей больших ресурсов. Таким образом Чтобы избежать ненужного поиска, необходимо гарантировать, что каждой страницы в приложении только используется словари ресурсов, которые подходят для страницы.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Объединение словарей в Xamarin.Forms 3.0

Начиная с версии 3.0 Xamarin.Forms, процесс слияния [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) становится немного проще и гибче экземпляров. `MergedDictionaries` Теги элементов свойства более не нужны. Вместо этого добавить словарь ресурсов другой `ResourceDictionary` тег с новым [ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source) , имеющим значение имени файла XAML-файла с ресурсами:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Поскольку Xamarin.Forms 3.0 автоматически создает `ResourceDictionary`, этих двух внешнего `ResourceDictionary` теги больше не требуются:

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Add more resources here -->

        <ResourceDictionary Source="MyResourceDictionary.xaml" />

        <!-- Add more resources here -->

    </ContentPage.Resources>
    ...
</ContentPage>
```

Этот новый синтаксис _не_ создать экземпляр `MyResourceDictionary` класса. Вместо этого он ссылается на файл XAML. По этой причине файл кода (**MyResourceDictionary.xaml.cs**) больше не требуется. Можно также удалить `x:Class` атрибут в корневом теге **MyResourceDictionary.xaml** файла.

## <a name="summary"></a>Сводка

В этой статье описано, как создать и использовать [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и способ слияния словари ресурсов. Объект `ResourceDictionary` позволяет определить в одном месте и повторно используются в приложении Xamarin.Forms ресурсы.

## <a name="related-links"></a>Связанные ссылки

- [Словари ресурсов (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
