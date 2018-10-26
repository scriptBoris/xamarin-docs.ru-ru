---
title: Привязка из шаблона Xamarin.Forms ControlTemplate
description: Привязки шаблонов позволяют привязать элементы управления в шаблоне элемента управления к данным общим свойствам, включение значения свойств для элементов управления в шаблоне элемента управления, чтобы легко изменить. В этой статье показано использование привязки шаблонов для выполнения привязки данных на основе шаблона элемента управления.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 13730dce5d4698085abe10cb93da5ba50b87ab01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106433"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Привязка из шаблона Xamarin.Forms ControlTemplate

_Привязки шаблонов позволяют привязать элементы управления в шаблоне элемента управления к данным общим свойствам, включение значения свойств для элементов управления в шаблоне элемента управления, чтобы легко изменить. В этой статье показано использование привязки шаблонов для выполнения привязки данных на основе шаблона элемента управления._

Объект [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) используется для привязки свойства элемента управления в шаблоне элемента управления к свойству привязки от родителя *целевой* представление, владеющее шаблон элемента управления. Например, вместо того, чтобы определить текст, отображаемый элементом [ `Label` ](xref:Xamarin.Forms.Label) экземпляров внутри [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate), можно использовать привязку шаблона для привязки [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) привязываемые свойства, которые определяют текста, отображаемого свойства.

Объект [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) похож на существующий [ `Binding` ](xref:Xamarin.Forms.Binding), за исключением того, что *источника* из `TemplateBinding` всегда автоматически присваивается родительским для объекта *целевой* представление, владеющее шаблон элемента управления. Тем не менее, обратите внимание, что использование `TemplateBinding` за пределами [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) не поддерживается.

## <a name="creating-a-templatebinding-in-xaml"></a>Создание TemplateBinding в XAML

В XAML [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) создается с помощью [ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) расширения разметки, как показано в следующем примере кода:

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

Вместо установки [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) свойства в статический текст, свойства можно использовать привязки шаблонов для привязки к привязываемые свойства от родителя *целевой* представление, владеющее [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Тем не менее, обратите внимание, что привязки шаблонов привязан к `Parent.HeaderText` и `Parent.FooterText`, а не `HeaderText` и `FooterText`. Это обусловлено тем, в этом примере привязываемые свойства определяются на прародителя *целевой* просмотреть, а не родительский элемент, как показано в следующем примере кода:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

*Источника* шаблона привязки всегда автоматически присваивается предком *целевой* представление, владеющее шаблон элемента управления, который вот [ `ContentView` ](xref:Xamarin.Forms.ContentView) экземпляр. Привязка использует шаблона [ `Parent` ](xref:Xamarin.Forms.Element.Parent) возвращаемого родительский элемент свойства `ContentView` экземпляра, то есть [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляра. Таким образом, использование [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) в [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) для привязки к `Parent.HeaderText` и `Parent.FooterText` находит привязываемые свойства, которые определены на `ContentPage`, как в следующем примере кода демонстрируются:

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

Это приводит к появлению, показано на следующем снимке экрана:

![](template-binding-images/teal-theme.png "Сине-зеленой шаблон элемента управления, с помощью привязки шаблонов")

## <a name="creating-a-templatebinding-in-c35"></a>Создание TemplateBinding в C&#35;

В C# [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) создается с помощью `TemplateBinding` конструктора, как показано в следующем примере кода:

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

Вместо установки [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) свойства в статический текст, свойства можно использовать привязки шаблонов для привязки к привязываемые свойства от родителя *целевой* представление, владеющее [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Привязка шаблонов создается с помощью [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) метод, указав [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) экземпляр в качестве второго параметра. Обратите внимание, что привязки шаблонов привязки к `Parent.HeaderText` и `Parent.FooterText`, поскольку привязываемых свойства определяются на прародителя *целевой* просмотреть, а не родительский элемент, как показано в следующем примере кода:

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

Привязываемые свойства определяются на `ContentPage`, как описано ранее.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Привязка к свойству модели представления BindableProperty

Как указывалось ранее, [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) привязывает свойство элемента управления в шаблоне элемента управления к свойству привязки от родителя *целевой* представление, владеющее шаблон элемента управления. В свою очередь эти свойства связывания можно привязать к свойствам в ViewModels.

В следующем примере кода определяет два свойства модели представления:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

`HeaderText` И `FooterText` свойства ViewModel могут быть привязаны к, как показано в следующем примере кода XAML:

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

`HeaderText` И `FooterText` привязываемые свойства привязаны к `HomePageViewModel.HeaderText` и `HomePageViewModel.FooterText` свойства, из-за параметра [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) к экземпляру `HomePageViewModel` класса. В целом, это приводит к свойства элемента управления в [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) привязанным к [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляры [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), который, в свою очередь, привязка к Свойства ViewModel.

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

Можно также привязать к свойствам модели представления непосредственно, таким образом, чтобы не нужно объявлять `BindableProperty`s для `HeaderText` и `FooterText` на `ContentPage`, путем привязки шаблона элемента управления к Parent.BindingContext. _PropertyName_ например:

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

Дополнительные сведения о привязке данных к ViewModels см. в разделе [из привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Сводка

В этой статье демонстрируется использование привязки шаблонов для выполнения привязки данных на основе шаблона элемента управления. Привязки шаблонов позволяют привязать элементы управления в шаблоне элемента управления к данным общим свойствам, включение значения свойств для элементов управления в шаблоне элемента управления, чтобы легко изменить.

## <a name="related-links"></a>Связанные ссылки

- [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [От привязки данных до MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Простой темы с привязка шаблонов (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Простой темы с привязка шаблонов и ViewModel (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
