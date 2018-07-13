---
title: Создание Xamarin.Forms DataTemplateSelector
description: В этой статье показано, как создавать и использовать DataTemplateSelector, который может использоваться для выбора шаблона данных DataTemplate во время выполнения на основе значения свойства с привязкой к данным.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: a72777c7e51e96a8e123ecd85ad0aa24fc60fc6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994521"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Создание Xamarin.Forms DataTemplateSelector

_DataTemplateSelector можно использовать для выбора шаблона данных DataTemplate во время выполнения на основе значения свойства с привязкой к данным. Благодаря этому несколько шаблоны DataTemplate, чтобы применить тот же тип объекта, чтобы настроить внешний вид определенных объектов. В этой статье показано, как создавать и использовать DataTemplateSelector._

Селектор шаблона данных позволяет реализовать сценарии, такие как [ `ListView` ](xref:Xamarin.Forms.ListView) привязка к коллекции объектов, где внешний вид каждого объекта в `ListView` можно выбрать во время выполнения, возвращая селектор шаблона данных определенный [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate).

## <a name="creating-a-datatemplateselector"></a>Создание DataTemplateSelector

Селектор шаблона данных реализуется путем создания класса, который наследует от [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). `OnSelectTemplate` Метод переопределяется для возврата конкретного [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), как показано в следующем примере кода:

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

`OnSelectTemplate` Метод возвращает соответствующий шаблон, на основе значения из `DateOfBirth` свойство. Возвращаемый шаблон — это значение `ValidTemplate` свойство или `InvalidTemplate` свойства, которые задаются при использовании `PersonDataTemplateSelector`.

Экземпляр класса селектор шаблона данных можно затем назначить свойства элемента управления Xamarin.Forms например [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1). Список допустимых свойств, см. в разделе [Создание DataTemplate](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Ограничения

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) экземпляры имеют следующие ограничения:

- `DataTemplateSelector` Подкласс должен всегда возвращать один и тот же шаблон для тех же данных, при запросе несколько раз.
- `DataTemplateSelector` Подкласс не должен возвращать другой `DataTemplateSelector` подкласс.
- `DataTemplateSelector` Подкласс не должен возвращать новые экземпляры `DataTemplate` при каждом вызове. Вместо этого должен быть возвращен тот же экземпляр. Невыполнение этого требования создаст утечки памяти и приведет к отключению виртуализации.
- В Android, может быть не более 20 различные шаблоны данных на `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Использование DataTemplateSelector в XAML

В XAML `PersonDataTemplateSelector` могут создаваться, объявив ее в качестве ресурса, как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Этот уровень страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) определяет два [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) экземпляров и `PersonDataTemplateSelector` экземпляра. `PersonDataTemplateSelector` Экземпляра наборы его `ValidTemplate` и `InvalidTemplate` соответствующих свойств `DataTemplate` экземпляров с помощью `StaticResource` расширение разметки. Обратите внимание, что при ресурсы определены на странице [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), они также можно определить на уровне элемента управления или приложения.

`PersonDataTemplateSelector` Экземпляра используется его присвоением [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) свойства, как показано в следующем примере кода:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

Во время выполнения [ `ListView` ](xref:Xamarin.Forms.ListView) вызовы `PersonDataTemplateSelector.OnSelectTemplate` метод для каждого элемента в базовой коллекции, с помощью вызова, передавая объект данных, что `item` параметра. [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) , Возвращаемый метод применяется к этому объекту.

На следующих снимках экрана показано результат [ `ListView` ](xref:Xamarin.Forms.ListView) применение `PersonDataTemplateSelector` для каждого объекта в базовой коллекции:

![](selector-images/data-template-selector.png "ListView с помощью селектора шаблона данных")

Любой `Person` объект, имеющий `DateOfBirth` значение свойства больше или равно 1980 отображается зеленым цветом, с оставшихся объектов, отображаемых красным цветом.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Использование DataTemplateSelector в C&num;

В C# `PersonDataTemplateSelector` создан и назначен [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1) свойства, как показано в следующем примере кода:

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

`PersonDataTemplateSelector` Экземпляра наборы его `ValidTemplate` и `InvalidTemplate` соответствующих свойств [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) экземпляры, созданные `SetupDataTemplates` метод. Во время выполнения [ `ListView` ](xref:Xamarin.Forms.ListView) вызовы `PersonDataTemplateSelector.OnSelectTemplate` метод для каждого элемента в базовой коллекции, с помощью вызова, передавая объект данных, что `item` параметра. `DataTemplate` , Возвращаемый метод применяется к этому объекту.

## <a name="summary"></a>Сводка

В этой статье показано, как создавать и использовать [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector). Объект `DataTemplateSelector` может использоваться для выбора [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) во время выполнения на основе значения свойства с привязкой к данным. Это позволяет нескольким `DataTemplate` экземпляров для применения к тому же типу объектов, чтобы настроить внешний вид определенных объектов.


## <a name="related-links"></a>Связанные ссылки

- [Селектор шаблона данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplateselector/)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)
