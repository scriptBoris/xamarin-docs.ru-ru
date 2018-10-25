---
title: Иерархическая навигация
description: В этой статье показано, как использовать класс NavigationPage выполнить переход в стек последним на страницах поступления (LIFO).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: a0a58cf05c97221a73cd0784b7859bb9c84cef86
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "38994680"
---
# <a name="hierarchical-navigation"></a>Иерархическая навигация

_Класс NavigationPage обеспечивает иерархическую навигацию, где пользователь является возможность переходить по страницам вперед и назад по своему усмотрению. Класс реализует навигацию в виде стопки последним поступил — первым обслужен (LIFO) объектов страницы. В этой статье показано, как использовать класс NavigationPage выполнить переход в виде стека страниц._

Чтобы перейти от одной страницы к другому, приложение будет отправлять новую страницу в стек навигации, где она становится активной страницей, как показано на следующей схеме:

![](hierarchical-images/pushing.png "Помещает в стек навигации страницы")

Для возврата к предыдущей странице приложение выбирает текущую страницу из стека навигации, а верхняя страница становится активной страницей, как показано на следующей схеме:

![](hierarchical-images/popping.png "Извлечение страницу из стека навигации")

Представляемые методы навигации [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство для какого-либо [ `Page` ](xref:Xamarin.Forms.Page) производные типы. Эти методы предоставляют возможность для отправки страницы в стек навигации, pop страниц из стека навигации, а также для выполнения со стеком.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Выполнение навигации

При иерархической навигации класс [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) используется для перехода по стеку объектов [`ContentPage`](xref:Xamarin.Forms.ContentPage). На следующих снимках экрана показано основные компоненты `NavigationPage` на каждой платформе:

![](hierarchical-images/navigationpage-components.png "Компоненты NavigationPage")

Макет [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) зависит от платформы:

- В iOS, присутствует панель навигации в верхней части страницы, отображающий заголовок и имеющего *обратно* кнопка, которая возвращает на предыдущую страницу.
- В Android присутствует панель навигации в верхней части страницы, отображающей заголовок, значок, и *обратно* кнопка, которая возвращает на предыдущую страницу. Значок определяется в `[Activity]` атрибут, который оформляет `MainActivity` класс в проекте специфические для платформы Android.
- На универсальной платформе Windows в верхней части страницы, отображающей заголовок присутствует панель навигации.

На всех платформах, значение [ `Page.Title` ](xref:Xamarin.Forms.Page.Title) свойство отображается как заголовок страницы.

> [!NOTE]
> Рекомендуется `NavigationPage` должен быть заполнен `ContentPage` только экземпляр.

### <a name="creating-the-root-page"></a>Создание корневой страницы

Первая страница, добавленная в стек навигации, называется *корневой* страницей приложения, что демонстрируется в следующем примере кода:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

В результате `Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) экземпляр помещается в стек навигации, где он становится активной страницей и на корневую страницу приложения. Это показано на следующем снимке экрана:

![](hierarchical-images/mainpage.png "Корневую страницу из стека навигации")

> [!NOTE]
> [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage) Свойство [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляр предоставляет доступ к первой странице в стеке навигации.

### <a name="pushing-pages-to-the-navigation-stack"></a>Помещает в стек навигации страницы

Чтобы перейти к `Page2Xaml`, необходимо вызвать [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) метод [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство текущей страницы, как показано в следующем примере кода:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

В результате в стек навигации помещается экземпляр `Page2Xaml`, где он становится активной страницей. Это показано на следующем снимке экрана:

![](hierarchical-images/secondpage.png "Стек навигации помещается страницы")

Когда [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) вызывается метод, происходят следующие события:

- Вызов страницы `PushAsync` имеет его [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) вызывается переопределение.
- На странице, на которой осуществляется переход к его [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) вызывается переопределение.
- `PushAsync` Завершения задачи.

Однако точный порядок, в котором происходят эти события не зависит от платформы. Дополнительные сведения см. в разделе [Глава 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) книги Xamarin.Forms (Charles Petzold).

> [!NOTE]
> Вызовы [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) и [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределения не может рассматриваться как гарантированной указывают, навигация по страницам. Например, в iOS `OnDisappearing` переопределение вызывается на активной странице, когда приложение завершает работу.

### <a name="popping-pages-from-the-navigation-stack"></a>Добавление страницы из стека навигации

Активная страница может быть извлечена из стека навигации путем нажатия кнопки *Назад* на устройстве, причем это может быть как физическая кнопка, так и кнопка на экране.

Чтобы вернуться на исходную страницу программным образом, экземпляр `Page2Xaml` должен вызвать метод [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), как показано в следующем примере кода:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

В результате экземпляр `Page2Xaml` удаляется из стека навигации, а активной становится верхняя страница в нем. Когда [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) вызывается метод, происходят следующие события:

- Вызов страницы `PopAsync` имеет его [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) вызывается переопределение.
- На странице, возвращается его [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) вызывается переопределение.
- `PopAsync` Задач возвращает.

Однако точный порядок, в котором происходят эти события не зависит от платформы. Дополнительные сведения см. в разделе [Глава 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) книги Xamarin.Forms (Charles Petzold).

А также [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) и [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) методы, [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство каждой страницы также предоставляет [ `PopToRootAsync` ](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) метод, который показан в следующем примере кода:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Этот метод выводит все, кроме корневого [ `Page` ](xref:Xamarin.Forms.Page) из стека навигации, что на корневую страницу активная страница приложения.

### <a name="animating-page-transitions"></a>Анимация переходов страниц

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) Свойство каждой страницы также предоставляет переопределенный Push-уведомлений и pop методы, которые включают `boolean` параметр, который управляет отображением анимированный страницы во время перехода, как показано в следующем коде Пример:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

Установка `boolean` параметр `false` отключает анимацию перехода страницы, задать для параметра `true` позволяет анимации перехода страницы, при условии, что он поддерживается используемой платформой. Тем не менее Принудительная отправка и извлечение методы, которые не хватает этот параметр по умолчанию включают анимации.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Передача данных при переходе

Иногда это необходимо для страницы для передачи данных на другую страницу во время навигации. Два способа решения этой проблемы происходит обмен данными с помощью конструктора страницы и установив новую страницу [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) к данным. Каждый теперь обсуждаются в свою очередь.

### <a name="passing-data-through-a-page-constructor"></a>Передача данных через конструктор страниц

Самый простой способ передачи данных на другую страницу во время навигации — в качестве параметра конструктора страницы, как показано в следующем примере кода:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Этот код создает `MainPage` экземпляра, передавая текущую дату и время в формате ISO8601, который упаковывается в [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра.

`MainPage` Экземпляр получает данные с помощью параметра конструктора, как показано в следующем примере кода:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Данные отображаются на странице, задав [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) свойства, как показано на следующем снимке экрана:

![](hierarchical-images/passing-data-constructor.png "Данные, передаваемые через конструктор страницы")

### <a name="passing-data-through-a-bindingcontext"></a>Передача данных через объект BindingContext

Альтернативный подход для передачи данных на другую страницу во время навигации —, задав новую страницу [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) к данным, как показано в следующем примере кода:

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Этот код задает [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `SecondPage` экземпляр `Contact` экземпляра, а затем переходит к `SecondPage`.

`SecondPage` Затем использует привязку данных для отображения `Contact` экземпляра данных, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В следующем примере кода показано, как можно выполнить привязку данных в C#:

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

Данные отображаются на странице ряд [ `Label` ](xref:Xamarin.Forms.Label) элементы управления, как показано на следующем снимке экрана:

![](hierarchical-images/passing-data-bindingcontext.png "Данные, передаваемые через объект BindingContext")

Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Управление стек навигации

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) Предоставляет свойство [ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack) свойство, из которого можно получить страницы в стеке навигации. Хотя Xamarin.Forms поддерживает доступ к стек навигации, `Navigation` предоставляет свойство [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) и [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) методы для управления стека путем вставки страницы или их удаления.

[ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) Метод вставляет указанную страницу в стеке навигации перед указанной существующей страницы, как показано на следующей схеме:

![](hierarchical-images/insert-page-before.png "Вставка страницы в стеке навигации")

[ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) Метод удаляет указанную страницу из стека навигации, как показано на следующей схеме:

![](hierarchical-images/remove-page.png "Удаление страницы из стека навигации")

Эти методы позволяют с возможностями пользовательских переходов, например, заменив страницу входа на новую страницу, после успешного входа. В следующем примере кода демонстрируется этот сценарий:

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

При условии, что учетные данные пользователя верны, `MainPage` экземпляр вставляется в стеке навигации перед текущей страницы. [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) Метод затем удаляет текущую страницу из стека навигации, с помощью `MainPage` экземпляра, становится активной страницей.

## <a name="displaying-views-in-the-navigation-bar"></a>Отображение представлений на панели навигации

Любой Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View) могут отображаться в панели переходов [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Это достигается путем установки [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) вложенное свойство, чтобы `View`. Это вложенное свойство может быть задано для любого [ `Page` ](xref:Xamarin.Forms.Page)и когда `Page` помещается `NavigationPage`, `NavigationPage` будет учитывать значение свойства.

В следующем примере, взятом из [Title Просмотр результата](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/), показано, как задать [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) присоединенное свойство из XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

Ниже приведен эквивалент C# кода:

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

В результате [ `Slider` ](xref:Xamarin.Forms.Slider) будут отображаться на панели навигации на [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

[![Ползунок TitleView](hierarchical-images/titleview-small.png "TitleView ползунок")](hierarchical-images/titleview-large.png#lightbox "TitleView \"ползунок\"")

> [!IMPORTANT]
> Несколько представлений не будет отображаться на панели навигации, если не указан размер представления с [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) и [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) свойства. Кроме того, представление может быть заключен в [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) с [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства, задайте соответствующие значения.

Обратите внимание, что, поскольку [ `Layout` ](xref:Xamarin.Forms.Layout) класс является производным от [ `View` ](xref:Xamarin.Forms.View) класс, [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) присоединенного свойства, которые можно задать для отображения макета класс, содержащий несколько представлений. В iOS и универсальной платформы Windows (UWP) нельзя изменить высоту панели навигации и поэтому происходит обрезки, если представление, отображаемое на панели навигации больше, чем размер по умолчанию на панели навигации. Тем не менее, в Android высоту панели навигации можно изменить, задав [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) свойство, используемое для `double` представляющее новую высоту. Дополнительные сведения см. в разделе [значение высоты панели навигации на NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/android.md#navigationpage-barheight).

Кроме того панель с расширенной навигации могут быть предложены, поместить некоторое содержимое на панели навигации, а некоторые — в представление в верхней части страницы содержимого, вы цвет соответствует на панель навигации. Кроме того, в iOS разделительной линии и тени, который находится в нижней части панели навигации можно удалить, установив [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) свойство, используемое для `true`. Дополнительные сведения см. в разделе [скрытие разделителя панели навигации на NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#navigationpage-hideseparatorbar).

> [!NOTE]
> [ `BackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [ `Title` ](xref:Xamarin.Forms.Page.Title), [ `TitleIcon` ](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), И [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) свойства позволяют определять значения, которые занимают место на панели навигации. Хотя размер панели навигации зависит от платформы и размера экрана, установка все эти свойства приведет конфликты из-за ограниченной свободное место. Вместо использования сочетания этих свойств, возможно, лучше достигается макета панели требуемой навигации, только задав `TitleView` свойство.

### <a name="limitations"></a>Ограничения

Существует ряд ограничений, которые следует учитывать при отображении [ `View` ](xref:Xamarin.Forms.View) в строке навигации [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

- В iOS, представления, помещаются в панели переходов `NavigationPage` отображаться по-разному в зависимости от того, доступны ли крупных названий. Дополнительные сведения о включении крупных названий, см. в разделе [отображения крупных названий](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title).
- В Android, поместив представления в панели переходов `NavigationPage` могут выполняться только в приложениях, использующих совместимости приложений.
- Не рекомендуется помещать больших и сложных представлениях, например [ `ListView` ](xref:Xamarin.Forms.ListView) и [ `TableView` ](xref:Xamarin.Forms.TableView), в строке навигации `NavigationPage`.

## <a name="related-links"></a>Связанные ссылки

- [Навигация по страницам](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Иерархическое (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [TitleView (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)
- [Как создать знак в потоке экрана в пример приложения Xamarin.Forms (видео Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Как создать знак в потоке экрана в Xamarin.Forms (видео Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
