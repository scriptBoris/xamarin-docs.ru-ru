---
title: Класс приложения Xamarin.Forms
description: В этой статье описаны функции класса приложения по умолчанию, который содержит свойство, чтобы задать начальную страницу для приложения, и постоянных словарь для хранения простых значений в изменениях состояния жизненного цикла.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 9acd1b8f25696267578f5cc269eb1b0c738be571
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675098"
---
# <a name="xamarinforms-app-class"></a>Класс приложения Xamarin.Forms

`Application` Базовый класс предоставляет следующие возможности, которые предоставляются в используемом по умолчанию проекты `App` подкласс:

* Объект `MainPage` свойство, которое можно задать начальную страницу для приложения.
* Это постоянный [ `Properties` словарь](#Properties_Dictionary) для хранения простых значений в изменениях состояния жизненного цикла.
* Статический `Current` свойство, которое содержит ссылку на текущий объект приложения.

Он также предоставляет [методы жизненного цикла](~/xamarin-forms/app-fundamentals/app-lifecycle.md) например `OnStart`, `OnSleep`, и `OnResume` а также события модальной навигации.

В зависимости от шаблона, который вы выбрали, `App` класса можно определить одним из двух способов:

* **C#**, или
* **XAML И C#**

Для создания **приложения** класса с помощью XAML, значение по умолчанию **приложения** класса должны быть заменены XAML **приложения** класс и выделенным кодом, как показано в следующем примере кода:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

В следующем примере кода показано связанного кода:

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

А также параметр [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойства кода необходимо также вызвать `InitializeComponent` метод, чтобы загрузить и проанализировать связанные XAML.

## <a name="mainpage-property"></a>Свойство MainPage

`MainPage` Свойство `Application` класса задает корневую страницу приложения.

Например, можно создать логику в вашей `App` для отображения на другую страницу, в зависимости от того, вошел ли пользователь или нет.

`MainPage` Свойство должно быть установлено `App` конструктор,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>Словарь свойств

`Application` Подкласс имеет статическое `Properties` словаря, который может использоваться для хранения данных, в частности, для использования в `OnStart`, `OnSleep`, и `OnResume` методы. Это может быть организован в любом месте кода Xamarin.Forms с помощью `Application.Current.Properties`.

`Properties` Использует словарь `string` ключ и сохраняет `object` значение.

Например, можно задать это постоянный `"id"` свойства в любом месте в коде (при выборе элемента на странице `OnDisappearing` метод, или в `OnSleep` метод) следующим образом:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

В `OnStart` или `OnResume` методы, это значение можно затем использовать для повторного создания с пользователем каким-либо образом. `Properties` Хранилищ словарь `object`s, поэтому необходимо привести его значение перед его использованием.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Всегда проверяйте наличие ключа перед получением доступа к его, чтобы предотвратить непредвиденные ошибки.

> [!NOTE]
> `Properties` Словарь только может сериализовать типы-примитивы, для хранения. Необходимо хранить другие типы (такие как `List<string>`) может завершиться ошибкой без уведомления.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Сохраняемость

`Properties` Словарь автоматически сохраняются на устройстве.
Данные, добавляется в словарь, будут доступны при переходе приложения в фоновом режиме, или даже в том случае, после его перезапуска.

Xamarin.Forms 1.4 появился дополнительный метод на `Application` класс - `SavePropertiesAsync()` -которого можно вызывать для сохранения заранее `Properties` словаря. Это позволяет сохранить свойства после важные обновления, а не риска их не сериализуемую из-за сбоя или закрывается Операционной системой.

Можно найти ссылки с помощью `Properties` словарь в **Создание мобильных приложений с помощью Xamarin.Forms** главы книги [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), и [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)и в связанном [примеры](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>Класс Application

Полный `Application` для справки ниже приведен реализацию класса:

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}

```

Затем этот класс создается в каждом проекте под конкретную платформу и передается `LoadApplication` метод, что выгодно `MainPage` загружается и отображается для пользователя.
В следующих разделах показан код для каждой платформы. Последние шаблоны решения Xamarin.Forms уже содержать весь этот код, предварительно настроенного для вашего приложения.


### <a name="ios-project"></a>Проект iOS

IOS `AppDelegate` класс наследует от `FormsApplicationDelegate`. Оно должно:

* Вызовите `LoadApplication` с экземпляром `App` класса.

* Всегда возвращает `base.FinishedLaunching (app, options);`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Проект Android

Android `MainActivity` наследует от `FormsAppCompatActivity`. В `OnCreate` переопределить `LoadApplication` был вызван с помощью экземпляра `App` класса.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Проект универсальной Windows (UWP) для Windows 10

См. в разделе [проекты установки Windows](~/xamarin-forms/platform/windows/installation/index.md) сведения о поддержке универсальной платформы Windows в Xamarin.Forms.

Главную страницу в проекте универсальной платформы Windows должен быть производным от `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

C# Кода программной части конструкции необходимо вызвать `LoadApplication` для создания экземпляра вашего Xamarin.Forms `App`. Обратите внимание, что следует явно использовать пространство имен приложения для уточнения `App` поскольку приложений универсальной платформы Windows, также имеют свои собственные `App` класс, не имеющих отношения к Xamarin.Forms.

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Обратите внимание, что `Forms.Init()` должен вызываться **App.xaml.cs** около строки 63.
