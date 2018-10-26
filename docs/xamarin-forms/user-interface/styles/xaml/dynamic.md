---
title: Динамические стили в Xamarin.Forms
description: В этой статье объясняется, как приложения Xamarin.Forms можно реагировать на изменения стиля динамически во время выполнения с помощью динамических ресурсов.
ms.prod: xamarin
ms.assetid: 13D4FA4B-DF10-42BF-B001-2C49367FC216
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 2f336bff19302228b9c8c3a1aa87930414c44ac2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108409"
---
# <a name="dynamic-styles-in-xamarinforms"></a>Динамические стили в Xamarin.Forms

_Стили не отвечать на изменения свойств и остаются неизменными в течение всего приложения. Например после назначения стиля в визуальный элемент, если один из экземпляров задания изменены, удалены, или добавить новый экземпляр задания, изменения не будут применяться к визуальный элемент. Тем не менее приложения могут реагировать на изменения стиля динамически во время выполнения с помощью динамических ресурсов._

`DynamicResource` Расширение разметки аналогичен `StaticResource` расширение разметки в, использующих ключ словаря для извлечения значения из [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Однако, хотя `StaticResource` выполняет поиск один словарь, `DynamicResource` хранит ссылку на ключ словаря. Таким образом при замене элемента словаря, связанное с ключом, изменение будет применено к визуальный элемент. Благодаря этому среда выполнения стиля вносить изменения в приложении.

В следующем примере кода показано *динамическое* стили на странице XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesPage" Title="Dynamic" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle"
                   TargetType="SearchBar"
                   BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle"
                   TargetType="SearchBar">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Placeholder="These SearchBar controls"
                       Style="{DynamicResource searchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Экземпляров использования `DynamicResource` расширения разметки для ссылки на [ `Style` ](xref:Xamarin.Forms.Style) с именем `searchBarStyle`, который не определен в XAML. Тем не менее так как [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства `SearchBar` экземпляры устанавливаются с использованием `DynamicResource`, отсутствует ключ словаря не приводит к возникновению исключения.

Вместо этого в файл с выделенным кодом, конструктор создает [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) запись с ключом `searchBarStyle`, как показано в следующем примере кода:

```csharp
public partial class DynamicStylesPage : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPage ()
    {
        InitializeComponent ();
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
    }

    void OnButtonClicked (object sender, EventArgs e)
    {
        if (originalStyle) {
            Resources ["searchBarStyle"] = Resources ["greenSearchBarStyle"];
            originalStyle = false;
        } else {
            Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];
            originalStyle = true;
        }
    }
}
```

Когда `OnButtonClicked` выполняется обработчик событий, `searchBarStyle` служит для переключения между `blueSearchBarStyle` и `greenSearchBarStyle`. Это приводит к появлению, показано на следующем снимке экрана:

[![](dynamic-images/dynamic-style-blue.png "Пример динамического стиля цвета на синем")](dynamic-images/dynamic-style-blue-large.png#lightbox "синий пример динамического стиля")
[![](dynamic-images/dynamic-style-green.png "зеленый пример динамического стиля") ] (dynamic-images/dynamic-style-green-large.png#lightbox "Зеленый пример динамического стиля")

В следующем примере кода показаны эквивалентные страницы на языке C#:

```csharp
public class DynamicStylesPageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesPageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        ...
        var searchBar1 = new SearchBar { Placeholder = "These SearchBar controls" };
        searchBar1.SetDynamicResource (VisualElement.StyleProperty, "searchBarStyle");
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = { searchBar1, searchBar2, searchBar3, searchBar4,    button    }
        };
    }
    ...
}
```

В C# [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) экземпляров использования [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) метода для ссылки `searchBarStyle`. `OnButtonClicked` Код обработчика событий идентичен приведенному XAML и при выполнении `searchBarStyle` служит для переключения между `blueSearchBarStyle` и `greenSearchBarStyle`.

## <a name="dynamic-style-inheritance"></a>Наследование динамического стиля

Наследование от динамического стиля стиль нельзя сделать с помощью [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) свойство. Вместо этого [ `Style` ](xref:Xamarin.Forms.Style) класс включает [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) свойство, которое может быть присвоено словарь ключ, значение которого может динамически изменять.

В следующем примере кода показано *динамическое* стиля наследования в страницу XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DynamicStylesInheritancePage" Title="Dynamic Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
            <Style x:Key="blueSearchBarStyle" TargetType="SearchBar" BasedOn="{StaticResource baseStyle}">
              ...
            </Style>
            <Style x:Key="greenSearchBarStyle" TargetType="SearchBar">
              ...
            </Style>
            <Style x:Key="tealSearchBarStyle" TargetType="SearchBar" BaseResourceKey="searchBarStyle">
              ...
            </Style>
            ...
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <SearchBar Text="These SearchBar controls" Style="{StaticResource tealSearchBarStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `SearchBar` ](xref:Xamarin.Forms.SearchBar) Экземпляров использования `StaticResource` расширения разметки для ссылки на [ `Style` ](xref:Xamarin.Forms.Style) с именем `tealSearchBarStyle`. Это `Style` задает некоторые дополнительные свойства и использует [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) свойство для ссылки `searchBarStyle`. `DynamicResource` Расширения разметки не является обязательным, так как `tealSearchBarStyle` не изменится, за исключением `Style` он является производным от. Таким образом `tealSearchBarStyle` сохраняет ссылку `searchBarStyle` и изменяется при изменении базового стиля.

В файле кода, конструктор создает [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) запись с ключом `searchBarStyle`, как в предыдущем примере, демонстрируются динамические стили. Когда `OnButtonClicked` выполняется обработчик событий, `searchBarStyle` служит для переключения между `blueSearchBarStyle` и `greenSearchBarStyle`. Это приводит к появлению, показано на следующем снимке экрана:

[![](dynamic-images/dynamic-style-inheritance-blue.png "Синий пример наследования стиля динамического")](dynamic-images/dynamic-style-inheritance-blue-large.png#lightbox "синий пример наследования стиля динамического")
[![](dynamic-images/dynamic-style-inheritance-green.png "зеленый динамического стиля Пример наследования")](dynamic-images/dynamic-style-inheritance-green-large.png#lightbox "зеленый пример наследования стиля динамические")

В следующем примере кода показаны эквивалентные страницы на языке C#:

```csharp
public class DynamicStylesInheritancePageCS : ContentPage
{
    bool originalStyle = true;

    public DynamicStylesInheritancePageCS ()
    {
        ...
        var baseStyle = new Style (typeof(View)) {
            ...
        };
        var blueSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var greenSearchBarStyle = new Style (typeof(SearchBar)) {
            ...
        };
        var tealSearchBarStyle = new Style (typeof(SearchBar)) {
            BaseResourceKey = "searchBarStyle",
            ...
        };
        ...
        Resources = new ResourceDictionary ();
        Resources.Add ("blueSearchBarStyle", blueSearchBarStyle);
        Resources.Add ("greenSearchBarStyle", greenSearchBarStyle);
        Resources ["searchBarStyle"] = Resources ["blueSearchBarStyle"];

        Content = new StackLayout {
            Children = {
                new SearchBar { Text = "These SearchBar controls", Style = tealSearchBarStyle },
                ...
            }
        };
    }
    ...
}
```

`tealSearchBarStyle` Назначается непосредственно к [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойство [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) экземпляров. Это `Style` задает некоторые дополнительные свойства и использует [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) свойство для ссылки `searchBarStyle`. [ `SetDynamicResource` ](xref:Xamarin.Forms.Element.SetDynamicResource*) Метод не требуется здесь поскольку `tealSearchBarStyle` не изменится, за исключением `Style` он является производным от. Таким образом `tealSearchBarStyle` сохраняет ссылку `searchBarStyle` и изменяется при изменении базового стиля.

## <a name="summary"></a>Сводка

Стили не отвечать на изменения свойств и остаются неизменными в течение всего приложения. Тем не менее приложения могут реагировать на изменения стиля динамически во время выполнения с помощью динамических ресурсов. Кроме того *динамическое* стили могут быть производными от с [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) свойство.



## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Динамические стили (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Работа с использованием стилей (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Стиль](xref:Xamarin.Forms.Style)
- [Метод задания](xref:Xamarin.Forms.Setter)
