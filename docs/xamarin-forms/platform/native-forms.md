---
title: Xamarin.Forms в проектах Xamarin Native
description: В этой статье объясняется, как использовать производные ContentPage страниц, которые напрямую добавляются в собственные проекты Xamarin и как перемещаться между ними.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 04d435b29f6f2f577df5025995fcc074ba5d9d9d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122755"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms в проектах Xamarin Native

_Исходные формы позволяют производным Xamarin.Forms ContentPage страниц для использования в собственных проектах Xamarin.iOS, Xamarin.Android и универсальной платформы Windows (UWP). Собственные проекты могут потреблять производным ContentPage страниц, которые добавляются непосредственно в проект, или из библиотеки .NET Standard, библиотеку .NET Standard или общий проект. Эта статья объясняет, как использовать производные ContentPage страниц, которые напрямую добавляются в собственные проекты и как перемещаться между ними._

Как правило, приложения Xamarin.Forms включает в себя одну или несколько страниц, которые являются производными от [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), и эти страницы являются общими для всех платформ в проекте библиотеки .NET Standard или общий проект. Тем не менее, исходные формы позволяет `ContentPage`-производным страницы, чтобы добавить непосредственно в собственных приложений Xamarin.iOS, Xamarin.Android и универсальной платформы Windows. По сравнению с необходимости использовать собственный проект `ContentPage`-производных страниц из проекта библиотеки .NET Standard или общий проект, добавление страниц проектов в машинном коде преимущество — что страниц можно расширить с помощью собственного представления. Исходные представления затем можно присвоить имя в XAML с `x:Name` и на которые имеются ссылки из кода. Дополнительные сведения о собственном представлениях см. в разделе [исходные представления](~/xamarin-forms/platform/native-views/index.md).

Процесс получения Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производных от page в собственный проект выглядит следующим образом:

1. Добавьте пакет Xamarin.Forms NuGet в собственный проект.
1. Добавить [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производных страницы и зависимые компоненты в собственный проект.
1. Вызовите метод `Forms.Init`.
1. Сконструировать экземпляр [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-страница производной и преобразовать его в соответствующий собственный тип, с помощью одного из следующих методов расширения: `CreateViewController` для iOS, `CreateFragment` или `CreateSupportFragment` для Android, или `CreateFrameworkElement` для универсальной платформы Windows.
1. Перейдите в представление собственного типа [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-страница, с помощью собственного API-навигации производной.

Xamarin.Forms должны инициализироваться, вызвав `Forms.Init` метод, прежде чем можно создать собственный проект [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-страница производной. Выбор места для этого в основном зависит от времени наиболее удобное в блок-схеме приложения — может быть выполнена при запуске приложения или непосредственно перед `ContentPage`-создается производный страницы. В этой статье и сопутствующие примеры приложений `Forms.Init` метод вызывается при запуске приложения.

> [!NOTE]
> **NativeForms** примера приложения решения не содержит все проекты Xamarin.Forms. Вместо этого он состоит из проекта Xamarin.iOS, проект Xamarin.Android и проекта универсальной платформы Windows. Каждый проект имеет собственный проект, использующий собственные формы для использования [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страниц. Тем не менее, нет причин, почему не могли использовать собственные проекты `ContentPage`-страниц на основе проекта библиотеки .NET Standard или общий проект.

Если вы используете собственные формы, Xamarin.Forms такие функции, как [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)и механизм привязки данных, все по-прежнему работали. Тем не менее Навигация по страницам выполняется с помощью собственного API-навигации.

## <a name="ios"></a>iOS

В iOS `FinishedLaunching` в Переопределите `AppDelegate` класса обычно является местом для выполнения приложения задач, связанных с запуска. Он вызывается после запуска и приложения обычно переопределяется, чтобы настроить главное окно и просмотреть контроллера. В следующем коде показано в примере `AppDelegate` класс в приложении-примере:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

`FinishedLaunching` Метод выполняет следующие задачи:

- Xamarin.Forms инициализируется путем вызова `Forms.Init` метод.
- Ссылку на `AppDelegate` класса хранится в `static` `Instance` поля. Это позволяет предоставить механизм для других классов для вызова методов, определенных в `AppDelegate` класса.
- `UIWindow`, Который является основной контейнер для представлений в собственные приложения iOS, создается.
- `PhonewordPage` Класс, являющийся Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы, определенные в XAML, создается и преобразовать `UIViewController` с помощью `CreateViewController` метода расширения.
- `Title` Свойство `UIViewController` имеет значение, отображаемые на `UINavigationBar`.
- Объект `UINavigationController` создается для управления иерархической навигации. `UINavigationController` Класс управляет стеком контроллеров представлений и `UIViewController` передан в конструктор отображается изначально при `UINavigationController` загружается.
- `UINavigationController` Экземпляр задан в качестве верхнего уровня `UIViewController` для `UIWindow`и `UIWindow` задан в качестве ключа окна для приложения и является видимым.

Один раз `FinishedLaunching` успешного выполнения метода, определенного пользовательского интерфейса в Xamarin.Forms `PhonewordPage` класс будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

Взаимодействие с пользовательским Интерфейсом, например, коснувшись [ `Button` ](xref:Xamarin.Forms.Button), приведет к обработчиков событий в `PhonewordPage` выполнение кода. Например, когда пользователь касается **Call History** выполняется следующий обработчик событий кнопки:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

`static` `AppDelegate.Instance` Поле позволяет `AppDelegate.NavigateToCallHistoryPage` метод должен быть вызван, как показано в следующем примере кода:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

`NavigateToCallHistoryPage` Метод преобразует Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-страница для производной `UIViewController` с `CreateViewController` метод расширения и наборы `Title` свойство `UIViewController`. `UIViewController` Затем помещается в `UINavigationController` по `PushViewController` метод. Таким образом, пользовательский Интерфейс определен в Xamarin.Forms `CallHistoryPage` класс будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

При `CallHistoryPage` отображается, коснувшись обратной стрелки появится всплывающее `UIViewController` для `CallHistoryPage` класса из `UINavigationController`, возвращая пользователю `UIViewController` для `PhonewordPage` класса.

## <a name="android"></a>Android

В Android `OnCreate` в Переопределите `MainActivity` класса обычно является местом для выполнения приложения задач, связанных с запуска. В следующем коде показано в примере `MainActivity` класс в приложении-примере:

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

`OnCreate` Метод выполняет следующие задачи:

- Xamarin.Forms инициализируется путем вызова `Forms.Init` метод.
- Ссылку на `MainActivity` класса хранится в `static` `Instance` поля. Это позволяет предоставить механизм для других классов для вызова методов, определенных в `MainActivity` класса.
- `Activity` Содержимое устанавливается из ресурса макета. В приложении-примере макет состоит из `LinearLayout` , содержащий `Toolbar`и `FrameLayout` в качестве контейнера фрагмента.
- `Toolbar` Извлекается и задать в качестве панели действий для `Activity`, и задайте заголовок панели действий.
- `PhonewordPage` Класс, являющийся Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы, определенные в XAML, создается и преобразовать `Fragment` с помощью `CreateFragment` метода расширения.
- `FragmentManager` Класс создает и фиксирует транзакцию, которая заменяет `FrameLayout` с экземпляром `Fragment` для `PhonewordPage` класса.

Дополнительные сведения о фрагментах см. в разделе [фрагментов](~/android/platform/fragments/index.md).

> [!NOTE]
> В дополнение к `CreateFragment` метод расширения, Xamarin.Forms также входит `CreateSupportFragment` метод. `CreateFragment` Метод создает `Android.App.Fragment` , можно использовать в приложениях, предназначенных для API 11 и выше. `CreateSupportFragment` Метод создает `Android.Support.V4.App.Fragment` , можно использовать в приложениях, предназначенных для версий API до 11.

Один раз `OnCreate` успешного выполнения метода, определенного пользовательского интерфейса в Xamarin.Forms `PhonewordPage` класс будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "Android PhonewordPage")

Взаимодействие с пользовательским Интерфейсом, например, коснувшись [ `Button` ](xref:Xamarin.Forms.Button), приведет к обработчиков событий в `PhonewordPage` выполнение кода. Например, когда пользователь касается **Call History** выполняется следующий обработчик событий кнопки:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainActivity.Instance` Поле позволяет `MainActivity.NavigateToCallHistoryPage` метод должен быть вызван, как показано в следующем примере кода:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

`NavigateToCallHistoryPage` Метод преобразует Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-страница для производной `Fragment` с `CreateFragment` метод расширения и добавляет `Fragment` в тот фрагмент обратно в стек. Таким образом, пользовательский Интерфейс определен в Xamarin.Forms `CallHistoryPage` будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "Android CallHistoryPage")

При `CallHistoryPage` отображается, коснувшись обратной стрелки появится всплывающее `Fragment` для `CallHistoryPage` из фрагмента стек переходов назад, возвращая пользователю `Fragment` для `PhonewordPage` класса.

### <a name="enabling-back-navigation-support"></a>Включение поддержки переходов назад

`FragmentManager` Класс имеет `BackStackChanged` события, возникающего при каждом изменении содержимого фрагмента стек переходов назад. `OnCreate` Метод в `MainActivity` класс содержит анонимный обработчик для этого события:

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Этот обработчик событий отображает кнопки "Назад" на панели действий, при условии, что имеется один или несколько `Fragment` свернет во фрагменте стека. Ответ при нажатии кнопки "Назад", обрабатывается `OnOptionsItemSelected` переопределить:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

`OnOptionsItemSelected` Переопределение вызывается каждый раз при выборе элемента в меню "Параметры". Эта реализация извлекает текущий фрагмент из фрагмента стек переходов назад, если был выбран "Назад", а также есть один или несколько `Fragment` свернет во фрагменте стека.

### <a name="multiple-activities"></a>Несколько действий

Если приложение состоит из нескольких действий [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производных страниц могут быть внедрены в каждое из действий. В этом случае `Forms.Init` метод должны вызываться только в `OnCreate` переопределить первого `Activity` , внедряет Xamarin.Forms `ContentPage`. Тем не менее это имеет следующие последствия:

- Значение `Xamarin.Forms.Color.Accent` будет взято из `Activity` вызвавшую `Forms.Init` метод.
- Значение `Xamarin.Forms.Application.Current` будут связаны с `Activity` вызвавшую `Forms.Init` метод.

### <a name="choosing-a-file"></a>Выбор файла

При внедрении [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы, использующей [ `WebView` ](xref:Xamarin.Forms.WebView) , требуется для поддержки HTML «Выбрать файл» кнопки, `Activity` необходимо переопределить `OnActivityResult` метод:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

На UWP, собственного `App` класса обычно является местом для выполнения приложения задач, связанных с запуска. Xamarin.Forms обычно инициализируется, в приложениях универсальной платформы Windows в `OnLaunched` переопределить в собственной `App` класс, для передачи `LaunchActivatedEventArgs` аргумент `Forms.Init` метод. По этой причине собственных приложений универсальной платформы Windows, которые используют Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производных от page, проще всего можно вызвать `Forms.Init` метода из `App.OnLaunched` метод.

По умолчанию, собственного `App` класса запускает `MainPage` класс как первая страница приложения. В следующем коде показано в примере `MainPage` класс в приложении-примере:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

`MainPage` Конструктор выполняет следующие задачи:

- На странице включено кэширование, чтобы новый `MainPage` не создается, когда пользователь переходит на страницу.
- Ссылку на `MainPage` класса хранится в `static` `Instance` поля. Это позволяет предоставить механизм для других классов для вызова методов, определенных в `MainPage` класса.
- `PhonewordPage` Класс, являющийся Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы, определенные в XAML, создается и преобразовать `FrameworkElement` с помощью `CreateFrameworkElement` метод расширения, а затем задайте как содержимое `MainPage` класса.

Один раз `MainPage` конструктор выполнен, пользовательский Интерфейс определен в Xamarin.Forms `PhonewordPage` класс будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage универсальной платформы Windows")

Взаимодействие с пользовательским Интерфейсом, например, коснувшись [ `Button` ](xref:Xamarin.Forms.Button), приведет к обработчиков событий в `PhonewordPage` выполнение кода. Например, когда пользователь касается **Call History** выполняется следующий обработчик событий кнопки:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainPage.Instance` Поле позволяет `MainPage.NavigateToCallHistoryPage` метод должен быть вызван, как показано в следующем примере кода:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Навигация в универсальной платформы Windows обычно выполняется с помощью `Frame.Navigate` метод, который принимает `Page` аргумент. Xamarin.Forms определяет `Frame.Navigate` метод расширения, который принимает [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производный экземпляр страницы. Таким образом, когда `NavigateToCallHistoryPage` выполняет метод, пользовательский Интерфейс, определенный в Xamarin.Forms `CallHistoryPage` будет отображаться, как показано на следующем снимке экрана:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage универсальной платформы Windows")

При `CallHistoryPage` отображается, коснувшись обратной стрелки появится всплывающее `FrameworkElement` для `CallHistoryPage` из стек переходов назад в приложении, возвращая пользователю `FrameworkElement` для `PhonewordPage` класса.

### <a name="enabling-back-navigation-support"></a>Включение поддержки переходов назад

На универсальной платформы Windows приложения необходимо включить переходов назад для всех оборудования и программного обеспечения назад кнопок, на другое устройство конструктивными. Это можно сделать, зарегистрировав обработчик событий для `BackRequested` событие, которое может быть выполнена в `OnLaunched` метод в собственной `App` класса:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

При запуске приложения, `GetForCurrentView` метод извлекает `SystemNavigationManager` объект связанный с текущим представлением, а затем регистрирует обработчик событий для `BackRequested` событий. Приложение получает это событие, только если его активным, а в ответ, вызывает `OnBackRequested` обработчик событий:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

`OnBackRequested` Вызовов обработчика событий `GoBack` метод корневого фрейма приложения и наборы `BackRequestedEventArgs.Handled` свойства `true` пометить событие как обработанное. Пометить событие как обработанное может появиться в системе или события (семейство настольных устройств) в переходе от приложения (на семейства мобильных устройств).

Приложение полагается на предоставленном системой кнопки возврата на телефоне, но выбирает, нужно ли отображать кнопки "Назад" в строке заголовка для настольных компьютеров. Это достигается путем установки `AppViewBackButtonVisibility` задается одно из `AppViewBackButtonVisibility` значений перечисления:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated` Обработчик событий, который выполняется в ответ на `Navigated` события, обновляет видимость заголовка окна кнопки "Назад", когда происходит переход по страницам. Это гарантирует, что Назад кнопка панели заголовка является видимым, если стек переходов назад в приложении не является пустым или удалены из заголовка, если стек переходов назад в приложении является пустым.

Дополнительные сведения о поддержке переходов назад на UWP, см. в разделе [истории переходов и обратной навигации для приложений UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Сводка

Исходные формы разрешить Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страниц для использования в собственных проектах Xamarin.iOS, Xamarin.Android и универсальной платформы Windows (UWP). Способные подключаться к собственным проектам `ContentPage`-производным страниц, которые добавляются непосредственно в проект, или из проекта библиотеки .NET Standard и общий проект. В этой статье описано, как использовать `ContentPage`-производным страниц, которые напрямую добавляются в собственные проекты, а также перемещаться между ними.


## <a name="related-links"></a>Связанные ссылки

- [NativeForms (пример)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Исходные представления](~/xamarin-forms/platform/native-views/index.md)
