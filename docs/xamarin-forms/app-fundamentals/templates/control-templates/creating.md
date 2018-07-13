---
title: Создание шаблона ControlTemplate
description: Шаблоны элементов управления можно определять на уровне приложения или страницы. В этой статье показано, как создавать и использовать шаблоны элементов управления.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998194"
---
# <a name="creating-a-controltemplate"></a>Создание шаблона ControlTemplate

_Шаблоны элементов управления можно определять на уровне приложения или страницы. В этой статье показано, как создавать и использовать шаблоны элементов управления._

## <a name="creating-a-controltemplate-in-xaml"></a>Создания объекта ControlTemplate в XAML

Для определения [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) на уровне приложения, [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) должны добавляться `App` класса. По умолчанию используют все приложения Xamarin.Forms, созданные на основе шаблона **приложения** класс для реализации [ `Application` ](xref:Xamarin.Forms.Application) подкласс. Для объявления `ControlTemplate` на уровне приложения, в приложении `ResourceDictionary` с помощью XAML, значение по умолчанию **приложения** класса должны быть заменены XAML **приложения** класс и связанные с выделенным кодом, как в следующем примере кода показан:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Каждый [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) в виде многократного использования объекта создается экземпляр [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary).  Это достигается путем предоставления каждое объявление уникальный `x:Key` атрибут, который предоставляет ему описательное ключа в `ResourceDictionary`.

В следующем примере кода показано связанного `App` кода:

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

А также параметр [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойства кода необходимо также вызвать `InitializeComponent` метод, чтобы загрузить и проанализировать связанные XAML.

В следующем коде показано в примере [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) применение `TealTemplate` для [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

`TealTemplate` Назначается [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) свойства с помощью `StaticResource` расширение разметки. [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) Свойству [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) , определяющий содержимое, отображаемое на [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Это содержимое будет отображаться по [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) содержащихся в `TealTemplate`. Это приводит к появлению, показано на следующем снимке экрана:

![](creating-images/teal-theme.png "Сине-зеленый шаблона элемента управления")

### <a name="re-theming-an-application-at-runtime"></a>RE темы приложения во время выполнения

Щелкнув **изменить тему** нажатию кнопки выполняется `OnButtonClicked` метод, который показан в следующем примере кода:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Этот метод заменяет активного [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) экземпляр с альтернативой `ControlTemplate` экземпляр, полученный на следующем снимке экрана:

![](creating-images/aqua-theme.png "Шаблон элемента управления зеленовато-голубой")

> [!NOTE]
> На `ContentPage`, `Content` свойство может быть присвоено и `ControlTemplate` также можно задать свойство. Когда это происходит, если `ControlTemplate` содержит `ContentPresenter` экземпляр содержимого, назначенного `Content` будет должно быть представлено свойство `ContentPresenter` в `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Установка шаблона элемента управления, используя стиль

Объект [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) также могут устанавливаться через [ `Style` ](xref:Xamarin.Forms.Style) Дополнительно расширить возможности темы. Это можно сделать, создав *неявное* или *явные* стиль для представление целевого объекта в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и параметр `ControlTemplate` свойства целевого объекта Просмотр в [ `Style` ](xref:Xamarin.Forms.Style) экземпляра. В следующем коде показано в примере *неявное* стиль, который добавляется к уровню приложения [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Так как [ `Style` ](xref:Xamarin.Forms.Style) экземпляр *неявное*, он применяется ко всем `ContentView` экземпляров в приложении. Таким образом, он больше не необходимо задать [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) свойства, как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Дополнительные сведения о стилях см. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Создания объекта ControlTemplate на уровне страниц

В дополнение к созданию [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) экземпляров на уровне приложения, они также могут создаваться на уровне страницы, как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

При добавлении [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) на уровне страницы, [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) добавляется [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), а затем `ControlTemplate` экземпляры включены в `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Создания объекта ControlTemplate в C&#35;

Для определения [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) на уровне приложения, `class` должны создаваться, представляющий `ControlTemplate`. Класс должен наследоваться от [макета](~/xamarin-forms/user-interface/layouts/index.md) используется шаблон, как показано в следующем примере кода:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

`AquaTemplate` Класс идентичен `TealTemplate` класса, за исключением того, что используются разные цвета для [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) и [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) свойства.

В следующем коде показано в примере [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) применение `TealTemplate` для [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) Экземпляры создаются путем указания типа классов, которые определяют шаблоны элементов управления, в `ControlTemplate` конструктор.

[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) Свойству [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) , определяющий содержимое, отображаемое на [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Это содержимое будет отображаться по [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) содержащихся в `TealTemplate`. Тот же механизм описанные ранее используется для изменения темы во время выполнения для `AquaTheme`.

## <a name="summary"></a>Сводка

В этой статье было показано, как создавать и использовать шаблоны элементов управления. Шаблоны элементов управления можно определять на уровне приложения или страницы.


## <a name="related-links"></a>Связанные ссылки

- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [Тема «простой» (образец)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
