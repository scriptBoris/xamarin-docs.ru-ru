---
title: Создание шаблона ControlTemplate
description: Шаблоны элементов управления можно определять на уровне приложения или на уровне страницы. В этой статье содержатся сведения о создании и использовании шаблонов элементов управления.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998194"
---
# <a name="creating-a-controltemplate"></a>Создание шаблона ControlTemplate

_Шаблоны элементов управления можно определять на уровне приложения или на уровне страницы. В этой статье содержатся сведения о создании и использовании шаблонов элементов управления._

## <a name="creating-a-controltemplate-in-xaml"></a>Создание ControlTemplate в XAML

Чтобы определить шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) на уровне приложения, необходимо добавить [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) в класс `App`. По умолчанию все приложения Xamarin.Forms, создаваемые на основе шаблона, используют класс **App** для реализации подкласса [`Application`](xref:Xamarin.Forms.Application). Чтобы объявить шаблон `ControlTemplate` на уровне приложения в словаре `ResourceDictionary` приложения на основе XAML, необходимо заменить класс **App** по умолчанию классом **App** XAML и соответствующим кодом программной части, как показано в следующем примере кода.

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

Каждый экземпляр [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) создается как повторно используемый объект в [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).  Для этого каждому объявлению присваивается уникальный атрибут `x:Key`, который предоставляет описательный ключ в `ResourceDictionary`.

В следующем примере показан соответствующий код программной части класса `App`.

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

Помимо задания свойства [`MainPage`](xref:Xamarin.Forms.Application.MainPage), в коде программной части должен вызываться метод `InitializeComponent` для загрузки и анализа соответствующего кода XAML.

В следующем примере кода показан объект [`ContentPage`](xref:Xamarin.Forms.ContentPage), который применяет `TealTemplate` к [`ContentView`](xref:Xamarin.Forms.ContentView).

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

Шаблон `TealTemplate` присваивается свойству [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) с помощью расширения разметки `StaticResource`. Свойству [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) присваивается экземпляр [`StackLayout`](xref:Xamarin.Forms.StackLayout), который определяет содержимое, отображаемое на странице [`ContentPage`](xref:Xamarin.Forms.ContentPage). Оно будет отображаться объектом [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter), содержащимся в шаблоне `TealTemplate`. Результат показан на следующих снимках экрана.

![](creating-images/teal-theme.png "Сине-зеленый шаблон элемента управления")

### <a name="re-theming-an-application-at-runtime"></a>Изменение темы приложения во время выполнения

При нажатии кнопки **Change Theme** (Сменить тему) выполняется метод `OnButtonClicked`, который показан в следующем примере кода.

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Этот метод заменяет активный экземпляр [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) на альтернативный экземпляр `ControlTemplate`. Результат показан на следующем снимке экрана.

![](creating-images/aqua-theme.png "Темно-бирюзовый шаблон элемента управления")

> [!NOTE]
> На странице `ContentPage` могут быть одновременно заданы свойства `Content` и `ControlTemplate`. В этом случае, если шаблон `ControlTemplate` содержит экземпляр `ContentPresenter`, содержимое, присвоенное свойству `Content`, будет отображаться объектом `ContentPresenter` в `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Задание ControlTemplate с помощью стиля

Шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) можно также применять с помощью стиля [`Style`](xref:Xamarin.Forms.Style) для расширения возможностей настройки тем. Для этого можно создать *неявный* или *явный* стиль для целевого представления в [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) и задать свойство `ControlTemplate` целевого представления в экземпляре [`Style`](xref:Xamarin.Forms.Style). В следующем примере кода показан *неявный* стиль, добавленный в [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) на уровне приложения.

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Так как экземпляр [`Style`](xref:Xamarin.Forms.Style) *неявный*, он применяется ко всем экземплярам `ContentView` в приложении. Поэтому задавать свойство [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) не нужно, как показано в следующем примере кода.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Дополнительные сведения о стилях см. в статье [Стили](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Создание ControlTemplate на уровне страницы

Экземпляры [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) можно создавать не только на уровне приложения, но и на уровне страницы, как показано в следующем примере кода.

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

Когда шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) добавляется на уровне страницы, объект [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) добавляется в [`ContentPage`](xref:Xamarin.Forms.ContentPage), после чего экземпляры `ControlTemplate` включаются в `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Создание ControlTemplate на C&#35;

Чтобы определить шаблон [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) на уровне приложения, необходимо создать `class`, представляющий `ControlTemplate`. Этот класс должен быть производным от [макета](~/xamarin-forms/user-interface/layouts/index.md), используемого для шаблона, как показано в следующем примере кода.

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

Класс `AquaTemplate` идентичен классу `TealTemplate` за тем исключением, что для свойств [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) и [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) используются другие цвета.

В следующем примере кода показан объект [`ContentPage`](xref:Xamarin.Forms.ContentPage), который применяет `TealTemplate` к [`ContentView`](xref:Xamarin.Forms.ContentView).

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

Экземпляры [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) создаются путем указания типа классов, которые определяют шаблоны элементов управления, в конструкторе `ControlTemplate`.

Свойству [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) присваивается экземпляр [`StackLayout`](xref:Xamarin.Forms.StackLayout), который определяет содержимое, отображаемое на странице [`ContentPage`](xref:Xamarin.Forms.ContentPage). Оно будет отображаться объектом [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter), содержащимся в шаблоне `TealTemplate`. Для изменения темы на `AquaTheme` во время выполнения используется описанный выше механизм.

## <a name="summary"></a>Сводка

В этой статье было показано, как создавать и использовать шаблоны элементов управления. Шаблоны элементов управления можно определять на уровне приложения или на уровне страницы.


## <a name="related-links"></a>Связанные ссылки

- [Стили](~/xamarin-forms/user-interface/styles/index.md)
- [Простая тема (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
