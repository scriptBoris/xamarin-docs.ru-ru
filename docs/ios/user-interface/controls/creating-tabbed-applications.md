---
title: Панели вкладки панели вкладок и контроллеры в Xamarin.iOS
description: В этом документе описываются iOS вкладку панели контроллеров и способы их использования с Xamarin.iOS. Показывается, как настроить UITabBarController, работать с изображениями, задайте значения эмблемы, работа с событиями и многое другое.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6f5af4cea0c223b9ddc7cd72331676f5f92f2509
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055203"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Панели вкладки панели вкладок и контроллеры в Xamarin.iOS

В iOS используются с вкладками приложения для поддержки пользовательских интерфейсов, где может быть доступно несколько экранов в произвольном порядке. Через `UITabBarController` класс, приложения могут легко включать поддержку таких сценариев с несколькими экранами. `UITabBarController` берет на себя управление несколькими экранами, позволяя разработчику сосредоточиться на деталях каждого экрана приложения.

Как правило, построение приложений с вкладками `UITabBarController` , `RootViewController` главного окна. Тем не менее применив немного дополнительного кода, с вкладками приложения также можно последовательно, чтобы некоторые другие начальный экран, подобная ситуация, где приложение сначала появится экран входа, следуют интерфейс с вкладками.

Мы рассмотрим, используя вкладки здесь при работе с простого приложения Пошаговое руководство. Затем мы рассмотрим способы работы с вкладками в отличном `RootViewController` сценария.

## <a name="introducing-uitabbarcontroller"></a>Знакомство с UITabBarController

`UITabBarController` Поддерживает с вкладками разработки приложений, следующие:

-  Разрешение нескольких контроллеров для добавления к нему.
-  Предоставление с вкладками пользовательского интерфейса, с помощью `UITabBar` класса, чтобы разрешить пользователю переключаться между контроллерами и их представления. 


Контроллеры добавляются к `UITabBarController` через его `ViewControllers` свойство, являющееся `UIViewController` массива. `UITabBarController` Сам обрабатывает загрузке правильной контроллера и представляющим его представление, в зависимости от выбранной вкладки.

Вкладки являются экземплярами `UITabBarItem` класс, который содержатся в `UITabBar` экземпляра. Каждый `UITabBar` экземпляра можно получить с помощью `TabBarItem` свойства контроллера на каждой вкладке.

Чтобы понять, как работать с `UITabBarController`, давайте подробнее рассмотрим действия по созданию простого приложения, которое использует один.

## <a name="tabbed-application-walkthrough"></a>Пошаговое руководство для приложения с вкладками

В этом пошаговом руководстве мы создадим следующие приложения:

[![](creating-tabbed-applications-images/00-app.png "Пример приложения с вкладками")](creating-tabbed-applications-images/00-app.png#lightbox)

Несмотря на то, что уже существует шаблон со вкладками приложения доступны в Visual Studio для Mac, в этом примере, мы будем работать с пустой проект для получения лучшего понимания того, как создается приложение.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Создание приложения

Начнем с создания нового приложения.

Выберите **файл > Создать > решение** пункта меню в Visual Studio для Mac и выберите **iOS > приложение > пустой проект** шаблон, имя проекта `TabbedApplication`, как показано ниже:

[![](creating-tabbed-applications-images/newsolution1.png "Выберите шаблон пустого проекта")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Назовите проект TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Добавление UITabBarController

Добавьте пустой класс, выбрав **файл > новый файл** и выбрав **общие: пустой класс** шаблона. Назовите файл `TabController` как показано ниже:

[![](creating-tabbed-applications-images/02-newclass.png "Добавьте класс TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController` Класс будет содержать реализацию `UITabBarController` , будут управлять массив `UIViewControllers`. Когда пользователь выбирает вкладку, `UITabBarController` позаботится о представление, соответствующее представление контроллера представления.

Для реализации `UITabBarController` нам нужно сделать следующее:

1.  Задать базовый класс `TabController` для `UITabBarController` . 
1.  Создание `UIViewController` экземпляры, чтобы добавить `TabController` . 
1.  Добавить `UIViewController` экземпляров в массив, назначенный `ViewControllers` свойство `TabController` . 


Добавьте следующий код, чтобы `TabController` класс для достижения следующие действия:

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

Обратите внимание, что для каждого `UIViewController` набор экземпляров, мы `Title` свойство `UIViewController`. При добавлении контроллера `UITabBarController`, `UITabBarController` будет считывать `Title` для каждого контроллера и отобразить ее на метку соответствующей вкладки, как показано ниже:

[![](creating-tabbed-applications-images/00-app.png "Запуск примера приложения")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Установка TabController RootViewController

Порядок, контроллеров находятся на вкладках соответствует порядку, они добавляются `ViewControllers` массива.

Чтобы получить `UITabController` для загрузки в качестве первого экрана, нам нужно сделать его окна `RootViewController`, как показано в следующем коде для `AppDelegate`:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;
    
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        
        tabController = new TabController ();
        window.RootViewController = tabController;
        
        window.MakeKeyAndVisible ();
        
        return true;
    }
}
```

Если запустить приложение сейчас, `UITabBarController` будет загружаться с выбранной вкладкой «первый» по умолчанию. При выборе любой из других вкладок в связанного контроллера представление результатов представленных `UITabBarController,` как показано ниже, где конечный пользователь выбрал второй вкладке:

[![](creating-tabbed-applications-images/03-secondtab.png "Вторая вкладка показано")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Изменение TabBarItems

Теперь, когда у нас есть промежуточный вкладке приложения, давайте изменим `TabBarItem` изменение изображения и текст, отображаемый, а также чтобы добавить эмблему на одной из вкладок.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>Задание элемента системы

Во-первых давайте установим первую вкладку, чтобы использовать элемент "система". В конструкторе класса `TabController`, удалите строку, которая задает контроллера `Title` для `tab1` экземпляра и замените его следующим кодом, чтобы задать контроллер `TabBarItem` свойство:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

При создании `UITabBarItem` с помощью `UITabBarSystemItem`, заголовок и изображения предоставляются автоматически, iOS, как показано на снимке экрана ниже отображение **"Избранное"** значок и заголовок на первой вкладке:

 ![](creating-tabbed-applications-images/04a-tabimage.png "Первая вкладка со значком типа \"звезда\"")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Настройка заголовка и изображения

Помимо использования элемент "система", заголовок и изображение `UITabBarItem` можно задать пользовательские значения. Например, измените код, который задает `TabBarItem` свойство с именем контроллера `tab2` следующим образом:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Приведенный выше код предполагается, что образ с именем `second.png` был добавлен в корень проекта в Visual Studio для Mac. Фактически мы добавили три изображения в свой проект, чтобы охватить все разрешения устройства, как показано ниже:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "Образы, добавленные в проект")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

Вкладка образ должен быть png 30 x 30 с прозрачностью для обычного разрешения, 60 x 60 для высокого разрешения и 90 x 90 для iPhone 6 Plus разрешения. В коде, нам нужно только загрузить файл с именем `second.png` и iOS автоматически загрузит высокого разрешения, один на устройствах с экраном Retina. Дополнительные сведения см. в [работа с образами](~/ios/app-fundamentals/images-icons/index.md) руководства. По умолчанию элементов этой панели, серый значок с синей оттенок, при выборе.

**Примечание**

На рисунках выше, также могут быть добавлены к **ресурсы** каталог, который представляет собой специальный каталог, содержимое которого будет автоматически копироваться в корневой каталог пакета приложения:

[![](creating-tabbed-applications-images/tabbedapplication8.png "Изображения как ресурсы")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Кроме того, когда мы устанавливаем `Title` свойство непосредственно на `TabBarItem`, она переопределит любое значение, заданное для `Title` на сам контроллер.

При запуске приложение сейчас, вторая вкладка отображается наш пользовательский заголовок и изображения, как показано ниже:

[![](creating-tabbed-applications-images/05-customtab.png "Второй закладки, используя квадратный значок")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>Установка значения эмблемы

Вкладки также можно отобразить индикатор событий. Например добавьте следующую строку кода, чтобы задать эмблему на третью вкладку:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Под управлением, это приведет к красная метка со строки «Hi» в верхнем левом углу вкладки, как показано ниже:

[![](creating-tabbed-applications-images/06-badge.png "Вторую вкладку с Hi эмблему")](creating-tabbed-applications-images/06-badge.png#lightbox)

Эмблема часто используется для отображения чисел значение, указывающее непрочитанные, новые элементы. Чтобы удалить значок, задайте `BadgeValue` как null, как показано ниже:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Вкладки в сценариях не RootViewController

В приведенном выше примере мы показали, как работать с `UITabBarController` где `RootViewController` окна. В этом примере мы рассмотрим, как использовать `UITabBarController` не `RootViewController` и показать, как это создается при помощи объектов Storyboard.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Пример начального экрана

В этом сценарии загружает начального экрана из контроллера, который не `UITabBarController`. Когда пользователь взаимодействует с экрана, коснувшись кнопки, один и тот же контроллер представления будут загружены в `UITabBarController`, который затем представляются пользователю. На следующем рисунке показан блок-схеме приложения:

[![](creating-tabbed-applications-images/inital-screen-application.png "На этом снимке экрана показан поток в приложении")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Начнем с нового приложения в этом примере. Опять же, мы будем использовать **iPhone > приложение > пустой проект (C#)** шаблона, назвав проекта `InitialScreenDemo`.


В этом примере нам потребуется раскадровки для хранения наших контроллеров представлений. Чтобы добавить раскадровку:

- Щелкните правой кнопкой мыши имя проекта и выберите **Добавить > новый файл**.

- В открывшемся диалоговом окне создания файла, перейдите к **iOS > Пустая раскадровка iPhone**.

Давайте назовем этот новая раскадровка **MainStoryboard** , как показано ниже: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Добавьте в проект файл MainStoryboard")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Существует несколько важных шагов, чтобы Обратите внимание, при добавлении раскадровки в файл ранее раскадровки, как описано в [введение в раскадровки](~/ios/user-interface/storyboards/index.md) руководства. Эти особые значения приведены ниже.

 
1. Добавьте свое имя раскадровки, чтобы **главный интерфейс** раздел `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Значение основной интерфейс MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. В вашей `App Delegate`, переопределить метод окне следующим кодом:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Мы собираемся требуется три контроллера представления для этого примера. Один, с именем `ViewController1`, будет использоваться как наш начальным контроллером представления и на первой вкладке. Другие два, с именем `ViewController2` и `ViewController3`, который будет использоваться на второй и третий вкладках соответственно.

Откройте конструктор, дважды щелкнув файл MainStoryboard.storyboard и перетащите три контроллера представления в область конструктора. Мы хотим, каждый из этих контроллеров представлений для своего класса, соответствующий имени выше, в этом случае в разделе **удостоверений > класс**, введите его имя, как показано на следующем снимке экрана:

[![](creating-tabbed-applications-images/class-name.png "Задайте класс ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio для Mac автоматически создает классы и необходимые файлы конструктора, это можно увидеть на панели решения, как показано ниже:

[![](creating-tabbed-applications-images/solution-pad2.png "Автоматически сгенерированные файлы в проекте")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Создание пользовательского интерфейса

Далее мы создадим простой пользовательский интерфейс для каждого из представлений ViewController использовании конструктора iOS Xamarin.

Мы хотим перетащите `Label` и `Button` на ViewController1 из **элементов** правой стороны. Далее при помощи панели свойств для редактирования, именем и текстом элементов управления следующего:

-  **Метка** : `Text`  =  **один**
-  **Кнопка** : `Title`  =  **пользователь выполняет некоторое Начальное действие**


Мы Управление видимостью нашей кнопки в `TouchUpInside` событий и мы должны ссылаться на него в коде программной части. Давайте идентифицировать его с **имя** `aButton` в панели свойств, как показано на следующем снимке экрана:

[![](creating-tabbed-applications-images/abutton-properties.png "Задайте имя aButton в панели свойств")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

Поверхность конструктора теперь должен выглядеть как на снимке экрана ниже:

[![](creating-tabbed-applications-images/design-surface1.png "Поверхность конструктора теперь должен выглядеть примерно")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Давайте добавим немного более подробно в `ViewController2` и `ViewController3`, путем добавления метки к каждому и замену текста «Два» и «Три» соответственно. Это подчеркивает пользователь какие вкладки или представления, мы рассматриваем.

#### <a name="wiring-up-the-button"></a>Подключив кнопки

Мы собираемся загрузить `ViewController1` при первом запуске приложения. Когда пользователь нажимает кнопку, мы скрытия кнопки и загрузить `UITabBarController` с `ViewController1` экземпляра на первой вкладке.

Если пользователь отпускает `aButton`, мы хотим TouchUpInside события. Давайте выберем кнопки и в **вкладке "события"** панели свойств Объявите обработчик событий — `InitialActionCompleted` — поэтому его можно ссылаться в коде. Это показано на следующем снимке экрана:

[![](creating-tabbed-applications-images/event-handler.png "Когда пользователь отпускает aButton, активация события TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

Теперь нам нужно сообщить контроллер представления, чтобы скрыть кнопки при возникновении события `InitialActionCompleted`. В `ViewController1`, добавьте следующий разделяемый метод:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Сохраните файл и запустить приложение. Мы должны увидеть отображается один экран и кнопку решаются при Touch вверх.

#### <a name="adding-the-tab-bar-controller"></a>Добавление панели вкладки контроллера

Теперь у нас есть нашего начального представления, работает должным образом. Далее, необходимо добавить его в `UITabBarController`, а также представления 2 и 3. Давайте откройте раскадровку, в конструкторе.

В **элементов**, поиск **контроллер панели вкладок** под контроллеры & объекты и перетащите в область конструктора. Как показано на следующем снимке экрана, контроллер панели вкладок является без интерфейса пользователя и таким образом, дает двумя контроллерами представлений с ним по умолчанию:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Добавление контроллер панели вкладок в макет")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Удалите эти новые контроллеры представления, выбрав черную полосу в нижней и нажав клавишу delete.

В нашем раскадровке мы используем Segues обрабатывать переходы между TabBarController и наши контроллеров представлений. После взаимодействия с исходное представление, мы хотим загрузить их в TabBarController, представленные пользователю. Давайте настроим это в конструкторе.

**CTRL + щелчок** и **перетащите** с помощью кнопки для TabBarController. На доступ к мыши появится контекстное меню. Мы хотим использовать модального перехода. 
 
Для настройки каждой из наших вкладок **Ctrl + щелчок** из TabBarController для каждого из наших контроллеров представлений в порядке от одного до трех, а затем выберите связь **вкладке** в контекстном меню, как показано ниже:

[![](creating-tabbed-applications-images/context-menu.png "Выберите вкладку связь")](creating-tabbed-applications-images/context-menu.png#lightbox)

Раскадровка должна выглядеть так на снимке экрана ниже:

[![](creating-tabbed-applications-images/segue-layout.png "Раскадровка должна напоминать на этом снимке экрана")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Если мы щелкните один из элементов этой панели и изучите панели «свойства», вы увидите несколько различных вариантов, как показано ниже:

[![](creating-tabbed-applications-images/properties-panel.png "Настройка параметров вкладки в обозревателе свойств")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Мы можем использовать его для изменения определенных атрибутов, такие как эмблема, заголовок и iOS [идентификатор](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), среди прочего

Если сохранить и запустить приложение сейчас, очень скоро найдется что кнопки появится снова при загрузке экземпляра ViewController1 в TabBarController. Исправим это путем проверки на наличие родительского контроллера представления для текущего представления. Если Да, мы знаем, мы внутри TabBarController, и поэтому должны быть скрыты кнопки. Давайте добавим приведенный ниже код к классу ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Загружается после запуска приложения и пользователь нажимает кнопку на первом экране UITabBarController, с помощью представления на первом экране помещены на первой вкладке, как показано ниже:

[![](creating-tabbed-applications-images/first-view.png "Образец выходных данных приложения")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Сводка

В этой статье описаны способы использования `UITabBarController` в приложении. Мы рассмотрели способ загрузки контроллеров в каждой вкладке, а также настройке свойств на вкладках такие названия, изображения и эмблемы. Мы затем проверяются, с помощью раскадровки, как загрузить `UITabBarController` во время выполнения, если она не `RootViewController` окна.


## <a name="related-links"></a>Связанные ссылки

- [Создание приложения с вкладками (пример)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.ZIP](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Ссылки на класс UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
