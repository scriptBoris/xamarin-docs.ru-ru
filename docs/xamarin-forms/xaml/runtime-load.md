---
title: Загрузка XAML во время выполнения в Xamarin.Forms
description: XAML можно загружать и проанализировать во время выполнения с помощью методов расширения LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814106"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Загрузка XAML во время выполнения в Xamarin.Forms

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

[ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml) Пространство имен включает в себя два [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) методы расширения, которые можно использовать для загрузки и синтаксического анализа XAML во время выполнения.

## <a name="background"></a>Фон

При создании класса XAML Xamarin.Forms [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) косвенно вызывается метод. Это происходит потому, что файл с выделенным кодом для XAML вызываемый классом `InitializeComponent` метод из своего конструктора:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Когда Visual Studio создает проект, содержащий файл XAML, он проводит синтаксический анализ файла XAML, чтобы создать C# файл кода (например, **MainPage.xaml.g.cs**), содержащий определение `InitializeComponent` метод:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

`InitializeComponent` Вызовы методов [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) метод для извлечения файла XAML (или его скомпилированный двоичный файл) из библиотеки .NET Standard. После извлечения, он инициализирует все объекты, определенные в файле XAML, соединяет их все вместе в родительско дочерних отношений, присоединяет обработчики событий, определенных в коде на события, определенные в файле XAML и задает результирующий дерево объектов в виде содержимого страница.

## <a name="loading-xaml-at-runtime"></a>Загрузка XAML во время выполнения

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) Методы являются `public`, поэтому можно вызвать из приложений Xamarin.Forms для загрузки и синтаксического анализа XAML во время выполнения. Это позволяет сценариев, таких как приложения Загрузка XAML из веб-службы, создание необходимого представления из XAML и его отображения в приложении.

> [!WARNING]
> Загрузка XAML во время выполнения значительно влияет на производительность и обычно следует избегать.

В следующем примере кода показано простое использование:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

В этом примере [ `Button` ](xref:Xamarin.Forms.Button) , создается экземпляр с его [ `Text` ](xref:Xamarin.Forms.Button.Text) определенные значения свойства, задаваемого из XAML в `string`. `Button` Добавляется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) , определенный в XAML для страницы.

> [!NOTE]
> [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) Методы расширения позволяют указывать аргумент универсального типа. Тем не менее, это редко необходимо указать аргумент типа, так как он будет выводится из типа экземпляра, в его работе.

[ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) Метод может использоваться для расширения любого XAML, в следующем примере подобных [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) и перейдя к нему:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Доступ к элементам

Загрузка XAML во время выполнения с [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) метод не выполняет строго типизированный доступ к элементам XAML, которые указаны имена объектов среды выполнения (с помощью `x:Name`). Тем не менее, эти элементы XAML могут быть получены с помощью [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) метод и при необходимости:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

В этом примере XAML для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) увеличивается. Этот XAML включает [ `Label` ](xref:Xamarin.Forms.Label) с именем `monkeyName`, которого извлекается с помощью [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) метод, прежде чем у [ `Text` ](xref:Xamarin.Forms.Label.Text) свойство имеет значение.

## <a name="related-links"></a>Связанные ссылки

- [LoadRuntimeXAML (пример)](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
