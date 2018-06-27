---
title: Подробные сведения о Xamarin.Forms (несколько экранов)
description: Эта статья знакомит читателя с такими понятиями, как навигация по страницам и привязка данных в приложении Xamarin.Forms, а также демонстрирует их использование в кроссплатформенном приложении с несколькими экранами.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: e4faa36c-6600-48c0-94c4-b4431103a4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 1c7edff3c71b9d7530b2acf21acaa06149156d43
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242239"
---
# <a name="xamarinforms-multiscreen-deep-dive"></a>Подробные сведения о Xamarin.Forms (несколько экранов)

В [кратком руководстве по Xamarin.Forms (несколько экранов)](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/quickstart.md) приложение Phoneword было расширено для включения второго экрана, который отслеживает журнал вызовов для приложения. В этой статье выполняется проверка созданных компонентов, позволяющая получить представление о навигации по страницам и привязке данных в приложении Xamarin.Forms.

## <a name="navigation"></a>Навигация

Xamarin.Forms предоставляет встроенную модель навигации, которая управляет навигацией по нескольким страницам и работой с ними. Эта модель реализует стек объектов `Page`, поддерживающий метод LIFO (последним поступил — первым обслужен). Для перехода c одной страницы на другую приложение будет отправлять новую страницу в этот стек. Для возврата на предыдущую страницу приложение будет извлекать текущую страницу из стека.

В Xamarin.Forms есть класс [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), который управляет стеком объектов [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Класс `NavigationPage` также добавляет панель навигации в верхнюю часть страницы, где отображается заголовок и соответствующая платформе кнопка <span class="uiitem">Назад</span>, позволяющая вернуться на предыдущую страницу. В следующем примере кода показано создание оболочки `NavigationPage` для первой страницы в приложении.

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new MainPage ());
}
```

У всех экземпляров [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) есть свойство[`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/), которое предоставляет методы для изменения стека страниц. Эти методы должны вызываться только в случае, если приложение содержит класс [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/). Для перехода к `CallHistoryPage` необходимо вызвать метод [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/), как показано в следующем примере кода:

```csharp
async void OnCallHistory(object sender, EventArgs e)
{
    await Navigation.PushAsync (new CallHistoryPage ());
}
```

В результате новый объект `CallHistoryPage` отправляется в стек навигации. Чтобы вернуться на исходную страницу программным образом, объект `CallHistoryPage` должен вызвать метод [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), как показано в следующем примере кода:

```csharp
await Navigation.PopAsync();
```

Однако в приложении Phoneword этот код не требуется, так как класс [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) добавляет панель навигации в верхнюю часть страницы, где находится соответствующая платформе кнопка <span class="uiitem">Назад</span>, которая будет возвращать на предыдущую страницу.

## <a name="data-binding"></a>Привязка данных

Привязка данных используется для упрощения способа, которым приложение Xamarin.Forms отображает данные и взаимодействует с ними. Она устанавливает связь между пользовательским интерфейсом и базовым приложением. Класс [`BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) содержит основную часть инфраструктуры для поддержки привязки данных.

Привязка данных определяет связь между двумя объектами. *Исходный* объект будет предоставлять данные. *Целевой* объект будет использовать (и часто отображать) данные из исходного объекта. В приложении Phoneword целевым объектом привязки является элемент управления [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), отображающий номера телефонов, а исходным объектом привязки является коллекция `PhoneNumbers`.

Коллекция `PhoneNumbers` объявляется и инициализируется в классе `App`, как показано в следующем примере кода:

```csharp
public partial class App : Application
{
   public static List<string> PhoneNumbers { get; set; }

   public App ()
   {
     PhoneNumbers = new List<string>();
     ...
   }
   ...
}
```

Экземпляр [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) объявляется и инициализируется в классе `CallHistoryPage`, как показано в следующем примере кода:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    ...
    <ContentPage.Content>
       ...
       <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
       ...
    </ContentPage.Content>
</ContentPage>
```

В этом примере элемент управления [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) будет отображать коллекцию данных `IEnumerable`, к которой осуществляется привязка свойства [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView.ItemsSource/). Коллекцией данных могут быть объекты любого типа, но по умолчанию `ListView` будет использовать метод `ToString` каждого элемента для отображения этого элемента. Расширение разметки [`x:Static`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) позволяет указать, что свойство `ItemsSource`будет привязано к статическому свойству `PhoneNumbers` класса `App`, который может быть расположен в пространстве имен `local`.

Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md). Дополнительные сведения о расширениях разметки XAML см. в статье [Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="additional-concepts-introduced-in-phoneword"></a>Дополнительные понятия, представленные в Phoneword

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) отвечает за отображение коллекции элементов на экране. Каждый элемент в `ListView` содержится в отдельной ячейке. Дополнительные сведения об использовании элемента управления `ListView` см. в статье о [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="summary"></a>Сводка

В этой статье были представлены такие понятия, как навигация по страницам и привязка данных в приложении Xamarin.Forms, и показано их использование в кроссплатформенном приложении с несколькими экранами.
