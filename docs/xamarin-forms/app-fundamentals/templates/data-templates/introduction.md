---
title: Знакомство с шаблонами Xamarin.Forms данных
description: Шаблоны Xamarin.Forms данных предоставляют возможность настраивать представление данных в поддерживаемых элементах управления. В этой статье введение в шаблоны данных, анализ, почему они необходимы.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 129ce7a04b93bfb3cb1b9a1639aee61cd56d09d5
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998925"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Знакомство с шаблонами Xamarin.Forms данных

_Шаблоны Xamarin.Forms данных предоставляют возможность настраивать представление данных в поддерживаемых элементах управления. В этой статье введение в шаблоны данных, анализ, почему они необходимы._

Рассмотрите возможность [ `ListView` ](xref:Xamarin.Forms.ListView) , отображающий коллекцию `Person` объектов. В следующем примере кода показано определение `Person` класса:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person` Класс определяет `Name`, `Age`, и `Location` свойства, которые можно задать при `Person` создается объект. [ `ListView` ](xref:Xamarin.Forms.ListView) Используется для отображения коллекция `Person` объектов, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Элементы добавляются [ `ListView` ](xref:Xamarin.Forms.ListView) в XAML путем инициализации [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) свойства из массива `Person` экземпляров.

> [!NOTE]
> Обратите внимание, что `x:Array` элемента требуется `Type` атрибут, указывающий, тип элементов в массиве.

Эквивалентное страницы C# показан в следующем примере кода, который инициализирует [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) свойства `List` из `Person` экземпляров:

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[ `ListView` ](xref:Xamarin.Forms.ListView) Вызовы `ToString` при отображении объектов в коллекции. Так как не `Person.ToString` переопределить, `ToString` возвращает имя типа каждого объекта, как показано на следующем снимке экрана:

![](introduction-images/no-data-template.png "ListView без шаблона данных")

`Person` Объекта можно переопределить `ToString` метод для отображения значимых данных, как показано в следующем примере кода:

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

В результате [ `ListView` ](xref:Xamarin.Forms.ListView) отображение `Person.Name` значение свойства для каждого объекта в коллекции, как показано на следующем снимке экрана:

![](introduction-images/override-tostring.png "ListView с помощью шаблона данных")

`Person.ToString` Переопределения может вернуть отформатированную строку, состоящую из `Name`, `Age`, и `Location` свойства. Тем не менее этот подход обеспечивает отчасти контролировать внешний вид каждого элемента данных. Для большей гибкости [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) могут создаваться, определяющий внешний вид данных.

## <a name="creating-a-datatemplate"></a>Создание шаблона данных DataTemplate

Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) используется для определения внешнего вида данных и обычно использует привязку данных для отображения данных. Его стандартный сценарий использования — при отображении данных из коллекции объектов в [ `ListView` ](xref:Xamarin.Forms.ListView). Например, если `ListView` привязан к коллекции `Person` объектов, `ListView.ItemTemplate` свойству будет присвоено `DataTemplate` , определяющий внешний вид каждого `Person` объекта в `ListView`. `DataTemplate` Будет содержать элементы, которые привязаны к значениям свойств каждого `Person` объекта. Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) может использоваться в качестве значения для следующих свойств:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), который наследуется [ `ListView` ](xref:Xamarin.Forms.ListView).
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), который наследуется [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), и [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage).

> [!NOTE]
> Обратите внимание, что, несмотря на то что [ `TableView` ](xref:Xamarin.Forms.TableView) используются [ `Cell` ](xref:Xamarin.Forms.Cell) объектов, он не использует [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Это обусловлено привязки данных всегда задаются непосредственно в `Cell` объектов.

Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) , помещаются как является прямым потомком свойства, перечисленные выше называется *встроенного шаблона*. Кроме того `DataTemplate` можно определить как ресурс уровня управления, страницы или приложения. Выбор места для определения [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) последствий, где его можно использовать:

- Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) определены в элементе управления уровень может применяться только к элементу управления.
- Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) определены на странице уровень может применяться к несколько допустимых элементов управления на странице.
- Объект [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) определены на уровне приложения может быть применен к допустимым элементов управления в приложении.

Шаблоны данных более низкого уровня в иерархии представлений имеют приоритет над определенных выше вверх, когда они совместно используют `x:Key` атрибуты. Например шаблон приложения уровня данных будут переопределены шаблон уровня страницы данных и шаблон уровня страницы данных будут переопределены шаблон элемента управления на уровне данных, или встроенный шаблон данных.


## <a name="related-links"></a>Связанные ссылки

- [Вид ячейки](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Шаблоны данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
