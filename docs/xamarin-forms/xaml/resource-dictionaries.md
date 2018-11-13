---
title: Словари ресурсов
description: Ресурсы XAML — это объекты, которые можно совместно использовать и повторно использоваться на протяжении всего приложения Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 30eb8522f39438ca4006379b3b9e0d3a613ba112
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563034"
---
# <a name="resource-dictionaries"></a>Словари ресурсов

_Ресурсы XAML, определения объектов, которые можно совместно использовать и повторно использоваться на протяжении всего приложения Xamarin.Forms._

Эти объекты ресурсов хранятся в словаре ресурсов. В этой статье описывается, как создавать и использовать словарь ресурсов и как объединение словарей ресурсов.

## <a name="overview"></a>Обзор

Объект [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) — это репозиторий для ресурсов, используемых в приложении Xamarin.Forms. Стандартные ресурсы, которые хранятся в `ResourceDictionary` включают [стили](~/xamarin-forms/user-interface/styles/index.md), [шаблоны элементов управления](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [шаблоны данных](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), цвета и преобразователями.

В XAML, ресурсы, которые хранятся в `ResourceDictionary` затем извлекаются и применяется к элементам с помощью `StaticResource` расширение разметки. В C#, ресурсы также могут быть определены в `ResourceDictionary` и затем извлекается и применяется к элементам с помощью индексатора строковых данных. Однако нет большой пользы с помощью `ResourceDictionary` в C#, как общие объекты можно просто хранятся в виде полей или свойств и использовать напрямую без необходимости первый извлекать их из словаря.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Создание и использование ResourceDictionary

Ресурсы определяются в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) то есть установите одно из следующих `Resources` свойства:

- [ `Resources` ](xref:Xamarin.Forms.Application.Resources) Свойства любого класса, производного от [`Application`](xref:Xamarin.Forms.Application)
- [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) Свойства любого класса, производного от [`VisualElement`](xref:Xamarin.Forms.Application)

Программа Xamarin.Forms содержит только один класс, производный от `Application` , но часто используются различные классы, которые являются производными от `VisualElement`, в том числе элементы управления, страниц и макетов. Эти объекты могут иметь его `Resources` свойство значение `ResourceDictionary`. Выбор места для размещения определенного `ResourceDictionary` последствий, где могут использоваться ресурсы:

- Ресурсы в `ResourceDictionary` , присоединяется к представлению, такие как `Button` или `Label` может применяться только для этого конкретного объекта, так что это не очень полезно.
- Ресурсы в `ResourceDictionary` подключен к макета, такие как `StackLayout` или `Grid` могут применяться к макет и все дочерние элементы этого макета.
- Ресурсы в `ResourceDictionary` определены на странице уровень может применяться к странице и всем его дочерним элементам.
- Ресурсы в `ResourceDictionary` определенный уровень приложения может применяться в приложении.

Следующий XAML показаны ресурсы, определенные на уровне приложения `ResourceDictionary` в **App.xaml** файл, созданный как часть Стандартная программа Xamarin.Forms:

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

Это `ResourceDictionary` определяет три [ `Color` ](xref:Xamarin.Forms.Color) ресурсы и [ `Style` ](xref:Xamarin.Forms.Style) ресурсов. Дополнительные сведения о `App` , представлена в разделе [класс App](~/xamarin-forms/app-fundamentals/application-class.md).

Начиная с версии 3.0 Xamarin.Forms, явный `ResourceDictionary` теги не являются обязательными. `ResourceDictionary` Объект создается автоматически, и ресурсы можно вставить непосредственно между `Resources` теги элемента свойства:

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

Каждый ресурс имеет ключ, который задается с помощью `x:Key` атрибут, который становится его ключ словаря в `ResourceDictionary`. Ключ используется для извлечения ресурса из `ResourceDictionary` по [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) расширения разметки, как показано в следующем примере кода XAML, показывающий дополнительные ресурсы, определенные в `StackLayout`:

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

Первый [ `Label` ](xref:Xamarin.Forms.Label) экземпляр извлекает и использует `LabelPageHeadingStyle` ресурс, определенный на уровне приложения `ResourceDictionary`, со вторым `Label` экземпляра получение и использование `LabelNormalStyle`ресурс, определенный в уровне управления `ResourceDictionary`. Аналогичным образом [ `Button` ](xref:Xamarin.Forms.Button) экземпляр извлекает и использует `NormalTextColor` ресурс, определенный на уровне приложения `ResourceDictionary`и `MediumBoldText` ресурс, определенный в уровне управления `ResourceDictionary`. Это приводит к появлению, показано на следующем снимке экрана:

[![](resource-dictionaries-images/screenshots-sml.png "Использование ResourceDictionary ресурсов")](resource-dictionaries-images/screenshots.png#lightbox "потребления ресурсов ResourceDictionary")

> [!NOTE]
> Ресурсы, относящиеся к одной странице не должно быть включено в приложение ресурсов на уровне словарю, таким образом ресурсы будут анализироваться при запуске приложения, а не, затем при необходимости в страницу. Дополнительные сведения см. в разделе [уменьшение размера словаря ресурсов приложения](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Переопределения ресурсов

Когда `ResourceDictionary` совместно используют ресурсы `x:Key` значений атрибутов, ресурсы, определенные ниже в иерархии представлений будет иметь приоритет над определенных выше вверх. Например, установка `PageBackgroundColor` ресурса `Blue` приложения будут переопределены уровень уровня страницы `PageBackgroundColor` набор ресурсов, `Yellow`. Аналогичным образом, уровень страницы `PageBackgroundColor` ресурса будет переопределено уровень управления `PageBackgroundColor` ресурсов. Этот приоритет демонстрируется в следующем примере кода XAML:

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

Исходный `PageBackgroundColor` и `NormalTextColor` экземпляров, определенные на уровне приложения, переопределяются `PageBackgroundColor` и `NormalTextColor` экземпляров, определенные на уровне страниц. Таким образом, цвет фона страницы становится синий, а текст на странице желтый, как показано на следующем снимке экрана:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Переопределения ресурсов ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "ResourceDictionary ресурсов переопределения")

Тем не менее, обратите внимание, что на фон панели [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) по-прежнему желтый, так как [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) свойству присваивается значение `PageBackgroundColor` ресурс, определенный в приложении уровень `ResourceDictionary`.

Вот еще один способ подумать о `ResourceDictionary` приоритет: синтаксического анализа XAML, когда обнаруживает `StaticResource`, он выполняет поиск совпадающего ключа, проходя вверх по визуальному дереву, используется первый из них найдет. Если этот поиск завершается на странице и ключ по-прежнему не был найден, средство синтаксического анализа XAML ищет `ResourceDictionary` подключен к `App` объекта. Если ключ не найден, возникает исключение.

## <a name="stand-alone-resource-dictionaries"></a>Словари ресурсов автономного

Класс, производный от `ResourceDictionary` также может находиться в отдельном файле автономного. (Точнее, класс, производный от `ResourceDictionary` обычно требуется _пары_ файлов, так как эти ресурсы определяются в файле XAML, но файл с выделенным кодом `InitializeComponent` вызов также необходим.) Итоговый файл может быть предоставлена приложений.

Чтобы создать такой файл, добавьте новый **представление содержимого** или **страницы содержимого** в проект элемент (, но не **представление содержимого** или **страницы содержимого** с только файл C#). В XAML-файл и файл C#, измените имя базового класса из `ContentView` или `ContentPage` для `ResourceDictionary`. В файле XAML имя базового класса является элементом верхнего уровня.

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

Это `ResourceDictionary` содержит один ресурс, который является объектом типа `DataTemplate`.

Можно создать экземпляр `MyResourceDictionary` , поместив его между парой `Resources` элемента свойства тегов, например, в `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Экземпляр `MyResourceDictionary` присваивается `Resources` свойство `ContentPage` объекта.

Однако такой подход имеет некоторые ограничения: `Resources` свойство `ContentPage` ссылается только вот `ResourceDictionary`. В большинстве случаев требуется, в том числе другие `ResourceDictionary` экземпляров и возможно других ресурсов, а также.

Эта задача требует объединенные словари ресурсов.

## <a name="merged-resource-dictionaries"></a>Объединенные словари ресурсов

Объединенные словари ресурсов различные сочетания `ResourceDictionary` экземпляров в другой `ResourceDictionary`. Установив это можно сделать в файле XAML [ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) свойства один или несколько словарей ресурсов, которые будут объединены в приложения, страницы или уровень управления `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` также определяет [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) свойство. Не используйте это свойство; он является устаревшим начиная с Xamarin.Forms 3.0.

Экземпляр `MyResourceDictionary` можно объединить в любое приложение, страницы или уровень управления `ResourceDictionary`. В следующем примере кода XAML отображает их в уровне страниц `ResourceDictionary` с помощью `MergedDictionaries` свойство:

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

Разметка отображает только экземпляр `MyResourceDictionary` , добавляемый `ResourceDictionary` , но можно также обращаться другие `ResourceDictionary` экземпляров в `MergedDictionary` теги элемента свойства и другие ресурсы за пределами этих тегов:

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

Может существовать только один `MergedDictionaries` статьи `ResourceDictionary`, но вы можете поместить столько `ResourceDictionary` экземпляров здесь, как требуется.

При слиянии [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) ресурсы используют одинаковые `x:Key` значений атрибутов, Xamarin.Forms используется следующий приоритет ресурсов:

1. Ресурсы локального в словарь ресурсов.
1. Ресурсы, содержащиеся в словаре ресурсов, которая была объединена с помощью устаревших [ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith) свойство.
1. Ресурсы, содержащиеся в словарях ресурсов, которые были объединены с помощью `MergedDictionaries` коллекции, в обратном порядке, они перечислены в `MergedDictionaries` свойство.

> [!NOTE]
> Поиск словари ресурсов может оказаться задачей с большим объемом вычислений, если приложение содержит несколько словарей больших ресурсов. Таким образом Чтобы избежать ненужного поиска, должны убедитесь, что каждая страница в приложение только использует словари ресурсов, которые подходят для страницы.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Объединение словарей в Xamarin.Forms 3.0

Начиная с версии 3.0 Xamarin.Forms, процесс слияния [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) экземпляров становится немного намного проще и гибче. `MergedDictionaries` Теги элементов свойства, больше не нужно. Вместо этого можно добавить в словарь ресурсов `ResourceDictionary` тега с новым [ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source) свойство присвоено имя файла XAML с ресурсами:

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

Поскольку Xamarin.Forms 3.0 автоматически создает экземпляр `ResourceDictionary`, эти два внешнего `ResourceDictionary` теги больше не требуются:

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

Этот новый синтаксис _не_ создать экземпляр `MyResourceDictionary` класса. Вместо этого он ссылается на файл XAML. По этой причине файл с выделенным кодом (**MyResourceDictionary.xaml.cs**) больше не требуется. Вы также можете удалить `x:Class` атрибут из корневым тегом **MyResourceDictionary.xaml** файла.

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как создавать и использовать [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и как объединение словарей ресурсов. Объект `ResourceDictionary` позволяет ресурсы, определенные в одном месте, и повторно использоваться на протяжении всего приложения Xamarin.Forms.

## <a name="related-links"></a>Связанные ссылки

- [Словари ресурсов (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
