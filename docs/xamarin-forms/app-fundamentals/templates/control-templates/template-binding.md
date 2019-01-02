---
title: Привязка на основе ControlTemplate в Xamarin.Forms
description: Привязки шаблонов позволяют элементам управления в шаблоне элемента управления выполнять привязку данных к общим свойствам, предоставляя возможность легко изменять значения свойств в элементах управления в шаблоне элемента управления. В этой статье содержатся сведения об использовании привязок шаблонов для выполнения привязки данных на основе шаблона элемента управления.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 49f66164c707f91f298b2e5cb09b35f1767186cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051585"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Привязка на основе ControlTemplate в Xamarin.Forms

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)

_Привязки шаблонов позволяют элементам управления в шаблоне элемента управления выполнять привязку данных к общим свойствам, предоставляя возможность легко изменять значения свойств в элементах управления в шаблоне элемента управления. В этой статье содержатся сведения об использовании привязок шаблонов для выполнения привязки данных на основе шаблона элемента управления._

Класс [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) служит для привязки свойства элемента управления в шаблоне элемента управления к привязываемому свойству родительского объекта *целевого* представления, являющегося владельцем шаблона элемента управления. Например, вместо того чтобы определять текст, отображаемый экземпляром [`Label`](xref:Xamarin.Forms.Label) в [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), можно использовать привязку шаблона, чтобы привязать свойство [`Label.Text`](xref:Xamarin.Forms.Label.Text) к привязываемым свойствам, которые определяют отображаемый текст.

Класс [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) аналогичен существующему классу [`Binding`](xref:Xamarin.Forms.Binding) за тем исключением, что в качестве *источника* `TemplateBinding` всегда автоматически задается родительский объект *целевого* представления, являющегося владельцем шаблона элемента управления. Обратите внимание, однако, что использование класса `TemplateBinding` за пределами [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) не поддерживается.

## <a name="creating-a-templatebinding-in-xaml"></a>Создание TemplateBinding в XAML

В XAML объект [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) создается с помощью расширения разметки [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension), как показано в следующем примере кода:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Вместо того чтобы присваивать свойствам [`Label.Text`](xref:Xamarin.Forms.Label.Text) статический текст, с помощью привязки к шаблону их можно привязать к привязываемым свойствам родительского объекта *целевого* представления, являющегося владельцем шаблона [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Однако имейте в виду, что с помощью привязок к шаблону выполняется привязка к `Parent.HeaderText` и `Parent.FooterText`, а не к `HeaderText` и `FooterText`. Причина в том, что в этом примере привязываемые свойства определены в прародителе *целевого* представления, а не в его родителе, как показано в следующем фрагменте кода:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

В качестве *источника* привязки к шаблону всегда автоматически задается родительский объект *целевого* представления, являющегося владельцем шаблона элемента управления, которым в этом случае является экземпляр [`ContentView`](xref:Xamarin.Forms.ContentView). Привязка к шаблону использует свойство [`Parent`](xref:Xamarin.Forms.Element.Parent) для возврата родительского элемента экземпляра `ContentView`, которым является экземпляр [`ContentPage`](xref:Xamarin.Forms.ContentPage). Поэтому в результате использования [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) в шаблоне [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) для привязки к `Parent.HeaderText` и `Parent.FooterText` определяются привязываемые свойства, которые определены в `ContentPage`, как показано в следующем примере кода:

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

Результат показан на следующих снимках экрана:

![](template-binding-images/teal-theme.png "Сине-зеленый шаблон элемента управления на основе привязок к шаблону")

## <a name="creating-a-templatebinding-in-c35"></a>Создание TemplateBinding в C&#35;

На C# объект [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) создается с помощью конструктора `TemplateBinding`, как показано в следующем примере кода:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

Вместо того чтобы присваивать свойствам [`Label.Text`](xref:Xamarin.Forms.Label.Text) статический текст, с помощью привязки к шаблону их можно привязать к привязываемым свойствам родительского объекта *целевого* представления, являющегося владельцем шаблона [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Привязка к шаблону создается с помощью метода [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)). Экземпляр [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) указывается в качестве второго параметра. Обратите внимание на то, что привязки к шаблону выполняются к свойствам `Parent.HeaderText` и `Parent.FooterText`, так как привязываемые свойства определены в прародителе *целевого* представления, а не в его родителе, как показано в следующем фрагменте кода:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

Как уже говорилось, привязываемые свойства определены в `ContentPage`.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Привязка BindableProperty к свойству ViewModel

Как говорилось ранее, класс [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) служит для привязки свойства элемента управления в шаблоне элемента управления к привязываемому свойству родительского объекта *целевого* представления, являющегося владельцем шаблона элемента управления. В свою очередь, эти привязываемые свойства можно привязать к свойствам во ViewModels.

В следующем примере кода определяются два свойства во ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

Привязка к свойствам ViewModel `HeaderText` и `FooterText` демонстрируется в следующем примере кода XAML:

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

Привязываемые свойства `HeaderText` и `FooterText` привязываются к свойствам `HomePageViewModel.HeaderText` и `HomePageViewModel.FooterText`, так как [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) присваивается экземпляру класса `HomePageViewModel`. В целом это приводит к привязке свойств элемента управления в [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) к экземплярам [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) в [`ContentPage`](xref:Xamarin.Forms.ContentPage), которые, в свою очередь, привязываются к свойствам ViewModel.

Эквивалентный код на языке C# показан в следующем примере:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

Привязку к свойствам модели представления можно также выполнять напрямую, не объявляя `BindableProperty` для свойств `HeaderText` и `FooterText` в `ContentPage`. Для этого нужно привязать шаблон элемента управления к Parent.BindingContext._ИмяСвойства_, например:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Дополнительные сведения о привязке данных к ViewModel см. в статье [От привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Сводка

В этой статье было продемонстрировано использование привязок к шаблону для выполнения привязки данных на основе шаблона элемента управления. Привязки шаблонов позволяют элементам управления в шаблоне элемента управления выполнять привязку данных к общим свойствам, предоставляя возможность легко изменять значения свойств в элементах управления в шаблоне элемента управления.

## <a name="related-links"></a>Связанные ссылки

- [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [От привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Простая тема с привязкой к шаблону (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Простая тема с привязкой к шаблону и ViewModel (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
