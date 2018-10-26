---
title: Использование JSON для создания пользовательского интерфейса в Xamarin.iOS
description: Контроллер представления MonoTouch.Dialog (машинного перевода. (Г) включает поддержку для динамического создания пользовательского интерфейса с помощью данных JSON. В этом руководстве мы рассмотрим способы использования JSONElement для создания пользовательского интерфейса из JSON, в состав приложения, или загруженные из удаленного URL-адрес.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: 4c04edc5dd66917dbf83385ab88fd5f01e232c2d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105198"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Использование JSON для создания пользовательского интерфейса в Xamarin.iOS

_Контроллер представления MonoTouch.Dialog (машинного перевода. (Г) включает поддержку для динамического создания пользовательского интерфейса с помощью данных JSON. В этом руководстве мы рассмотрим способы использования JSONElement для создания пользовательского интерфейса из JSON, в состав приложения, или загруженные из удаленного URL-адрес._

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D поддерживает создание пользовательских интерфейсов, объявленных в JSON. Если элементы объявлены с помощью JSON, главного целевого сервера D создаст связанных элементов для вас автоматически. JSON можно загрузить из локального файла, проанализированный `JsonObject` экземпляр или даже удаленного URL-адрес.

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D поддерживает полный спектр возможностей, доступных в API элементов при использовании JSON. Например на следующем снимке экрана приложение полностью объявляется с помощью JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Например, приложение на этом снимке экрана полностью объявляется с помощью JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [![](json-element-walkthrough-images/01-load-from-file.png "для этого приложения на этом снимке экрана полностью объявляется с помощью JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Давайте вернемся к примеру из [Пошаговое руководство по API элементы](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) руководстве описано, как добавлять задачи экран сведений с помощью JSON.

## <a name="setting-up-mtd"></a>Настройка главного целевого сервера D

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D распространяется вместе с Xamarin.iOS. Чтобы использовать его, щелкните правой кнопкой мыши **ссылки** Xamarin.iOS узел проекта в Visual Studio 2017 или Visual Studio для Mac и добавьте ссылку на **MonoTouch.Dialog 1** сборки. Затем добавьте `using MonoTouch.Dialog` инструкций в источнике кода при необходимости.

## <a name="json-walkthrough"></a>Пошаговое руководство JSON

Пример в этом пошаговом руководстве, позволяет задачи должен быть создан. При выборе задачи на первом экране представляется экран сведений, как показано:

 [![](json-element-walkthrough-images/03-task-list.png "При выборе задачи на первом экране представляется экран сведений, как показано")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Создание JSON

В этом примере мы загрузим JSON из файла в проект с именем `task.json`. ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D ожидает, что JSON-ФАЙЛ в соответствии с синтаксисом, который отражает API элементов. Так же, как с помощью API элементов из кода, при использовании JSON, мы объявляем разделах и в соответствующих разделах мы добавим элементы. Для объявления, разделы и элементы в формате JSON, мы используем строки «разделами» и «элементы» соответственно как ключи. Для каждого элемента, тип связанного элемента задается с помощью `type` ключ. Каждый других элементов свойству с именем свойства как ключ.

Например следующий код JSON описывает разделы и элементы для сведения о задаче:

```csharp
{
    "title": "Task",
    "sections": [
        {
          "elements" : [
            {
                "id" : "task-description",
                "type": "entry",
                "placeholder": "Enter task description"
            },
            {
                "id" : "task-duedate",
                "type": "date",
                "caption": "Due Date",
                "value": "00:00"
            }
         ]
        }
    ]
  }
```

Обратите внимание, что выше JSON-ФАЙЛ включает в себя идентификатор для каждого элемента. Любой элемент может включать код, чтобы сослаться на него во время выполнения. Мы увидим, как он используется в данный момент, когда мы покажем, как загрузить JSON в коде.

## <a name="loading-the-json-in-code"></a>Загрузка JSON в коде

После определения JSON, нам нужно загрузить их в главного целевого сервера С помощью D `JsonElement` класса. При условии, что файл с JSON, созданных ранее был добавлен в проект с именем sample.json и заданным действием сборки содержимого, загрузка `JsonElement` осуществляется простым вызовом следующую строку кода:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Так как мы добавляем по запросу каждый раз, создается задача, мы можем изменить кнопки, нажатой из примера выше элементы API, следующим образом:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Доступ к элементам во время выполнения

Помните, мы добавили код для обоих элементов при их объявлении в файле JSON. Свойство id можно использовать для доступа к каждому элементу во время выполнения можно изменить их свойства в коде. Например следующий код ссылается на элементы даты и запись для установки значений из объекта задачи:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        taskElement.Caption = task.Name;

        var description = taskElement ["task-description"] as EntryElement;

        if (description != null) {
                description.Caption = task.Name;
                description.Value = task.Description;       
        }

        var duedate = taskElement ["task-duedate"] as DateElement;

        if (duedate != null) {                
                duedate.DateValue = task.DueDate;
        }
        _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>Загрузка JSON из URL-адреса

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D также поддерживает динамическое JSON из внешнего URL-адреса, просто передав URL-адрес в конструктор класса `JsonElement`. ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D будет расширяться иерархии, объявленные в JSON по запросу, как переход между экранами. Например рассмотрим файл JSON, как показано ниже, расположенный в корневом локальном веб-сервера:

```csharp
{
    "type": "root",
    "title": "home",
    "sections": [
       {
         "header": "Nested view!",
         "elements": [
           {
             "type": "boolean",
             "caption": "Just a boolean",
             "id": "first-boolean",
             "value": false
           },
           {
             "type": "string",
             "caption": "Welcome to the nested controller"
           }
         ]
       }
     ]
}
```

Мы можем загрузить это с помощью `JsonElement` как в следующем коде:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

Во время выполнения файл будет извлекаются и анализируются с главного целевого сервера "D", когда пользователь переходит на второй вид, как показано на следующем снимке экрана:

 [![](json-element-walkthrough-images/04-json-web-example.png "Файл будет иметь извлекаются и анализируются с главного целевого сервера D, когда пользователь переходит на второй вид")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Сводка

В этой статье было показано, как создать с помощью интерфейса с помощью машинного перевода. D из JSON. Он показал, как загрузить JSON, включены в файл с приложением, а также из URL-адрес удаленного. Он также показал, как получить доступ к элементам, описанных в формате JSON во время выполнения.

## <a name="related-links"></a>Связанные ссылки

- [MTDJsonDemo (пример)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Презентация - Мигель de Icaza создает на экран входа iOS с MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Презентация - легко создавать пользовательские интерфейсы iOS с помощью MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Введение в контроллер представления MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Пошаговое руководство по API элементов](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Пошаговое руководство по API отражения](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Диалоговое окно MonoTouch на Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation приложения](https://github.com/migueldeicaza/TweetStation)
- [Ссылки на класс UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Ссылки на класс UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
