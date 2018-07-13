---
title: Страницы Xamarin.Forms модальное окно
description: Xamarin.Forms поддерживает модальные страницы. На модальной странице пользователь должен выполнить отдельную задачу, причем он не может уйти с этой страницы, пока задача не будет выполнена или отменена. В этой статье показано, как для перехода по модальным страницам.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 44aee8500c7de2ae56b59049368d6025ec49cc5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994832"
---
# <a name="xamarinforms-modal-pages"></a>Страницы Xamarin.Forms модальное окно

_Xamarin.Forms поддерживает модальные страницы. На модальной странице пользователь должен выполнить отдельную задачу, причем он не может уйти с этой страницы, пока задача не будет выполнена или отменена. В этой статье показано, как для перехода по модальным страницам._

В этой статье рассматриваются следующие темы:

- [Выполнение навигации](#Performing_Navigation) — Отправка страниц в стеке модальное извлечения страниц из модального стека, отключение кнопки «Назад» и анимация переходов страниц.
- [Передача данных при переходе](#Passing_Data_when_Navigating) — передача данных через конструктор страниц и `BindingContext`.

## <a name="overview"></a>Обзор

Модальная страница может быть любой из [страницы](~/xamarin-forms/user-interface/controls/pages.md) типов, поддерживаемых Xamarin.Forms. Для отображения модальной страницы приложение помещает ее в модальное стек, где она становится активной страницей, как показано на следующей схеме:

![](modal-images/pushing.png "Отправка страницы в стеке модального")

Чтобы вернуться к предыдущей странице приложение выбирает текущую страницу из модального стека, а верхняя страница становится активной страницей, как показано на следующей схеме:

![](modal-images/popping.png "Извлечение страницы из модального стека")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Выполнение навигации

Методы модальной навигации предоставляются свойством [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) любых типов, производных от класса [`Page`](xref:Xamarin.Forms.Page). Эти методы предоставляют возможность [push модальные страницы](#Pushing_Pages_to_the_Modal_Stack) в модальное стек, и [pop модальные страницы](#Popping_Pages_from_the_Modal_Stack) из модального стека.

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) Также предоставляет свойство [ `ModalStack` ](xref:Xamarin.Forms.INavigation.ModalStack) свойство, из которого можно получить модальные страницы в стеке модальным. Однако не существует средств для работы с модальным стеком или перехода к корневой странице модальной навигации. Причина в том, что такие операции поддерживаются не всеми базовыми платформами.

> [!NOTE]
> Экземпляр [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) не требуется для навигации по модальным страницам.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Отправка страницы в стеке модального

Чтобы перейти к `ModalPage` необходимо вызвать [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) метод [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство текущей страницы, как показано в следующем примере кода:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

В результате `ModalPage` помещается в стек модальное, где он становится активной страницей, экземпляр указано, что элемент выбран в [ `ListView` ](xref:Xamarin.Forms.ListView) на `MainPage` экземпляра. `ModalPage` Экземпляр показан на снимках экрана ниже:

![](modal-images/modalpage.png "Пример модальные страницы")

Когда [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) вызывается, происходят следующие события:

- Вызов страницы `PushModalAsync` имеет его [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) переопределение вызывается, при условии, что базовая платформа не Android.
- На странице, на которой осуществляется переход к его [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) вызывается переопределение.
- `PushAsync` Завершения задачи.

Тем не менее точный порядок, что эти события происходят — зависит от платформы. Дополнительные сведения см. в разделе [Глава 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) книги Xamarin.Forms (Charles Petzold).

> [!NOTE]
> Вызовы [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) и [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределения не может рассматриваться как гарантированной указывают, навигация по страницам. Например, в iOS `OnDisappearing` переопределение вызывается на активной странице, когда приложение завершает работу.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Добавление страницы из модального стека

Активная страница может извлекается из модального стека, нажав клавишу *обратно* кнопку на устройстве, независимо от того, является ли это физической кнопки на устройстве или на экране кнопку.

Чтобы вернуться на исходную страницу программным образом, экземпляр `ModalPage` должен вызвать метод [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), как показано в следующем примере кода:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

В результате `ModalPage` экземпляр удаляться из модального стека с верхняя страница становится активной страницей. Когда [ `PopModalAsync` ](xref:Xamarin.Forms.INavigation.PopModalAsync) вызывается, происходят следующие события:

- Вызов страницы `PopModalAsync` имеет его [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) вызывается переопределение.
- На странице, возвращается его [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) переопределение вызывается, при условии, что базовая платформа не Android.
- `PopModalAsync` Задач возвращает.

Тем не менее точный порядок, что эти события происходят — зависит от платформы. Дополнительные сведения см. в разделе [Глава 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) книги Xamarin.Forms (Charles Petzold).

### <a name="disabling-the-back-button"></a>Отключение кнопки «Назад»

В Android пользователь всегда можете вернуться на предыдущую страницу, нажав клавишу стандарте *обратно* кнопку на устройстве. Если модальные страницы требуется пользователь должен выполнить отдельную задачу до выхода со страницы, необходимо отключить приложение *обратно* кнопки. Это можно сделать путем переопределения [ `Page.OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) метода модальные страницы. Дополнительные сведения см. в разделе [Глава 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) книги Xamarin.Forms (Charles Petzold).

### <a name="animating-page-transitions"></a>Анимация переходов страниц

[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) Свойство каждой страницы также предоставляет переопределенный Push-уведомлений и pop методы, которые включают `boolean` параметр, который управляет отображением анимированный страницы во время перехода, как показано в следующем коде Пример:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

Установка `boolean` параметр `false` отключает анимацию перехода страницы, задать для параметра `true` позволяет анимации перехода страницы, при условии, что он поддерживается используемой платформой. Тем не менее Принудительная отправка и извлечение методы, которые не хватает этот параметр по умолчанию включают анимации.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Передача данных при переходе

Иногда это необходимо для страницы для передачи данных на другую страницу во время навигации. Два способа решения этой проблемы являются, передачи данных через конструктор страницы и новая страница [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) к данным. Каждый теперь обсуждаются в свою очередь.

### <a name="passing-data-through-a-page-constructor"></a>Передача данных через конструктор страниц

Самый простой способ передачи данных на другую страницу во время навигации — в качестве параметра конструктора страницы, как показано в следующем примере кода:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Этот код создает `MainPage` экземпляр, передав в текущую дату и время в формате ISO8601.

`MainPage` Экземпляр получает данные с помощью параметра конструктора, как показано в следующем примере кода:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Данные отображаются на странице, задав [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) свойство.

### <a name="passing-data-through-a-bindingcontext"></a>Передача данных через объект BindingContext

Альтернативный подход для передачи данных на другую страницу во время навигации —, задав новую страницу [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) к данным, как показано в следующем примере кода:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Этот код задает [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `DetailPage` экземпляр `Contact` экземпляра, а затем переходит к `DetailPage`.

`DetailPage` Затем использует привязку данных для отображения `Contact` экземпляра данных, как показано в следующем примере кода XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

В следующем примере кода показано, как можно выполнить привязку данных в C#:

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
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
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

Данные отображаются на странице ряд [ `Label` ](xref:Xamarin.Forms.Label) элементов управления.

Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Сводка

В этой статье показано, как для перехода по модальным страницам. На модальной странице пользователь должен выполнить отдельную задачу, причем он не может уйти с этой страницы, пока задача не будет выполнена или отменена.


## <a name="related-links"></a>Связанные ссылки

- [Навигация по страницам](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Модальное окно (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (пример)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
