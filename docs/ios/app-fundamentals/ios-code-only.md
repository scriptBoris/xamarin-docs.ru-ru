---
title: Создание iOS пользовательских интерфейсов в коде в Xamarin.iOS
description: В этом документе описывается, как использовать код для создания пользовательского интерфейса для приложения Xamarin.iOS. В нем описывается просмотр контроллеров построения представление иерархии, обработка, поворот и многое другое.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: 688457ab25398e8c5b9848a7e58f6163db4c0a05
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242398"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Создание iOS пользовательских интерфейсов в коде в Xamarin.iOS

Пользовательский интерфейс приложения iOS подобно онлайн-магазина — приложение обычно получает одно окно, но ее можно заполнить окно как многие объекты ей, а объекты и схемы упорядочения может изменяться в зависимости от того, какое приложение хочет отобразить. В этом сценарии объекты — то, что видит пользователь — называются представлениями. Чтобы создать один экран в приложении, представления располагаются друг над другом в иерархию представлений содержимого и иерархии, которыми управляет отдельный контроллер представления. Приложения с несколькими экранами используют несколько иерархий представлений содержимого, каждая из которых имеет свой контроллер представления. Приложение размещает представления в окне, чтобы создать другую иерархию представлений содержимого в зависимости от экрана, на котором находится пользователь.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

На следующей схеме показаны связи между окном, представлениями, вложенными представлениями и контроллером представления, которые позволяют вывести пользовательский интерфейс на экран устройства: 

[![](ios-code-only-images/image9.png "На этой схеме показаны связи между окном, представлениями, представлений и контроллера представления")](ios-code-only-images/image9.png#lightbox)

Эти представления иерархии может быть создан с помощью [конструктор Xamarin для iOS](~/ios/user-interface/designer/index.md) в Visual Studio, однако полезно иметь основные сведения о работе полностью в коде. В этой статье рассматриваются некоторые основные точки, чтобы приступить к работе в разработки интерфейса пользователя только для кода.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

На следующей схеме показаны связи между окном, представлениями, вложенными представлениями и контроллером представления, которые позволяют вывести пользовательский интерфейс на экран устройства: 

[![](ios-code-only-images/image9.png "На этой схеме показаны связи между окном, представлениями, представлений и контроллера представления")](ios-code-only-images/image9.png#lightbox)

Эти представления иерархии может быть создан с помощью [конструктор Xamarin для iOS](~/ios/user-interface/designer/index.md) в Visual Studio для Mac, однако полезно иметь основные сведения о работе полностью в коде. В этой статье рассматриваются некоторые основные точки, чтобы приступить к работе в разработки интерфейса пользователя только для кода.

-----

## <a name="creating-a-code-only-project"></a>Создание проекта, доступных только для кода

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="ios-blank-project-template"></a>Пустой шаблон проекта iOS

Сначала создайте проект iOS в Visual Studio с помощью **файл > Новый проект > Visual C# > iPhone & iPad > iOS (Xamarin) приложение** проекта, показано ниже:

[![Диалоговое окно нового проекта](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Затем выберите **пустое приложение** шаблона проекта:

[![Выберите диалоговое окно шаблона](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Шаблон пустого проекта добавляет 4 файлы в проект.

[![Файлы проекта](ios-code-only-images/empty-project.w157-sml.png "файлы проекта")](ios-code-only-images/empty-project.w157.png#lightbox)


1. **AppDelegate.cs** -содержит `UIApplicationDelegate` подкласс, `AppDelegate` , который используется для обработки событий приложения от операций ввода-вывода. Окно приложения создается в `AppDelegate` `FinishedLaunching` метод.
1. **Main.cs** -содержит точку входа для приложения, который указывает класс для `AppDelegate` .
1. **Info.plist** -файл списка свойств, содержащий сведения о конфигурации приложения.
1. **Entitlements.plist** — файл списка свойств, содержащий сведения о возможностях и разрешения приложения.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

## <a name="ios-templates"></a>iOS шаблоны


Visual Studio для Mac не поддерживает пустой шаблон. Все шаблоны поставляются с поддержкой раскадровки, в которой корпорация Apple рекомендует использовать как основной способ создания пользовательского интерфейса. Тем не менее существует возможность создать свой пользовательский Интерфейс полностью в коде. 

Приведенные ниже инструкции по Удаление раскадровки из приложения. 


1. Используйте приложение с одним представлением шаблон, чтобы создать новый проект iOS:
    
    [![](ios-code-only-images/single-view-app.png "Используйте шаблон приложение с одним представлением")](ios-code-only-images/single-view-app.png#lightbox)

1. Удалить `Main.Storyboard` и `ViewController.cs` файлы. Сделать **не** удалить `LaunchScreen.Storyboard`. Контроллер представления следует удалить, так как он кода программной части для контроллера представления, создаваемая в раскадровку:
1. Не забудьте выбрать **удалить** из всплывающего диалога:
    
    [![](ios-code-only-images/delete.png "Выберите Delete из всплывающего диалога")](ios-code-only-images/delete.png#lightbox)

1. В файле Info.plist, удалите их содержимое **сведения о развертывании > главный интерфейс** параметр:
    
    [![](ios-code-only-images/main-interface.png "Удалять данные в параметре главный интерфейс")](ios-code-only-images/main-interface.png#lightbox)

1. Наконец, добавьте следующий код, чтобы ваши `FinishedLaunching` метод в класс AppDelegate:
        
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }

Код, который был добавлен в `FinishedLaunching` на шаге 5 выше, в противном случае минимальный объем кода, необходимого для создания окна для приложения iOS.


-----



iOS-приложений с помощью [шаблона MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#Model_View_Controller). Первый экран, отображающий приложения создается из корневого контроллера представления окна. См. в разделе [Привет, iOS (несколько экранов)](~/ios/get-started/hello-ios-multiscreen/index.md) руководство для Дополнительные сведения о MVC шаблон сам.

Реализация `AppDelegate` добавленные этот шаблон создает окно приложения, из которого существует только по одному для каждого приложения iOS и делает его видимым, следующим кодом:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

Если вы запустите это приложение сейчас, вы получили бы исключение, создаваемое, о том, что `Application windows are expected to have a root view controller at the end of application launch`. Добавим контроллер и сделать его корневым контроллером представления приложения.

## <a name="adding-a-controller"></a>Добавление контроллера

Приложение может содержать большое количество контроллеров представления, но он должен иметь один корневой контроллер представления для управления контроллерами представления.  Добавление контроллера в окно путем создания `UIViewController` экземпляра и установив значение `window.RootViewController` свойства:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Каждый контроллер имеет связанное представление, доступное из `View` свойство. Приведенный выше код изменяет представление `BackgroundColor` свойства `UIColor.LightGray` таким образом, чтобы он был видим, как показано ниже:

 [![](ios-code-only-images/image1.png "Фон представления является видимым светло-серый")](ios-code-only-images/image1.png#lightbox)

Нужно присвоить любой `UIViewController` подкласс как `RootViewController` таким образом, включая контроллеры UIKit, а также тех, мы напишем сами. Например, следующий код добавляет `UINavigationController` как `RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

В результате получается контроллер, вложенные в контроллер навигации, как показано ниже:

 [![](ios-code-only-images/image2.png "Контроллер, вложенные в контроллер навигации")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Создания контроллера представления

Теперь, когда мы узнали, как по добавлению контроллера в качестве `RootViewController` окна, давайте посмотрим, как создать пользовательский контролер представлений в коде.

Добавьте новый класс с именем `CustomViewController` как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Добавьте новый класс с именем CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![](ios-code-only-images/new-file.png "Добавьте новый класс с именем CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

Класс должен наследовать от `UIViewController`, который находится в `UIKit` пространства имен, как показано:

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

<a name="Initializing_the_View"/>

## <a name="initializing-the-view"></a>Инициализация представления

`UIViewController` содержит метод с именем `ViewDidLoad` , вызываемый, когда контроллер представления сначала загружается в память. Это необходимо выполнить инициализацию представления, такие как настройка его свойств.

Например следующий код добавляет кнопку и обработчик событий для передачи нового контроллера представления в стек навигации при нажатии кнопки:

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Чтобы загрузить этот контроллер в приложении и продемонстрировать простой навигации, создайте новый экземпляр класса `CustomViewController`. Создание нового контроллера навигации, передайте в вашем экземпляре контроллер представления и установите новый контроллер навигации в окно `RootViewController` в `AppDelegate` как и раньше:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Теперь при загрузке приложения, `CustomViewController` загружается внутри контроллер навигации:

 [![](ios-code-only-images/customvc.png "CustomViewController загружается внутри контроллер навигации")](ios-code-only-images/customvc.png#lightbox)
 
Нажав кнопку, будет _принудительной_ контроллер представления в стек навигации:

[![](ios-code-only-images/customvca.png "Стек навигации помещается новый контроллер представления")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Создание иерархии представлений

В приведенном выше примере мы начали создания пользовательского интерфейса в код, добавив кнопки на контроллер представления.

пользовательские интерфейсы iOS состоят из иерархии представлений. Дополнительные представления, такие как метки, кнопки, ползунки и др. добавляются как вложенные представления некоторые родительского представления.

Например, изменим `CustomViewController` создать экран входа, где пользователь может ввести имя пользователя и пароль. Экран будет состоять из двух текстовых полей и кнопки.

### <a name="adding-the-text-fields"></a>Добавление текстового поля

Во-первых, удалить кнопку и событие обработчик, который был добавлен в [инициализации представления](#Initializing_the_View) раздел. 

Добавьте элемент управления для имени пользователя, создание и инициализация `UITextField` и затем добавив его в иерархии представлений, как показано ниже:

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Когда мы создаем `UITextField`, мы устанавливаем `Frame` свойство, чтобы определить его расположение и размер. В iOS точки с координатами 0,0 находится в левом верхнем углу на + x справа и + y вниз. После задания `Frame` и несколько других свойств мы вызываем `View.AddSubview` добавление `UITextField` иерархию представлений. Это делает `usernameField` вложенное представление из `UIView` экземпляр, на котором `View` ссылок на свойства. Вложенное представление добавляется с z порядком, больше, чем его родительского представления, поэтому у него перед родительское представление на экране.

Приложение с помощью `UITextField` включены показан ниже:

 [![](ios-code-only-images/image4.png "Приложение с помощью UITextField включены")](ios-code-only-images/image4.png#lightbox)

Мы можем добавить `UITextField` пароль таким же образом, только на этот раз мы устанавливаем `SecureTextEntry` значение true, как показано ниже:

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField); 
      View.AddSubview(passwordField);
   }
}

```

Установка `SecureTextEntry = true` скрывает текст, введенный в `UITextField` пользователем, как показано ниже:

 [![](ios-code-only-images/image4a.png "Параметр SecureTextEntry значение true позволяет скрывать текст, введенный пользователем")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Добавление кнопки

Далее мы добавим кнопки, поэтому пользователь может отправить имя пользователя и пароль. Будет добавлена кнопка для иерархии представлений, как и любой другой элемент управления, передавая его в качестве аргумента в родительском представлении `AddSubview` метод снова.

Следующий код добавляет кнопку и регистрирует обработчик событий для `TouchUpInside` событий:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

После этого на экране входа будет отображаться как показано ниже:

 [![](ios-code-only-images/image5.png "Экран входа")](ios-code-only-images/image5.png#lightbox)

В отличие от в предыдущих версиях iOS, фона кнопки по умолчанию является прозрачным. Изменение кнопки `BackgroundColor` изменении этого свойства:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Это приведет к квадратную кнопку, а не стандартной округленное Обведенная кнопки. Чтобы получить скругленными edge, используйте следующий фрагмент кода:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Благодаря этим изменениям представление будет выглядеть следующим образом:

[![](ios-code-only-images/image6.png "Пример выполнения представления")](ios-code-only-images/image6.png#lightbox)
 
## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Добавление нескольких представлений в иерархию представлений

iOS предоставляет возможность добавить несколько представлений иерархии представлений с помощью `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>Добавление функциональности кнопки

При нажатии кнопки, пользователи будут рассчитывать происходящие события. Например отображается предупреждение или навигации выполняется на другой экран. 

Добавим код для отправки второго контроллера представления в стек навигации.

Во-первых создайте второй контроллер представления:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Затем добавьте функциональные возможности для `TouchUpInside` событий:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

Области навигации как показано ниже:

[![](ios-code-only-images/navigation.png "На этой диаграмме показано навигации")](ios-code-only-images/navigation.png#lightbox)

Обратите внимание на то, что по умолчанию при использовании контроллера навигации iOS предоставляет приложению панель навигации и кнопки возврата, чтобы можно было переместить обратно через стек.

## <a name="iterating-through-the-view-hierarchy"></a>Итерации по иерархии представлений

Это возможно для прохода по иерархии вложенное представление и выбрать любого конкретного представления. Например, чтобы найти все `UIButton` и присвойте этой кнопки другой `BackgroundColor`, можно использовать следующий фрагмент кода

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

Это, однако будет работать, если представление проходимого для `UIView` как всех представлений будет восстановлен как `UIView` как объекты, добавляемые в родительском представлении сами наследовать `UIView`.

## <a name="handling-rotation"></a>Обработка поворота

При повороте устройства на альбомную, элементы управления не меняют размер соответствующим образом, как показано на следующем снимке экрана:

 [![](ios-code-only-images/image7.png "При повороте устройства на альбомную, элементы управления не меняют размер соответствующим образом")](ios-code-only-images/image7.png#lightbox)

— Один из способов, чтобы устранить эту проблему, задав `AutoresizingMask` свойство в каждом представлении. В этом случае мы хотим элементы управления для растяжения по горизонтали, поэтому мы бы установили для каждого `AutoresizingMask`. Следующий пример приведен для `usernameField`, но также потребуется применить для каждого мини-приложения в иерархии представлений.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Теперь когда мы повернуть устройство или эмулятор, все, что растягивается, заполняя дополнительное пространство, как показано ниже:

 [![](ios-code-only-images/image8.png "Все элементы управления растягиваются для заполнения объем дополнительного пространства")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Создание настраиваемых представлений

Помимо использования элементов управления, которые являются частью UIKit, можно также использовать настраиваемые представления. Представления могут создаваться путем наследования от `UIView` и переопределение `Draw`. Давайте создать пользовательское представление и добавьте его в иерархию представлений для демонстрации.

### <a name="inheriting-from-uiview"></a>Наследование от UIView

Первое, что нам нужно сделать — это создать класс настраиваемого представления. Мы сделаем это с помощью **класс** шаблона в Visual Studio, чтобы добавить пустой класс с именем `CircleView`. Базовый класс должно быть присвоено `UIView`, который мы Напомним находится в `UIKit` пространства имен. Нам также потребуется `System.Drawing` также пространство имен. Другие различных `System.*` пространств имен не будет используется в этом примере, поэтому вы можете удалить их.

Класс должен выглядеть следующим образом:

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Рисование в UIView

Каждый `UIView` имеет `Draw` метод, который вызывается системой, когда он должен быть нарисован. `Draw` вызывать не следует напрямую. Вызывается системой во время выполнения цикла обработки. В первый раз с помощью выполнения цикла после добавления представления иерархии представлений, его `Draw` вызывается метод. Последующие вызовы `Draw` возникать, когда представление помечается как нуждающийся в рисуется с помощью вызова `SetNeedsDisplay` или `SetNeedsDisplayInRect` в представлении.

Можно добавить код рисования нашего представления, добавив такой код внутри переопределенный `Draw` метод, как показано ниже:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Так как `CircleView` — `UIView`, мы также можно задать `UIView` также свойства. Например, можно установить `BackgroundColor` в конструкторе:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Чтобы использовать `CircleView` мы только что создали, мы можем либо добавить его как вложенное представление иерархию представлений в контроллере, как мы сделали с `UILabels` и `UIButton` ранее, или мы можем загрузить в виде нового контроллера. Давайте сделаем последний.

### <a name="loading-a-view"></a>Загрузка представления

 `UIViewController` метод с именем `LoadView` вызывается на контроллере, чтобы создать его представление. Это необходимо создать представление и его назначение контроллера `View` свойство.

Во-первых, нужен контроллер, поэтому создайте пустой класс с именем `CircleController`.

В `CircleController` добавьте следующий код, чтобы задать `View` для `CircleView` (не следует вызывать `base` реализацию в переопределении):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Наконец нам нужно предоставить контроллеру во время выполнения. Давайте это сделать, добавив обработчик событий "Отправить", который мы добавили ранее, как показано ниже:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Теперь когда мы запустите приложение и нажмите кнопку "Отправить", отображается новое представление круг:

 [![](ios-code-only-images/circles.png "Отображается новое представление круг")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Создание экрана запуска

Объект [экран запуска](~/ios/app-fundamentals/images-icons/launch-screens.md) отображается при запуске приложения, как способ отображения для пользователей, что оно отвечает. Так как экран запуска отображается при загрузке приложения, не может быть создан в коде, как приложение по-прежнему загружается в память. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Когда ваш Создание проекта в Visual Studio, на экран запуска предоставляется в виде файла .xib, который можно найти в iOS **ресурсы** папки внутри проекта. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Если ваш создайте проект iOS в Visual Studio для Mac, на экран запуска предоставляется автоматически в виде файла раскадровки. 

-----

Это можно изменить с помощью двойных щелкните ее и открыв его в конструкторе iOS.

Apple рекомендует .xib или Storyboard-файл используется для приложений, нацеленных на iOS 8 или более поздней версии, при запуске файла, либо в конструкторе iOS, вы будете использовать классы размера и автоматический макет для адаптации макета, чтобы он вас устраивает и отображаются правильно, для всех устройств размеры. Изображение статический запуска можно использовать в дополнение к .xib или раскадровки для поддержки приложений, предназначенных для более ранних версий.

Дополнительные сведения о создании экрана запуска см. в документах ниже:

- [Создание с помощью .xib экрана запуска](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Управление экранов запуска с помощью Storyboard](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> Начиная с версии iOS 9 компания Apple рекомендует что раскадровки следует использовать в качестве основного способа создания экрана запуска.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Создание образа запуска для предварительной iOS 8 приложений

Статическое изображение может использоваться в дополнение к .xib или экран запуска раскадровки, если вы приложение предназначено для версий до iOS 8. 

Это статическое изображение можно задать в файле Info.plist, или как каталог ресурсов (для iOS 7) в приложении. Необходимо будет предоставить отдельные образы для каждого устройства размер (320 x 480, 640 x 960, 640 x 1136), который может выполняться приложение. Дополнительные сведения о размерах экрана запуска [изображения при запуске экрана](~/ios/app-fundamentals/images-icons/launch-screens.md) руководства.

> [!IMPORTANT]
> Если приложение имеет не экрана запуска, можно заметить, что он не помещается полностью на экране. Если это так, следует включить, по крайней мере, 640 x 1136 образ с именем `Default-568@2x.png` в Info.plist. 



## <a name="summary"></a>Сводка

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В этой статье рассматриваются способы разработки приложений iOS программно в Visual Studio. Мы рассмотрели способы создания проекта из шаблона пустого проекта, разговор о способах создания и добавления корневого контроллера представления окна. Затем мы показали, как использовать элементы управления из UIKit для создания иерархии в контроллере представления для разработки на экран приложения. Далее мы рассмотрели, как осуществлять представления макета надлежащим образом в различные ориентации и мы узнали, как создать пользовательское представление путем создания подклассов `UIView`, а также способ загрузить представление в контроллере. Наконец, мы изучили способы добавления экран запуска приложения.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

В этой статье рассматриваются способы разработки приложений iOS программно в Visual Studio для Mac. Мы рассмотрели способы создания проекта из шаблона единое представление разговор о способах создания и добавления корневого контроллера представления окна. Затем мы показали, как использовать элементы управления из UIKit для создания иерархии в контроллере представления для разработки на экран приложения. Далее мы рассмотрели, как осуществлять представления макета надлежащим образом в различные ориентации и мы узнали, как создать пользовательское представление путем создания подклассов `UIView`, а также способ загрузить представление в контроллере. Наконец, мы изучили способы добавления экран запуска приложения.

-----




## <a name="related-links"></a>Связанные ссылки

- [SimpleLogin (пример)](https://developer.xamarin.com/samples/monotouch/SimpleLogin)
