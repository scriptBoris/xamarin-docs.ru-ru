---
title: Навигация в приложении предприятия
description: В данной главе объясняется, как мобильное приложение eShopOnContainers выполняет просмотр моделей в первую очередь навигации с Просмотр моделей.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994774"
---
# <a name="enterprise-app-navigation"></a>Навигация в приложении предприятия

Xamarin.Forms поддерживает навигацию по страницам, что обычно приводит к от взаимодействия пользователя с помощью пользовательского интерфейса или из самого приложения, в результате изменения внутреннего состояния на основе логики. Тем не менее Навигация может быть сложно реализовать в приложениях, использующих шаблон Model-View-ViewModel (MVVM), как должны быть выполнены следующие задачи:

-   Как определить представление для навигации, используя подход, который не вносит тесной связи и зависимости между представлениями.
-   Как для координации процесса, который создан и инициализирован представлении, чтобы осуществить переход. При использовании MVVM, представления и модель представления необходимо создать экземпляр и связанные друг с другом через контекст привязки для представления. Когда приложение использует контейнер внедрения зависимостей, при создании экземпляра представления и Просмотр моделей может потребоваться механизм определенную конструкцию.
-   Следует ли выполнить первое представление навигации или просмотреть навигации «сначала модель». С навигацией первое представление чтобы перейти к странице ссылается на имя типа представления. Во время навигации создается экземпляр указанное представление, а также его соответствующей модели представления и другие зависимые службы. Альтернативный подход — использовать представление навигации «сначала модель», где страницы, чтобы перейти к ссылается на имя типа модели представления.
-   Как для четко отделить навигационный поведение приложения через представления и Просмотр моделей. Шаблон MVVM обеспечивает разделение между пользовательском Интерфейсе приложения и его представления и бизнес-логики. Тем не менее поведение приложения часто будет охватывать пользовательского интерфейса и презентации части приложения. Пользователь часто будет инициации перехода из представления и представления будут заменены в результате перехода. Тем не менее навигации также часто необходимо инициировать или координируемые из модели представления.
-   Способ передачи параметров во время перехода в целях инициализации. Например если пользователь переходит в режим для обновления сведений о заказе, данные заказа будет передаваться в представление так, чтобы отобразить правильные данные.
-   Способы навигации координаты по оси, чтобы убедиться что всей определенных бизнес-правил. Например пользователи могут получать запрос перед переходе от представления, чтобы их можно устранить любые недопустимые данные или предложено отправить или отменить все изменения данных, которые были внесены в представлении.

В этой главе решают эти задачи, предоставляя `NavigationService` класс, используемый для выполнения Навигация по страницам основанная на модели представления.

> [!NOTE]
> `NavigationService` Используется приложение, предназначенное только для выполнения иерархическую навигацию между экземплярами ContentPage. С помощью службы для перехода между другими типами страницы может привести к непредвиденному поведению.

## <a name="navigating-between-pages"></a>Переходы между страницами

Логика перемещения находятся в коде программной части представления, или в данных привязки модели представления. Поместив логику навигации в представлении может быть самым простым подходом, не легко тестируемых через модульные тесты. Поместив логику навигации в представлении классов модели означает, что логика может быть выполнено через модульные тесты. Кроме того модель представления затем можно реализовать логику для управления навигации, чтобы убедиться, что применяются определенные бизнес-правила. Например, приложение не может позволить пользователю уходите со страницы без предварительного обеспечивая правильность введенных данных.

Объект `NavigationService` класса обычно вызывается из модели представления, для обеспечения возможности тестирования. Тем не менее переход к представлениям из модели представлений требуется Просмотр моделей, ссылающиеся на представления, и особенно представления, которые модель активное представление не связана с, а это не рекомендуется. Таким образом `NavigationService` представленные здесь указывает тип модели представления в качестве целевого объекта для перехода к.

Мобильное приложение eShopOnContainers использует `NavigationService` класс, предоставляющий навигации основанная на модели представления. Этот класс реализует `INavigationService` интерфейс, который показан в следующем примере кода:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Этот интерфейс определяет, что класс, реализующий должен предоставить следующие методы:

|Метод|Цель|
|--- |--- |
|`InitializeAsync`|Выполняет переход к одной из двух страниц, когда запускается приложение.|
|`NavigateToAsync`|Выполняет иерархическая навигация на указанной странице.|
|`NavigateToAsync(parameter)`|Выполняет иерархическая навигация на указанной странице, передавая параметр.|
|`RemoveLastFromBackStackAsync`|Удаляет предыдущую страницу из стека навигации.|
|`RemoveBackStackAsync`|Удаляет все предыдущие страницы из стека навигации.|

Кроме того `INavigationService` интерфейс указывает, что должен предоставить класс, реализующий `PreviousPageViewModel` свойство. Это свойство возвращает тип модели представления, связанные с предыдущей страницы в стеке навигации.

> [!NOTE]
> `INavigationService` Интерфейс обычно будет также указать `GoBackAsync` метод, который используется, чтобы вернуться на предыдущую страницу в стеке навигации программным образом. Тем не менее этот метод отсутствует в мобильном приложении eShopOnContainers, так как это не обязательно.

### <a name="creating-the-navigationservice-instance"></a>Создав экземпляр NavigationService

`NavigationService` Класса, который реализует `INavigationService` интерфейсом, регистрируется как Singleton-класс с помощью контейнера внедрения зависимостей Autofac, как показано в следующем примере кода:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService` Интерфейс разрешается в `ViewModelBase` конструктора класса, как показано в следующем примере кода:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Возвращает ссылку на `NavigationService` объект, хранящийся в Autofac контейнера внедрения зависимостей, который создается с `InitNavigation` метод в `App` класса. Дополнительные сведения см. в разделе [перехода при запуске приложения](#navigating_when_the_app_is_launched).

`ViewModelBase` Класса хранилищ `NavigationService` в экземпляре `NavigationService` свойство типа `INavigationService`. Таким образом, все просмотра классов модели, которые являются производными от `ViewModelBase` класса, можно использовать `NavigationService` свойство для доступа к методам, определяемое `INavigationService` интерфейс. Это позволяет избежать затрат на внедрение `NavigationService` объекта из контейнера внедрения зависимостей Autofac в каждый класс модели представления.

### <a name="handling-navigation-requests"></a>Обработка запросов навигации

Xamarin.Forms предоставляет [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) класс, который обеспечивает иерархическую навигацию, в котором пользователь может переходить по страницам вперед и назад по своему усмотрению. Дополнительные сведения об иерархической навигации см. в статье [Иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Вместо того чтобы использовать [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) класса напрямую, в который заключается приложения eShopOnContainers `NavigationPage` в класс `CustomNavigationView` класса, как показано в следующем примере кода:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

Этот перенос предназначена для упрощения стилей [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляра в файле XAML для класса.

Переходы выполняются внутри классов модели представления, вызвав один из `NavigateToAsync` методы, указав тип модели представления для страницы, на которой осуществляется переход к, как показано в следующем примере кода:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

В следующем коде показано в примере `NavigateToAsync` методы, предоставляемые `NavigationService` класса:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Каждый метод позволяет любой класс модели представления, который является производным от `ViewModelBase` классом для выполнения иерархической навигации, вызвав `InternalNavigateToAsync` метод. Кроме того второй `NavigateToAsync` метод позволяет указать как аргумент, передаваемый модели представления, куда выполняется переход, где он обычно используется для инициализации данных навигации. Дополнительные сведения см. в разделе [передачи параметров во время навигации](#passing_parameters_during_navigation).

`InternalNavigateToAsync` Метод выполняет запрос навигации и показан в следующем примере кода:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync` Метод выполняет переход к модели представления, сначала вызывается метод `CreatePage` метод. Этот метод осуществляет поиск представление, соответствующее типу модели указанное представление и создает и возвращает экземпляр этого типа представления. Обнаружение представления, соответствующую типу модели представления использует подход на основе соглашений, в который предполагается, что:

-   Представления находятся в той же сборке, как типы модели представления.
-   Представления находятся в. Дочернее пространство имен представлений.
-   Просмотр моделей находятся в. Дочернее пространство имен ViewModels.
-   Имена представлений соответствуют просмотреть имена модели, с помощью «Модель» удален.

При создании экземпляра представления, она сопоставляется с его соответствующей модели представления. Дополнительные сведения о том, как это происходит, см. в разделе [автоматическое создание модели представления с указателем модели представления](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Если создается представление `LoginView`, он упакован в новый экземпляр класса `CustomNavigationView` класса и назначены [ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойство. В противном случае `CustomNavigationView` извлекается экземпляр и указано, что это не null, [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage) метод вызывается для принудительной отправки представление создаваемого в стек навигации. Тем не менее если полученный `CustomNavigationView` экземпляр `null`, представление создаваемого упакован в новый экземпляр класса `CustomNavigationView` класса и назначены `Application.Current.MainPage` свойство. Этот механизм гарантирует, что во время навигации, добавляются страницы правильно в стек навигации при является пустым, и если она содержит данные.

> [!TIP]
> Рассмотрите возможность кэширования страниц. Кэширование страниц приводит к потребление памяти для представления, которые в настоящее время не отображаются. Тем не менее без кэширования страниц это означает, что синтаксического анализа XAML и создания страниц и его модель представления будет выполняться в новой страницы осуществляется переход, что может повлиять на производительность для сложных страницы. Для хорошо спроектированной страницы, которая не использует слишком много элементов управления производительность будет достаточно. Тем не менее кэширование страниц может помочь при обнаружении медленных страниц время загрузки.

После создания и осуществлен переход, представление `InitializeAsync` метода связанное представление модели представления. Дополнительные сведения см. в разделе [передачи параметров во время навигации](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Перемещение приложения при запуске

При запуске приложения, `InitNavigation` метод в `App` класса вызывается. В следующем примере кода показан этот метод.

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

Создает новый метод `NavigationService` объекта в контейнер внедрения зависимостей Autofac и возвращает ссылку на него, перед вызовом его `InitializeAsync` метод.

> [!NOTE]
> Когда `INavigationService` интерфейс разрешается путем `ViewModelBase` класса контейнера возвращает ссылку на `NavigationService` объект, который был создан при вызове метода InitNavigation.

В следующем коде показано в примере `NavigationService` `InitializeAsync` метод:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView` Выполняется переход к, если приложение имеет доступ кэшированный маркер, который используется для проверки подлинности. В противном случае `LoginView` , к которому осуществляется переход.

Дополнительные сведения о Autofac контейнера внедрения зависимостей, см. в разделе [введение внедрения зависимостей](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Передача параметров во время навигации

Один из `NavigateToAsync` методов, указанных по `INavigationService` интерфейс навигации данных как аргумент, передаваемый модели представления, куда выполняется переход, где он обычно используется для выполнения инициализации.

Например `ProfileViewModel` класс содержит `OrderDetailCommand` , выполняется, когда пользователь выбирает заказ на `ProfileView` страницы. В свою очередь, это выполняет `OrderDetailAsync` метод, который показан в следующем примере кода:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Этот метод вызывает переход к `OrderDetailViewModel`, передав `Order` экземпляр, который представляет порядок, в котором пользователь выбрал в `ProfileView` страницы. Когда `NavigationService` класс создает `OrderDetailView`, `OrderDetailViewModel` класса создается и назначенные представления [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). После перехода по `OrderDetailView`, `InternalNavigateToAsync` выполняет метод `InitializeAsync` метод представления, связанного с модели представления.

`InitializeAsync` Метод определен в `ViewModelBase` класса в качестве метода, который может быть переопределен. Этот метод задает `object` аргумент, который представляет данные, передаваемые модель представления во время операции навигации. Таким образом, классы модели представления, которые хотите получать данные из операции навигации предоставляют свою собственную реализацию `InitializeAsync` метод для выполнения инициализации. В следующем коде показано в примере `InitializeAsync` метода из `OrderDetailViewModel` класса:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Этот метод извлекает `Order` экземпляр, который был передан в модели представления во время операции навигации и использует его для получения полного порядок сведения из `OrderService` экземпляра.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Вызов навигации с помощью поведений

Навигация обычно инициируется из представления взаимодействия с пользователем. Например `LoginView` выполняет навигации, после успешной проверки подлинности. В следующем примере кода показан способ вызова посредством поведения навигации:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

Во время выполнения `EventToCommandBehavior` будет отвечать на действия с [ `WebView` ](xref:Xamarin.Forms.WebView). Когда `WebView` переходит на веб-страницу [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) событие будет срабатывать, которой будет выполнен `NavigateCommand` в `LoginViewModel`. По умолчанию аргументы события для события передаются в команду. Эти данные преобразуются при передаче между источником и целью конвертера, указанный в `EventArgsConverter` свойство, которое возвращает [ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url) из [ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs). Таким образом, когда `NavigationCommand` будет выполнен, URL-адрес веб-страницы, передается в качестве параметра к зарегистрированному `Action`.

В свою очередь `NavigationCommand` выполняет `NavigateAsync` метод, который показан в следующем примере кода:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Этот метод вызывает переход к `MainViewModel`, и выполнив навигации, удаляет `LoginView` страницу из стека навигации.

### <a name="confirming-or-cancelling-navigation"></a>Подтверждения или отмены навигации

Взаимодействия с пользователем во время операции навигации, чтобы пользователь может подтвердить или отменить перемещение могут понадобиться приложению. Это может потребоваться, например, когда пользователь пытается перейти до полностью оформили страницы записи данных. В этом случае приложения должен предоставить уведомление, которое позволяет пользователю перемещаться за пределы страницы, или для отмены операции навигации, перед его выполнением. Это можно сделать в класс модели представления с помощью ответа для уведомления для управления, вызывается ли навигации.

## <a name="summary"></a>Сводка

Xamarin.Forms поддерживает навигацию по страницам, что обычно приводит к от взаимодействия пользователя с помощью пользовательского интерфейса, или в приложении, в результате изменения внутреннего состояния на основе логики. Тем не менее навигации могут быть сложными для реализации в приложениях, использующих шаблон MVVM.

В этой главе представлены `NavigationService` класс, который используется для выполнения переходов основанная на модели представления из модели представления. Поместив логику навигации в представлении классов модели означает, что логика может быть выполнено через автоматические тесты. Кроме того модель представления затем можно реализовать логику для управления навигации, чтобы убедиться, что применяются определенные бизнес-правила.


## <a name="related-links"></a>Связанные ссылки

- [Скачайте электронную книгу (2 МБ в формате PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (пример)](https://github.com/dotnet-architecture/eShopOnContainers)
