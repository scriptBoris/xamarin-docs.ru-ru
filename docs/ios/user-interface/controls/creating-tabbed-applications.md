---
title: Полосы вкладки и панели вкладки контроллеров в Xamarin.iOS
description: Этот документ описывает iOS вкладки панели контроллеров и способ их использования с Xamarin.iOS. Он демонстрирует Настройка UITabBarController, работы с изображениями, задайте значения эмблемы, работа с событиями и многое другое.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d8b096774e60ec0e0b69e109fa5da53c25e66d25
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789762"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Полосы вкладки и панели вкладки контроллеров в Xamarin.iOS

С вкладками приложения используются в iOS для поддержки пользовательских интерфейсов, где может быть доступно несколько экранов в произвольном порядке. Через `UITabBarController` класса приложения можно легко включить поддержку таких сценариев нескольких экранов. `UITabBarController` отвечает за управление нескольких экранов, позволяя разработчику приложения сосредоточить внимание на сведения о каждом экране.

Как правило, построение приложений с вкладками `UITabBarController` , `RootViewController` главного окна. Тем не менее с помощью небольшой дополнительный код с вкладками приложений также можно последовательно, чтобы некоторые другие начальный экран, например сценарии, в которой приложение сначала представляет экран входа, а затем интерфейс с вкладками.

Мы изучим, используя вкладки здесь при одновременной ликвидации пример простого приложения. Затем мы рассмотрим способы работы с вкладками в не - `RootViewController` сценария.

## <a name="introducing-uitabbarcontroller"></a>Знакомство с приложением UITabBarController

`UITabBarController` Поддерживает с вкладками разработки приложений с помощью следующего:

-  Разрешение нескольких контроллеров для добавления к нему.
-  Предоставление интерфейса пользователя с вкладками, через `UITabBar` класса, чтобы позволить пользователю переключаться между контроллерами и их представления. 


Контроллеры добавляются к `UITabBarController` через его `ViewControllers` свойство, которое является `UIViewController` массива. `UITabBarController` Сам обрабатывает загрузку правильную контроллера и представляющим его представление, в зависимости от выбранной вкладки.

Вкладки являются экземплярами `UITabBarItem` класс, который содержится в `UITabBar` экземпляра. Каждый `UITabBar` экземпляр доступен с помощью `TabBarItem` свойства контроллера на каждой из вкладок.

Чтобы получить представление о работе с `UITabBarController`, давайте рассмотрим построение простое приложение, которое использует один.

## <a name="tabbed-application-walkthrough"></a>Пошаговое руководство с вкладками приложения

В данном пошаговом руководстве мы собираемся создавать следующие приложения:

[![](creating-tabbed-applications-images/00-app.png "Пример приложения с вкладками")](creating-tabbed-applications-images/00-app.png#lightbox)

Несмотря на то, что уже существует шаблон со вкладками приложения доступны в Visual Studio для Mac, в этом примере, мы будем работать с пустой проект, чтобы получить лучшее представление о том, как создается приложение.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Создание приложения

Давайте начнем с создания нового приложения.

Выберите **файл > Создать > решения** пункта меню в Visual Studio для Mac и выберите **iOS > приложения > пустой проект** шаблона, имя проекта `TabbedApplication`, как показано ниже:

[![](creating-tabbed-applications-images/newsolution1.png "Выберите шаблон пустого проекта")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Назовите проект TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Добавление UITabBarController

Добавьте пустым классом, выбрав **файл > новый файл** и выбрав **общие: пустым классом** шаблона. Назовите файл `TabController` как показано ниже:

[![](creating-tabbed-applications-images/02-newclass.png "Добавьте класс TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController` Класс будет содержать реализацию `UITabBarController` , будет управлять массив `UIViewControllers`. При выборе вкладки, `UITabBarController` берет на себя представляющим представление для контроллера соответствующее представление.

Для реализации `UITabBarController` необходимо сделать следующее:

1.  Базовый класс задается `TabController` для `UITabBarController` . 
1.  Создание `UIViewController` экземпляров, чтобы добавить `TabController` . 
1.  Добавить `UIViewController` экземпляров в массив, назначенный `ViewControllers` свойство `TabController` . 


Добавьте следующий код в `TabController` класса для достижения следующие действия:

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

Обратите внимание, что для каждого `UIViewController` набор экземпляров, мы `Title` свойство `UIViewController`. При добавлении контроллера `UITabBarController`, `UITabBarController` будет считывать `Title` для каждого контроллера и их отображения на вкладке связанные метки, как показано ниже:

[![](creating-tabbed-applications-images/00-app.png "Образец приложения будут работать")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Установка TabController RootViewController

Порядок, контроллеров находятся на вкладках соответствует порядку, они добавляются в `ViewControllers` массива.

Для получения `UITabController` для загрузки в качестве первого экрана, нам нужно сделать его окна `RootViewController`, как показано в следующем коде для `AppDelegate`:

```csharp
[Register ("AppDelegate")]
        public partial class AppDelegate : UIApplicationDelegate
        {
                UIWindow window;
                TabController tabController;

                public override bool FinishedLaunching (UIApplication app, NSDictionary options)
                {
                        window = new UIWindow (UIScreen.MainScreen.Bounds);

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

Если мы запустим приложение теперь `UITabBarController` будет загружать с выбранной вкладкой «первый» по умолчанию. Выбор любого из других вкладок в связанному контроллеру представление результатов представленных `UITabBarController,` как показано ниже, где пользователь выбрал второй вкладки:

[![](creating-tabbed-applications-images/03-secondtab.png "Вторая вкладка показано")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Изменение TabBarItems

Теперь, когда у нас есть работающего вкладке приложений, изменим `TabBarItem` изменение изображения и текст, отображаемый, а также для добавления эмблемы на одной из вкладок.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>Задание элемента системы

Во-первых давайте задать первую вкладку, чтобы использовать элемент "система". В конструкторе `TabController`, удалите строку, которая задает контроллер `Title` для `tab1` экземпляра и замените его следующим кодом для установки контроллера `TabBarItem` свойство:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

При создании `UITabBarItem` с помощью `UITabBarSystemItem`, заголовок и образа предоставляется автоматически операций ввода-вывода, как показано на снимке экрана ниже отображение **Избранное** значок и заголовок на первой вкладке:

 ![](creating-tabbed-applications-images/04a-tabimage.png "Первая вкладка со значком типа «звезда»")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Настройка заголовка и изображения

Помимо использования элемент системы, заголовок и изображение `UITabBarItem` можно задать пользовательские значения. Например, измените код, который задает `TabBarItem` свойство с именем контроллера `tab2` следующим образом:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Приведенный выше код предполагает образ с именем `second.png` будет добавлен в корневую папку проекта в Visual Studio для Mac. Фактически мы добавили три изображения для проектов, чтобы охватить разрешениях для всех устройств, как показано ниже:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "Изображения, добавляемые в проект")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

Вкладку образ должен быть png 30 x 30 с прозрачностью для обычного разрешения 60 x 60 для высоким разрешением и 90 x 90 для iPhone 6 Plus разрешения. В приведенном коде нам требуется только для загрузки файла с именем `second.png` и iOS будет автоматически загружать высоким разрешением, один на устройствах с дисплеем retina. Можно прочитать подробнее об этом [работа с образами](~/ios/app-fundamentals/images-icons/index.md) руководства. По умолчанию панель элементов вкладки, серый значок с синей оттенок, при выборе.

**Примечание**

На рисунках выше также могут быть добавлены к **ресурсов** каталог, который является специальный каталог, содержимое которого будет автоматически копироваться в корневой каталог пакета приложения:

[![](creating-tabbed-applications-images/tabbedapplication8.png "Изображения как ресурсы")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Кроме того, когда мы устанавливаем `Title` свойство напрямую на `TabBarItem`, оно переопределит значение, заданное для `Title` на сам контроллер.

При запуске приложение, второй вкладке отображается наши пользовательский заголовок и изображения, как показано ниже:

[![](creating-tabbed-applications-images/05-customtab.png "Вторая вкладка с квадратный значок")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>Задание значения эмблемы

Вкладки можно также отобразить индикатор событий. Например добавьте следующую строку кода для задания на вкладке третий эмблемы:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

При выполнении этого приводит красный метку со строки «Hi» в левом верхнем углу вкладки, как показано ниже:

[![](creating-tabbed-applications-images/06-badge.png "Вторая вкладка с эмблемой Hi")](creating-tabbed-applications-images/06-badge.png#lightbox)

Эмблема на значке часто используется для отображения числа непрочитанных, указание новых элементов. Чтобы удалить эмблему, установите `BadgeValue` NULL, как показано ниже:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Вкладки в сценариях не RootViewController

В приведенном выше примере мы показали, как работать с `UITabBarController` при `RootViewController` окна. В этом примере мы рассмотрим, как использовать `UITabBarController` не `RootViewController` и показать, как он будет создан использование раскадровки.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Пример начального экрана

В этом сценарии начальный экран загружает из контроллера, который не `UITabBarController`. Когда пользователь взаимодействует с экрана нажатием кнопки, один и тот же контроллер представления будут загружены в `UITabBarController`, который затем представляются пользователю. На следующем рисунке показан поток приложения:

[![](creating-tabbed-applications-images/inital-screen-application.png "На этом снимке экрана показан поток приложения")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Давайте начнем нового приложения для этого примера. Опять же, мы будем использовать **iPhone > приложения > пустой проект (C#)** шаблона, назвав проекта `InitialScreenDemo`.


В этом примере мы потребуется раскадровки для хранения нашей Просмотр контроллеров. Чтобы добавить раскадровки:

- Щелкните правой кнопкой мыши имя проекта и выберите **Добавить > новый файл**.

- В открывшемся диалоговом окне новый файл, перейдите к **iOS > пустые iPhone раскадровки**.

Давайте назовем эту раскадровку новый **MainStoryboard** , как показано ниже: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Добавьте в проект файл MainStoryboard")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Существует несколько важные действия, обратите внимание, при добавлении раскадровки в файле ранее раскадровки рассматривается в [введение в раскадровки](~/ios/user-interface/storyboards/index.md) руководства. Эти особые значения приведены ниже.

 
1. Добавьте имя раскадровки для **главного интерфейса** раздел `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Это основной интерфейс присвоено MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. В вашей `App Delegate`, переопределите метод окно со следующим кодом:

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

Мы собираемся требуются три контроллера представления для этого примера. Один, с именем `ViewController1`, будет использоваться как нашей начальное представление-контроллер и первой позицией табуляции. Два других, с именем `ViewController2` и `ViewController3`, который будет использоваться во второй и третий вкладки соответственно.

Откройте конструктор, дважды щелкнув файл MainStoryboard.storyboard и перетащите три контроллера представления на поверхность разработки. Мы хотим каждого из этих контроллеров представления иметь свои собственные класс, соответствующий указанным выше, в этом случае в списке **удостоверение > класс**, введите его имя, как показано на снимке экрана ниже:

[![](creating-tabbed-applications-images/class-name.png "Задайте класс ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio для Mac автоматически создаст классы и конструктора необходимые файлы, это можно увидеть в области решения, как показано ниже:

[![](creating-tabbed-applications-images/solution-pad2.png "Автоматически сгенерированные файлы в проекте")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Создание пользовательского интерфейса

Далее мы создадим простой пользовательский интерфейс для каждого из представлений ViewController с помощью Xamarin iOS конструктора.

Мы хотим перетащите `Label` и `Button` на ViewController1 из **элементов** правой стороны. Далее мы будем использовать панель свойств изменение имени и текст для следующих элементов управления:

-  **Метка** : `Text`  =  **один**
-  **Кнопка** : `Title`  =  **пользователь принимает некоторые начального действия**


Мы управления видимостью кнопку в `TouchUpInside` событий и мы должны ссылаться на него в коде. Давайте идентифицировать его с **имя** `aButton` в панели свойств, как показано на следующем снимке экрана:

[![](creating-tabbed-applications-images/abutton-properties.png "Задайте имя aButton в панели свойств")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

В область конструктора теперь должен выглядеть примерно на снимке экрана ниже:

[![](creating-tabbed-applications-images/design-surface1.png "В область конструктора теперь должен выглядеть примерно на следующий снимок экрана")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Давайте добавим немного более подробно в `ViewController2` и `ViewController3`, путем добавления метки к каждому и изменения текста «Два» и «Три», соответственно. Это подчеркивает пользователя мы ищем какие вкладки или представления.

#### <a name="wiring-up-the-button"></a>Подключение кнопки

Мы собираемся загрузить `ViewController1` при первом запуске приложения. Когда пользователь нажимает кнопку, мы скрыть кнопку и загрузить `UITabBarController` с `ViewController1` экземпляра в первой позицией табуляции.

Если пользователь отпускает `aButton`, мы хотим TouchUpInside события. Позволяет выбрать и в **вкладку события** прокладки свойства объявить обработчик событий — `InitialActionCompleted` , поэтому его можно ссылаться в коде. Это показано на снимке экрана ниже:

[![](creating-tabbed-applications-images/event-handler.png "Когда пользователь отпускает aButton, активация события TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

После этого необходимо определить представление-контроллер для скрытия кнопки при возникновении события `InitialActionCompleted`. В `ViewController1`, добавьте следующий разделяемый метод:

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

Сохраните файл и запустите приложение. Мы должны увидеть отображается один экран и кнопка исчезают Touch вверх.

#### <a name="adding-the-tab-bar-controller"></a>Добавление панели вкладок контроллера

Теперь у нас есть нашей исходное представление ожидаемым образом. Далее, необходимо добавить его в `UITabBarController`, а также представления 2 и 3. Откроем раскадровки в конструкторе.

В **элементов**, поиск **вкладка панели контроллера** в списке контроллеров & объекты и переместите ее в область конструктора. Как видно на снимке экрана ниже контроллера панели вкладки — без интерфейса пользователя и поэтому переводит двух контроллеров представления с ней по умолчанию:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Добавление контроллера вкладки панели макета")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Удалите эти новые контроллеры представление, выбрав черная полоса внизу и нажав клавишу delete.

В нашем раскадровки можно использовать Segues для обработки переходов между TabBarController и наши Просмотр контроллеров. После взаимодействует с исходное представление, мы хотим загрузка TabBarController, представляемые пользователю. Позволяет настроить в конструкторе.

**Удерживая клавишу CTRL** и **перетащите** с помощью кнопки для TabBarController. На доступ к мыши появится контекстное меню. Мы хотим использовать модального segue. 
 
Для настройки каждой из наших вкладок **нажатой клавише CTRL** из TabBarController для каждой из наших Просмотр контроллеров в порядке от одного до трех, а затем выберите связь **вкладке** в контекстном меню, как показано ниже:

[![](creating-tabbed-applications-images/context-menu.png "Выберите вкладку связь")](creating-tabbed-applications-images/context-menu.png#lightbox)

Раскадровки должен быть похож на снимке экрана ниже.

[![](creating-tabbed-applications-images/segue-layout.png "Раскадровка должен быть похож на следующий снимок экрана")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Если щелкнуть один из элементов панели вкладки и просмотр панели «свойства», видно ряд различных параметров, как показано ниже:

[![](creating-tabbed-applications-images/properties-panel.png "Настройка параметров вкладки в обозревателе свойств")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Это можно использовать для редактирования определенные атрибуты, такие как эмблема, заголовок и iOS [идентификатор](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), среди прочего

Если сохранить и запустить приложение, мы найти, кнопки опять появляется, когда экземпляр ViewController1 загружается в TabBarController. Давайте исправить эту проблему, проверьте, имеет ли текущее представление родительского представления контроллера. Если Да, мы знаем, мы находятся внутри TabBarController, и поэтому должен быть скрыт кнопки. Давайте добавим приведенный ниже код в класс ViewController1:

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

Загружается после запуска приложения и пользователь нажимает кнопку на первом экране UITabBarController, с помощью представления на первом экране помещаются в первую вкладку, как показано ниже:

[![](creating-tabbed-applications-images/first-view.png "Образец выходных данных приложения")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Сводка

В этой статье рассматриваются способы использования `UITabBarController` в приложении. Мы прошли через загрузку контроллеров в каждой из вкладок, а также способы настройки свойств на вкладках такой заголовок, изображения и эмблемы. Мы затем проверяются, с помощью раскадровки, как загрузить `UITabBarController` во время выполнения, если она не `RootViewController` окна.


## <a name="related-links"></a>Связанные ссылки

- [Создание приложений с вкладками (пример)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.ZIP](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Ссылку на класс UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
