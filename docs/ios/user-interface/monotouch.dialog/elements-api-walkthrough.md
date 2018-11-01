---
title: Создание приложения Xamarin.iOS с помощью API элементов
description: В этой статье основан на сведения, представленные во введении к статье MonoTouch диалоговое окно. Он предоставляет пошаговое руководство, которое показывает, как использовать MonoTouch.Dialog (главный целевой сервер. (Г) элементы API можно быстро приступить к сборке приложения с помощью машинного перевода. Г.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: a203975545c9f6e258505404d76e3fd5519a5a31
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674753"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Создание приложения Xamarin.iOS с помощью API элементов

_В этой статье основан на сведения, представленные во введении к статье MonoTouch диалоговое окно. Он предоставляет пошаговое руководство, которое показывает, как использовать MonoTouch.Dialog (главный целевой сервер. (Г) элементы API можно быстро приступить к сборке приложения с помощью машинного перевода. Г._

В этом пошаговом руководстве мы будем использовать главный целевой сервер. D элементы API для создания стиля «основной-подробности» приложения, который отображает список задач. Когда пользователь выбирает <span class="ui"> + </span> кнопки на панели навигации, новая строка добавляется в таблицу для задачи. Выбрав строку будет перейдите на экране сведений, который позволяет обновить описание задачи и срок, как показано ниже:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Выбрав строку перейдет на экран сведений, которое позволило бы обновить описание задачи и срок")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Настройка главного целевого сервера D

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D распространяется вместе с Xamarin.iOS. Чтобы использовать его, щелкните правой кнопкой мыши **ссылки** Xamarin.iOS узел проекта в Visual Studio 2017 или Visual Studio для Mac и добавьте ссылку на **MonoTouch.Dialog 1** сборки. Затем добавьте `using MonoTouch.Dialog` инструкций в источнике кода при необходимости.

## <a name="elements-api-walkthrough"></a>Пошаговое руководство элементы API

В [Общие сведения о диалоговом окне MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) статьи, мы получили основательного понимания различные части главного целевого сервера Г. Воспользуемся API элементов, чтобы собрать их все вместе в приложение.

## <a name="setting-up-the-multi-screen-application"></a>Настройка нескольких экранов приложения

Чтобы начать процесс создания экрана, создает контроллер представления MonoTouch.Dialog `DialogViewController`, а затем добавляет `RootElement`.

Чтобы создать приложение с несколькими экранами с MonoTouch.Dialog, нам нужно:

1.  Создание `UINavigationController.`
1.  Создание `DialogViewController.`
1.  Добавление `DialogViewController` как корень  `UINavigationController.` 
1.  Добавление `RootElement` для  `DialogViewController.`
1.  Добавить `Sections` и `Elements` для  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>С помощью UINavigationController

Для создания приложения в стиле навигации, нам нужно создать `UINavigationController`, а затем добавьте его как `RootViewController` в `FinishedLaunching` метод `AppDelegate`. Чтобы сделать `UINavigationController` работать с MonoTouch.Dialog, мы добавим `DialogViewController` для `UINavigationController` как показано ниже:

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
        _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        _rootElement = new RootElement ("To Do List"){new Section ()};

        // code to create screens with MT.D will go here …

        _rootVC = new DialogViewController (_rootElement);
        _nav = new UINavigationController (_rootVC);
        _window.RootViewController = _nav;
        _window.MakeKeyAndVisible ();
            
        return true;
}
```

Приведенный выше код создает экземпляр класса `RootElement` и передает его в `DialogViewController`. `DialogViewController` Всегда имеет `RootElement` в верхней части иерархии. В этом примере `RootElement` создается со строкой «To Do List, «который служит в качестве заголовка панели навигации контроллера навигации. На этом этапе выполнение приложения должны выводиться на экран, показанный ниже:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Запуск приложения будет представлять экран, показанный здесь")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Давайте посмотрим, как использовать MonoTouch.Dialog в иерархическую структуру `Sections` и `Elements` Чтобы добавить дополнительные экраны.

### <a name="creating-the-dialog-screens"></a>Создание экранов диалоговое окно

Объект `DialogViewController` является `UITableViewController` подкласс, использующего контроллер представления MonoTouch.Dialog для добавления экранов. Контроллер представления MonoTouch.Dialog создает экраны, добавляя `RootElement` для `DialogViewController`, как было показано выше. `RootElement` Может иметь `Section` экземпляры, представляющие части таблицы.
Разделы, состоящего из элементов, другие разделы, или даже другой `RootElements`. Путем вложения `RootElements`, MonoTouch.Dialog автоматически создает приложение навигации в стиле, как мы увидим Далее.

### <a name="using-dialogviewcontroller"></a>С помощью DialogViewController

`DialogViewController`, `UITableViewController` Имеет подкласс, `UITableView` как ее представление. В этом примере мы хотим добавить элементы в таблицу каждый раз <span class="ui"> + </span> нажатии кнопки. Так как `DialogViewController` был добавлен `UINavigationController`, мы можем использовать `NavigationItem` `RightBarButton` свойство, добавляемое <span class="ui"> + </span> кнопку, как показано ниже:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Когда мы создали `RootElement` ранее, мы передали ей один `Section` экземпляр таким образом, мы может добавлять элементы в виде <span class="ui"> + </span> нажатии кнопки пользователем. Для выполнения задачи в этом случае обработчик для кнопки можно использовать следующий код:

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

Этот код создает новый `Task` объекта при каждом нажатии кнопки. Ниже приведен простой реализации `Task` класса:

```csharp
public class Task
{   
        public Task ()
        {
        }
        
        public string Name { get; set; }
        
        public string Description { get; set; }

        public DateTime DueDate { get; set; }
}
```

Задачи `Name` свойство используется для создания `RootElement`его заголовок, а также переменной-счетчику `n` которое увеличивается для каждой новой задачи. Контроллер представления MonoTouch.Dialog включает элементы в строки, которые добавляются к `TableView` при каждом `taskElement` добавляется.

## <a name="presenting-and-managing-dialog-screens"></a>Представления и управление диалоговых экранов

Мы использовали `RootElement` , чтобы контроллер представления MonoTouch.Dialog будет автоматически создавать новый экран для сведения о каждой задаче и перейти к нему, если выбрана строка.

На экране сведений задач сама состоит из двух разделов; Каждый из этих разделов содержит один элемент. Первый элемент создается на основе `EntryElement` для предоставления к редактируемой строки для задачи `Description` свойство. При выборе элемента клавиатуры для редактирования текста представляется, как показано ниже:

 [![](elements-api-walkthrough-images/03-create-task.png "При выборе элемента клавиатуры для редактирования текста представляется, как показано")](elements-api-walkthrough-images/03-create-task.png#lightbox)

Второй раздел содержит `DateElement` , позволяет нам управлять задачи `DueDate` свойство. Выбор даты автоматически загружает элемент управления DatePicker, как показано:

 [![](elements-api-walkthrough-images/04-date-picker.png "Выбор даты автоматически загружает управляющий элемент выбора даты как")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

В обоих `EntryElement` и `DateElement` случаев (или для любого элемента ввода данных в контроллер представления MonoTouch.Dialog), любые изменения значения сохраняются автоматически. Мы покажем это, изменив дату и затем переходы назад и вперед между экрана корневой и различные сведения о задаче, в котором значения в экраны сведения сохраняются.

## <a name="summary"></a>Сводка

В этой статье представлено пошаговое руководство, которое было показано, как использовать API MonoTouch.Dialog элементов. Статья описывает основные шаги, чтобы создать приложение с несколькими экранами с главного целевого сервера D, включая способы использования `DialogViewController` и добавление элементов и разделы для создания экранов. Кроме того он показал, как использовать главного целевого сервера D в сочетании с `UINavigationController`.

## <a name="related-links"></a>Связанные ссылки

- [MTDWalkthrough (пример)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Презентация - Мигель de Icaza создает на экран входа iOS с MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Презентация - легко создавать пользовательские интерфейсы iOS с помощью MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Введение в контроллер представления MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Пошаговое руководство по API отражения](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Пошаговое руководство элемент JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Диалоговое окно MonoTouch на Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation приложения](https://github.com/migueldeicaza/TweetStation)
- [Ссылки на класс UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Ссылки на класс UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
