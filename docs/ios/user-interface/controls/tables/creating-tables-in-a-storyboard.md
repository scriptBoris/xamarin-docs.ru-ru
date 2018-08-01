---
title: Работа с таблицами в конструкторе iOS
description: В предыдущих разделах мы изучена разработка с использованием таблиц. В этом разделе пятый и последний будет статистической обработки, что мы узнали, пока и создать простое приложение список с помощью раскадровки.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 869719b1d3514fee4b45f61dcdb34a0bcf54f7b2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780727"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Работа с таблицами в конструкторе iOS

Раскадровки WYSIWYG позволяют создавать приложения iOS и поддерживается в Visual Studio на Mac и Windows. Дополнительные сведения о раскадровки ссылаться [введение для раскадровки](~/ios/user-interface/storyboards/index.md) документа. Раскадровки можно также редактировать ячейки макеты *в* в таблицу, которая упрощает разработку с помощью таблиц и ячеек

При настройке свойств представления таблицы в конструкторе iOS, существует два типа содержимого ячейки, можно выбрать: **динамическое** или **статических** прототип содержимого.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Прототип динамического содержимого

A `UITableView` с прототипом содержимого обычно предназначены для отображения списка данных, где прототип ячейки (или ячейки, как можно определить более одного) используются повторно для каждого элемента в списке. Ячейки не должны создаваться, они были получены в `GetView` метод путем вызова `DequeueReusableCell` метод его `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Статическое содержимое

`UITableView`s со статическим содержимым позволяет таблицы разрабатываться прямо в рабочей области конструирования. Ячейки можно перетащить в таблицу и настраивать путем изменения свойств и добавления элементов управления.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Создание раскадровки управляемого приложения

В примере StoryboardTable содержит простое приложение основной подробности, использует оба типа UITableView в раскадровку. В оставшейся части этого раздела описывается создание небольшого задача пример списка, в котором будет выглядеть следующим образом, после завершения:

 [![Пример экранов](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

Пользовательский интерфейс будет создано с помощью раскадровки, и оба экраны будет использовать UITableView. Главный экран использует *прототип содержимого* макета строки и подробности использует экрана *статическое содержимое* для создания формы ввода данных с помощью пользовательских ячеек разметки.

## <a name="walkthrough"></a>Пошаговое руководство

Создайте новое решение в Visual Studio с помощью **(Create) создать проект... > единого представления App(C#)** и назовите его _StoryboardTables_.

 [![Создать диалоговое окно нового проекта](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Будет открыто решение с некоторыми файлами C# и `Main.storyboard` файл уже создан. Дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструкторе iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Изменение раскадровки

Раскадровка будет изменять в три этапа:

-  Во-первых, макет, необходимая Просмотр контроллеров и задавать их свойства.
-  Во-вторых Создание пользовательского интерфейса с помощью перетаскивания объектов в представлении
-  Наконец добавьте требуемый класс UIKit в каждое представление и присвойте имя различные элементы управления, поэтому они могут ссылаться в коде.


После завершения раскадровку код можно добавлять вносить все работает.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Просмотр контроллеров макета

Удаление существующего подробное представление о первом изменении в раскадровку, которое его замена UITableViewController. Выполните следующие действия.

1.  Выберите в строке в нижней части представления контроллера и удалите его.
2.  Перетащите **навигации контроллера** и **таблицы View Controller** на раскадровку из области элементов. 
3.  Создайте segue из представления корневого второй контроллер представление таблицы, который был только что добавлен. Для создания segue управления + перетащите *из ячеек с подробными* для вновь добавленного UITableViewController. Выберите параметр **Показать*** под **перейти выбора**. 
4.  Выберите новый перейти был создан и назначение ему идентификатора для ссылки, это перейти в коде. Нажмите segue и введите `TaskSegue` для **идентификатор** в **панель свойств**, следующим образом:    
  [![Именование перейти в панель свойств](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Настройте два представления таблицы, выбрав их и использование панели свойств. Убедитесь в том выбрать представления и не представление-контроллер — структуру документа можно использовать для выбора.

6.  Изменение контроллера представления корневого быть **содержимого: динамические прототипы** (будут помечаться как представление в области конструктора **прототип содержимого** ):

    [![Свойство содержимого динамических прототипов](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Измените новый **UITableViewController** быть **содержимого: статические ячейки**. 


8. Новый UITableViewController должен иметь класс имени и идентификатора. Выберите представление контроллер и тип _TaskDetailViewController_ для **класса** в **панель свойств** — это создаст новый `TaskDetailViewController.cs` файл в решении Панель. Введите **StoryboardID** как _сведений_, как показано в следующем примере. Это будет использоваться позже для загрузки этого представления в коде C#:  

    [![Задание идентификатора раскадровки](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. Область конструктора раскадровки теперь должна выглядеть следующим образом (заголовок элемента контроллера представления корневого навигации был изменен на «При работе доски»):

    [![Область конструктора](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Создание пользовательского интерфейса

Теперь, представления и segues будут настроены, элементы пользовательского интерфейса необходимо добавить.

#### <a name="root-view-controller"></a>Корневой контроллер представления

Во-первых, выделите ячейку прототип в контроллере главного представления и задать **идентификатор** как _taskcell_, как показано ниже. Это будет использоваться далее в коде для извлечения экземпляра этого UITableViewCell:

 [![Задание идентификатора ячейки](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Далее необходимо создать кнопку, которая будет добавлять новые задачи, как показано ниже:

[![панель элемент кнопки на панели навигации](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Выполните следующие действия: 

-  Перетащите **элемент кнопки панели** из области элементов на _правой части панели навигации_.
-  В **панель свойств**в разделе **элемент кнопки панели** выберите **идентификатор: добавление** (чтобы сделать его *+* плюс). 
-  Присвойте ему имя, чтобы можно было идентифицировать в коде в дальнейшем. Обратите внимание, необходимо предоставить контроллеру представление корневого имени класса (например **ItemViewController**) позволяет задать имя элемента кнопки строки.


#### <a name="taskdetail-view-controller"></a>Представление TaskDetail контроллер

Подробное представление требует намного больше усилий. Вид ячеек таблицы должны перетаскивать представление и затем заполняется метки, текст представления и кнопки. На следующем снимке экрана показано завершения пользовательского интерфейса с двумя разделами. Один раздел имеет три ячейки, три подписи, два текстовых поля и одно переключения, а во втором разделе имеет одну ячейку с две кнопки:

 [![Подробное представление макета](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Для построения макета завершения, необходимо:

Выберите представление таблицы и откройте **Pad свойство**. Обновите следующие свойства:

-  **Разделы**: _2_ 
-  **Стиль**: _сгруппированные_
-  **Разделитель**: _None_
-  **Выбор**: _не выбрано_

Выберите верхний раздел и в разделе **свойства > раздел представление таблицы** изменить **строк** для _3_, как показано ниже:


 [![Установите значение в верхнем разделе три строки](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Для каждой ячейки откройте **панель свойств** и задать:

-  **Стиль**: _Custom_
-  **Идентификатор**: выберите уникальный идентификатор для каждой ячейки (например) «_заголовок_«,»_заметки_«,»_сделать_»).
-  Перетащите элементы управления, необходимые для создания макета, показанные на снимке экрана (поместите **UILabel**, **UITextField** и **UISwitch** правильное количество ячеек и задайте метки соответствующим образом т. е. Название, примечания и выполнена).


Во второй части задайте **строк** для _1_ и щелкните нижний маркер изменения размера ячейки, чтобы сделать его высота.

-  **Задать идентификатор**: уникальное значение (например) “save”). 
-  **Задать фон**: _Очистка цветов_ .
-  Перетащите две кнопки в ячейку и правильно настроить названиях (т. е. _Сохранить_ и _удалить_), как показано ниже:

   [![Установка двух кнопок в нижнем разделе](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

На этом этапе также можно задать ограничения для ячеек и элементов управления для обеспечения адаптивной макета.

### <a name="adding-uikit-class-and-naming-controls"></a>Добавление класса UIKit и именования элементов управления

Существует несколько заключительные действия для создания нашей раскадровки. Сначала мы необходимо предоставить каждому нашей управления имя в разделе **удостоверение > имя** , они могут использоваться в коде ниже на. Назовите их следующим образом:

-  **Заголовок UITextField** : _TitleText_
-  **Заметки о UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Удалить UIButton** : _DeleteButton_
-  **Сохранить UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Добавление кода

Остальная часть работы, выполняются в Visual Studio на Mac и Windows с помощью C#. Обратите внимание, что имена свойств, используемые в коде соответствуют были заданы в примере выше.

Сначала нужно создать `Chores` класс, который предоставляет способ получения и задания значения ID, имя, заметки и сделать логическое значение, чтобы мы могли использовать эти значения в приложении.

В вашей `Chores` класса добавьте следующий код:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Создайте `RootTableSource` класс, наследующий от `UITableViewSource`. 

Различие между этим и представление таблицы без раскадровки состоит в том `GetView` метода не нужно создать экземпляр любой ячейки — `theDequeueReusableCell` метод всегда будет возвращать экземпляр ячейки прототип (с соответствующим идентификатором).

Приведенный ниже код взят из `RootTableSource.cs` файла:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Для использования `RootTableSource` класса, создайте новую коллекцию в `ItemViewController`его конструктор:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

В `ViewWillAppear` коллекция передается в источник и назначение представление таблицы:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

При запуске приложения теперь главный экран будет загружать и отображать список из двух задач. При затронутых задачи segue, определяемый раскадровку вызовет экран сведений для отображения, но он не отображаются никакие данные в данный момент.

Для «параметр» в отправить segue, переопределите `PrepareForSegue` метод и задать свойства для `DestinationViewController` ( `TaskDetailViewController` в этом примере). Класс контроллера представления назначения будет создан, но не еще отображается для пользователя — это означает, можно задать свойства класса, но не могут изменять любые элементы управления пользовательского интерфейса:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

В `TaskDetailViewController` `SetTask` метод назначает его параметры свойств, можно ссылаться в ViewWillAppear. Свойства элемента управления не может быть изменен в `SetTask` так как не существует при `PrepareForSegue` вызывается:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Segue будет открыть экран сведений и содержит сведения о выбранной задачи. К сожалению, отсутствует реализация для **Сохранить** и **удалить** кнопки. Перед реализацией кнопок, добавьте эти методы для **ItemViewController.cs** обновление базовых данных и закрыть экран сведений:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

Далее необходимо добавить кнопку `TouchUpInside` обработчик событий `ViewDidLoad` метод **TaskDetailViewController.cs**. `Delegate` Ссылку на свойство для `ItemViewController` был создан, в частности, можно вызвать `SaveTask` и `DeleteTask`, который закройте это представление в ходе своей работы:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

Последний элемент оставшиеся функциональность для построения состоит в создании новых задач. В **ItemViewController.cs** добавить метод, который создает новые задачи, а также откроется подробное представление. Для создания представления с использованием раскадровки `InstantiateViewController` метод с `Identifier` для этого представления — в этом примере, который будет «Подробности»:

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Наконец, подключите кнопки на панели навигации в **ItemViewController.cs** `ViewDidLoad` метод будет вызывать его:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

На этом процесс пример раскадровки — готовое приложение выглядит так:

[![Готовое приложение](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

В примере показано:

-  Создание таблицы с прототипом содержимого, где, определенных для повторного использования для отображения списков данных. 
-  Создание таблицы со статического содержимого для построения форму ввода. Включить это изменение стиля таблицы и добавление разделов, ячеек и элементов управления пользовательского интерфейса. 
-  Создание segue и переопределить `PrepareForSegue` метод для уведомления целевое представление всех параметров, требуется. 
-  Загрузка представления раскадровки непосредственно с `Storyboard.InstantiateViewController` метод.



## <a name="related-links"></a>Связанные ссылки

- [StoryboardTable (пример)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
