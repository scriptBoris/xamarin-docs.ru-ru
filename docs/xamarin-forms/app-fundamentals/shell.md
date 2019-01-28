---
title: Оболочка Xamarin.Forms
description: В этой статье описывается использование оболочки Xamarin.Forms, которая снижает сложность приложений Xamarin.Forms с помощью основных возможностей пользовательского интерфейса, которые требуются для большинства приложений.
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 41530399bfc2210e7c3eda461688c12c6235ef79
ms.sourcegitcommit: 56b2f5cda7c37874618736d6129f19a8976826f0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2019
ms.locfileid: "54418677"
---
# <a name="xamarinforms-shell"></a>Оболочка Xamarin.Forms

![Предварительный просмотр](~/media/shared/preview.png)

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://github.com/Microsoft/TailwindTraders-Mobile)

Оболочка Xamarin.Forms — это контейнер для приложений, который предоставляет фундаментальные компоненты пользовательского интерфейса, необходимые для большинства приложений, позволяя сосредоточиться на базовой рабочей нагрузке приложения. Приложения оболочки получают следующие возможности:

- Одно место для описания визуальной структуры приложения.
- Единый пользовательский интерфейс навигации.
- Служба навигации с созданием глубинных ссылок.
- Обработчик интегрированного поиска.

Эта функциональность снижает сложность приложений, одновременно повышая производительность разработчиков. Кроме того, оболочка написана с учетом потребностей производительности визуализации и потребления памяти.

> [!IMPORTANT]
> Оболочку можно внедрить в уже существующие приложения для iOS и Android, сразу же получив преимущества улучшения навигации, производительности и расширяемости.

Оболочка предоставляет специализированный пользовательский интерфейс навигации на основе всплывающих окон и вкладок. Верхний уровень навигации в приложении оболочки — всплывающее окно:

![Всплывающее окно](shell-images/flyout.png "Всплывающее окно")

На следующем уровне навигации находятся вкладки на нижней панели:

![Вкладки внизу](shell-images/bottom-tabs-full.png "Вкладки внизу")

Если всплывающее окно не открыто, вкладки на нижней панели будут считаться верхним уровнем навигации.

В каждой нижней вкладке следующий уровень навигации — вкладки в верхней панели, где каждая вкладка — `ContentPage`:

![Вкладки вверху](shell-images/top-tabs-full.png "Вкладки вверху")

В каждом `ContentPage` можно добавлять и удалять дополнительные экземпляры `ContentPage` из стека навигации.

## <a name="bootstrapping-a-shell-application"></a>Начальная загрузка приложения оболочки

Приложение оболочки загружается путем присваивания свойству `MainPage` класса `App` экземпляра файла оболочки:

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            MainPage = new TheShell();
        }
    }
}
```

Класс `TheShell` является XAML-файлом, который описывает визуальную структуру вашего приложения.

> [!IMPORTANT]
> Оболочка сейчас является экспериментальной и может использоваться только путем добавления `Forms.SetFlags("Shell_Experimental");` в ваш проект платформы до вызова метода `Forms.Init`.

# <a name="androidtabandroid"></a>[Android](#tab/android)

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());
    }
}
```

# <a name="iostabios"></a>[iOS](#tab/ios)

```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
    }
}
```

----

## <a name="shell-file-hierarchy"></a>Иерархия файлов оболочки

Файл оболочки состоит из трех иерархических элементов:

- `ShellItem`. Один элемент или несколько во всплывающем окне. Каждый `ShellItem` является дочерним элементом `Shell`.
- `ShellSection`. Сгруппированное содержимое, поддерживающее навигацию при помощи нижних вкладок. Каждый `ShellSection` является дочерним элементом `ShellItem`.
- `ShellContent`. Экземпляры `ContentPage` в вашем приложении, которые позволяют перемещаться по верхней вкладке. Каждый `ShellContent` является дочерним элементом `ShellSection`.

Ни один из этих элементов не представляет какую-либо часть пользовательского интерфейса; вместо этого они служат для организации визуальной структуры приложения. Оболочка будет принимать эти элементы и создавать пользовательский интерфейс переходов по содержимому.

Ниже представлен простой XAML-пример файла оболочки:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>
</Shell>
```

> [!NOTE]
> Каждый `ShellItem` может также задавать свойство `FlyoutIcon` как `ImageSource`; оно будет отображаться слева от заголовка.

Этот XAML отображает `HomePage`, так как это первый элемент содержимого, объявленный в файле оболочки:

![Домашняя страница](shell-images/homepage.png "Домашняя страница")

При нажатии кнопки "гамбургер" отображается всплывающее окно:

![Открытое всплывающее окно](shell-images/flyout-initial.png "Открытое всплывающее окно")

Число элементов во всплывающем окне можно увеличить, добавив несколько экземпляров `ShellItem` к файлу оболочки. Тем не менее, обратите внимание, что `HomePage` создается во время запуска приложения; добавление дополнительных экземпляров `ShellItem` при таком подходе приведет к тому, что эти страницы будут также создаваться во время запуска приложения. Этого можно избежать, установив свойство `ShellContent.ContentTemplate` как `DataTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

При таком подходе `ProfilePage` создается только в том случае, когда пользователь переходит к нему, а не при запуске приложения. Кроме того, обратите внимание, что для `ProfilePage`, `ShellItem` и `ShellSection` объекты были пропущены, при этом свойство `Title` определено у экземпляра `ShellContent`. Столь сжатый подход требует меньше разметки; оболочка задает необходимые логические обертки (при этом не добавляя дополнительные представления к визуальному дереву).

Кроме того, внешний вид каждого `ShellItem` может быть настроен путем установки свойства `Shell.ItemTemplate` как `DataTemplate`:

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

Этот код просто изменяет положение текста для каждого `ShellItem` в всплывающем меню. Обратите внимание, что оболочка предоставляет свойства `Title` (и `Icon`) для `BindingContext` из `DataTemplate`.

## <a name="flyout"></a>Flyout

Всплывающее окно — это корневое меню приложения; оно состоит из заголовка всплывающего меню и пунктов меню:

![Всплывающее меню с заметками](shell-images/flyout-annotated.png "Всплывающее меню с заметками")

### <a name="flyout-behavior"></a>Поведение всплывающего меню

Всплывающее окно открывается, если нажать кнопку "гамбургер" или провести пальцем от края экрана. Тем не менее, это поведение можно изменить, задав в свойстве `Shell.FlyoutBehavior` один из членов перечисления `FlyoutBehavior`:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

Установка свойства `FlyoutBehavior` как `Disabled` скрывает всплывающее меню, что полезно при наличии только одного `ShellItem`. Другие допустимые значения `FlyoutBehavior`: `Flyout` (по умолчанию) и `Locked`.

### <a name="flyout-header"></a>Заголовок всплывающего меню

Заголовок всплывающего меню — это содержимое, которое при необходимости отображается в верхней части панели; его внешний вид, определяемый `View`, можно задать с помощью значения свойства `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

Кроме того, вид заголовка всплывающего меню можно определить путем задания свойства `Shell.FlyoutHeaderTemplate` как `DataTemplate`:

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Этот XAML дает следующий заголовок всплывающего меню:

![Заголовок всплывающего меню](shell-images/flyout-header.png "Заголовок всплывающего меню")

По умолчанию заголовок всплывающего меню будет зафиксирован во всплывающем элементе, хотя приведенное ниже его содержимое прокручивается в том случае, если элементов много. Тем не менее, это поведение можно изменить, задав в свойстве `Shell.FlyoutHeaderBehavior` один из членов перечисления `FlyoutHeaderBehavior`:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

Установка `FlyoutHeaderBehavior` для `CollapseOnScroll` приводит к свертыванию всплывающего меню при прокрутке. Другие возможные значения `FlyoutHeaderBehavior`: `Default`, `Fixed` и `Scroll` (прокрутка с пунктами меню).

## <a name="menu-items"></a>Пункты меню

Число элементов во всплывающем окне можно увеличить, добавив несколько новых экземпляров `ShellItem`. Тем не менее, можно также добавить экземпляры `MenuItem` во всплывающее меню. Это позволяет использовать такие сценарии, как перемещение на идентичные страницы с передачей данных через свойство `MenuItem.CommandParameter`.

Экземпляры `MenuItem` следует добавить в коллекцию `Shell.MenuItems`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> Каждый `MenuItem` может также задавать свойство `Icon` как `FileImageSource`; оно будет отображаться слева от текста.

Этот XAML дает следующий вид всплывающего меню:

![Полное всплывающее меню](shell-images/flyout-full.png "Полное всплывающее меню")

Кроме того, внешний вид каждого `MenuItem` можно настроить путем установки свойства `Shell.MenuItemTemplate` как `DataTemplate`:

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

В результате текст экземпляров `MenuItem` отображается полужирным шрифтом:

![Полужирный шрифт пунктов меню](shell-images/menuitems-bold.png "Полужирный шрифт пунктов меню")

## <a name="tabs"></a>Вкладки

Экземпляры `ShellSection` будут отображены в виде нижних вкладок при условии, что есть несколько экземпляров `ShellSection` в одном `ShellItem`:

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

В этом примере экземпляры `ShellSection` будут отображены в виде нижних вкладок:

![Вкладки внизу](shell-images/tabs-bottom.png "Вкладки внизу")

Экземпляры `ShellContent` будут отображены в виде верхних вкладок при условии, что есть несколько экземпляров `ShellContent` в одном `ShellSection`:

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

В этом примере экземпляры `ShellContent` будут отображены в виде верхних вкладок:

![Вкладки вверху](shell-images/tabs-top.png "Вкладки вверху")

Ко вкладкам можно применить различные стили, использовав стили XAML или указав пользовательское средство отрисовки. Так, в следующем примере показан стиль XAML, который задает цвет вкладки:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>Навигация

Оболочка включает навигацию на основе URI. URI предоставляют улучшенные возможности навигации по интерфейсу, позволяя переходить на любую страницу в приложении без необходимости навигации по заданной иерархии. Кроме того, они также дают возможность перехода назад без необходимости прохода всех страниц в стеке навигации.

Навигация на основе URI осуществляется с помощью маршрутов, которые являются сегментами URI, используемыми для перехода в приложении. В файле оболочки необходимо объявить схему маршрута, узел маршрутов и маршрут:

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

Вместе значения свойств `RouteScheme`, `RouteHost` и `Route` формируют корневой URI `app://www.microsoft.com/tailwindtraders`.

Каждый элемент в файле оболочки может также определять свойство route, используемое в программных переходах.

В конструкторе файла оболочки или любой другой части кода, которая выполняется перед вызовом маршрута, можно явно зарегистрировать дополнительные маршруты для всех страниц, которые не представлены с помощью элемента оболочки (таких как экземпляры `MenuItem`):

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>Реализация навигации

Пункты меню предоставляют свойство `Command`, которое может использоваться для реализации навигации:

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app:///tailwindtraders/productcategory?id={typeId}", true);
}
```

Для вызова навигации нужно получить ссылку на `Shell`приложения при помощи свойства `MainPage` класса `Application`. Затем можно вызывать навигацию с помощью вызова `GoToAsync`, передав допустимый URI в качестве аргумента. Метод `GoToAsync` переходит с помощью объекта `ShellNavigationState`, который будет создан из `string` или `Uri`.

### <a name="passing-data"></a>Передача данных

Данные могут передаваться между страницами через параметры строки запроса. Оболочки установит значения строковых параметров запроса в `ContentPage` или ViewModel при добавлении атрибутов свойств запроса к классу:

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

Атрибут `QueryProperty` указывает, что `TypeID` будет получать данные, передаваемые в параметре строки запроса `id` из URI в вызове метода `GoToAsync`.

### <a name="intercepting-navigation"></a>Перехват навигации

Оболочка предоставляет возможность обработки маршрутизации навигации до ее завершения и после ее завершения. Это можно сделать путем регистрации обработчиков событий `Navigating` и `Navigated` соответственно:

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

Класс `ShellNavigatingEventArgs` предоставляет следующие свойства:

| Свойство. | Тип | Описание |
|---|---|---|
| Текущие | `ShellNavigationState` | URI текущей страницы. |
| Исходный код | `ShellNavigationState` | URI, представляющий источник навигации. |
| целевого объекта | `ShellNavigationSource`  | URI, представляющий целевой объект навигации. |
| CanCancel  | `bool` | Значение, указывающее, возможна ли отмена перехода. |
| Отменено  | `bool` | Значение, указывающее, была ли навигация отменена. |

Кроме того, класс `ShellNavigatingEventArgs` предоставляет метод `Cancel`.

Класс `ShellNavigatedEventArgs` предоставляет следующие свойства:

| Свойство. | Тип | Описание |
|---|---|---|
| Текущие | `ShellNavigationState` | URI текущей страницы. |
| Назад| `ShellNavigationState` | URI предыдущей страницы. |
| Исходный код  | `ShellNavigationSource` | URI, представляющий источник навигации. |

Кроме того, оболочка предоставляет переопределяемый метод `OnBackButtonPressed`, который может использоваться для перехвата нажатия кнопки "Назад".

## <a name="related-links"></a>Связанные ссылки

- [Tailwind Traders (пример)](https://github.com/Microsoft/TailwindTraders-Mobile)
