---
title: Работа с таблицами в конструкторе iOS
description: В предыдущих разделах даны сведения о разработке с помощью таблиц. В этом разделе пятой, последней мы статистической обработки, что мы узнали до сих и создания базового приложения список с помощью раскадровки.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: d161a267c8ffa5040327db8e6e4f867a324b04f2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105811"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Работа с таблицами в конструкторе iOS

Раскадровки WYSIWYG позволяют создавать приложения iOS, а также поддерживаются в Visual Studio в Mac и Windows. Дополнительные сведения о раскадровках см [введение в раскадровки](~/ios/user-interface/storyboards/index.md) документа. Раскадровки также позволяют изменять макеты ячейки *в* в таблицу, которая упрощает разработку с таблицами и ячейками

При настройке свойств представления таблицы в конструкторе iOS, существует два типа содержимого ячейки, можно выбрать: **динамическое** или **статический** содержимое прототипа.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Прототип динамического содержимого

Объект `UITableView` с прототипом содержимого обычно предназначен для отображения списка данных, где прототип ячейки (или ячеек, как можно определить более одного) используются повторно для каждого элемента в списке. Ячейки не нужно создавать экземпляры, они были получены в `GetView` , вызывая `DequeueReusableCell` метод его `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Статическое содержимое

`UITableView`s с помощью статического содержимого позволяет таблицы разрабатываться прямо в рабочей области конструирования. Ячейки можно перетаскивать в таблицу, а затем настроить путем изменения свойств и добавления элементов управления.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Создание приложения на основе раскадровки

В примере StoryboardTable содержит простое приложение «основной-подробности», которое использует оба типа UITableView в раскадровке. В оставшейся части этого раздела описывается создание небольшого дел пример списка, в котором будет выглядеть после завершения:

 [![Пример экранов](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

Пользовательский интерфейс будет создано с помощью раскадровки, и оба экранов будет использовать UITableView. На главном экране используются *содержимое прототип* макет строки, а также подробные сведения о screen использует *статическое содержимое* для создания формы ввода данных с помощью макетов пользовательской ячейки.

## <a name="walkthrough"></a>Пошаговое руководство

Создайте новое решение в Visual Studio с помощью **(создать) проект... > приложение с одним представлением (C#)** и назовите его _StoryboardTables_.

 [![Создать диалоговое окно нового проекта](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Будет открыто решение с некоторыми C# файлы и `Main.storyboard` уже созданный файл. Дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструкторе iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Изменение раскадровки

Раскадровка будет редактироваться в три этапа:

-  Во-первых, макет, необходимая Просмотр контроллеров и задавать их свойства.
-  Во-вторых создайте свой пользовательский Интерфейс путем перетаскивания объектов в представлении
-  Наконец добавьте необходимый класс UIKit в каждое представление и присвойте имя различные элементы управления, чтобы их можно было ссылаться в коде.


После завершения раскадровки можно добавить код для обеспечения работы.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Макет контроллеров представления

Первое изменение в раскадровку удаление существующего представления сведений и его замена UITableViewController. Выполните следующие действия.

1.  Выберите на панели в нижней части контроллер представления и удалите его.
2.  Перетащите **контроллер навигации** и **контроллер представления таблицы** на раскадровку в области элементов. 
3.  Создание объекта перехода из корневого контроллера представления для второй контроллер представления таблиц, который был только что добавлен. Для создания перехода, управления + перетаскивание *из в ячейку сведений* для вновь добавленного UITableViewController. Выберите вариант **Показать*** в разделе **Segue выбора**. 
4.  Выберите новый segue был создан и назначение ему идентификатора для ссылки, это перехода в коде. Щелкните переход и введите `TaskSegue` для **идентификатор** в **панели свойств**, следующим образом:    
  [![Именование перехода в панели свойств](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Настройте два представления таблицы, выбрав их и с помощью панели свойств. Не забудьте выбрать представления и не View Controller – структуру документа можно использовать для облегчения выбора.

6.  Измените корневой контроллер представления, чтобы быть **содержимого: динамические прототипы** (представление в области конструктора будет меткой **прототип содержимого** ):

    [![Для свойства содержимого значение динамического прототипов](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Измените новый **UITableViewController** быть **содержимого: статические ячеек**. 


8. Новый UITableViewController должен иметь имя класса и идентификатора. Выберите контроллер представления и тип _TaskDetailViewController_ для **класс** в **панели свойств** – это создаст новый `TaskDetailViewController.cs` файлу в решении Панель. Введите **StoryboardID** как _подробно_, как показано в следующем примере. Он будет использоваться позже для загрузки в этом представлении в C# кода:  

    [![Указание идентификатора раскадровки](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. В область конструктора раскадровки теперь должен выглядеть следующим образом (название элемента навигации корневого контроллера представления было изменено на «Тяжкий труд доски»):

    [![Область конструктора](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Создание пользовательского интерфейса

Теперь, представлений и переходами, настроена, элементы пользовательского интерфейса должны быть добавлены.

#### <a name="root-view-controller"></a>Корневой контроллер представления

Во-первых, выделите ячейку прототипа в контроллер представления главного и задайте **идентификатор** как _taskcell_, как показано ниже. Это будет использоваться позже в коде для получения экземпляра этого UITableViewCell:

 [![Задание идентификатора ячейки](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Далее необходимо создать кнопку, которая будет добавлять новые задачи, как показано ниже:

[![панели кнопку на панели навигации](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Выполните следующие действия: 

-  Перетащите **элемента кнопки панели** из области элементов _правой части панели навигации_.
-  В **панели свойств**в разделе **элемента кнопки панели** выберите **идентификатор: добавление** (чтобы сделать его *+* плюс). 
-  Присвойте ему имя, чтобы можно было идентифицировать в коде на более позднем этапе. Обратите внимание, что необходимо будет предоставить корневого контроллера представления имени класса (например **ItemViewController**), чтобы можно было задать имя элемента панели кнопку.


#### <a name="taskdetail-view-controller"></a>Контроллер представления TaskDetail

Подробное представление требует гораздо больше усилий. Представление ячейки таблицы необходимо перетаскивать на представление, а затем заполняется с помощью меток, представлений текста и кнопок. На следующем снимке экрана показано готовое пользовательского интерфейса с двумя разделами. Один раздел имеет три ячейки, три метки, два текстовых поля и один переключиться, а во втором разделе имеет одну ячейку с двумя кнопками:

 [![макет представления сведений](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Приведены инструкции по созданию полного макета.

Выберите вид таблицы и откройте **панели свойство**. Обновите следующие свойства:

-  **Разделы**: _2_ 
-  **Стиль**: _сгруппированных_
-  **Разделитель**: _None_
-  **Выбор**: _не выбрано_

Выберите верхний раздел и в разделе **свойства > раздел представление таблицы** изменить **строк** для _3_, как показано ниже:


 [![Установка в верхнем разделе три строки](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Для каждой ячейки откройте **панели свойств** и задать:

-  **Стиль**: _Custom_
-  **Идентификатор**: выберите уникальный идентификатор для каждой ячейки (например) »_title_«,»_заметки_«,»_сделать_«).
-  Перетащите элементы управления, необходимые для создания макета, показанные на снимке экрана (поместите **UILabel**, **UITextField** и **UISwitch** на правильный ячейки и задайте метки соответствующим образом т. е. Заголовка, примечания и выполнена).


Во втором разделе, задайте **строк** для _1_ и щелкните нижний маркер изменения размера ячейки, чтобы сделать его больше.

-  **Задать идентификатор**: уникальное значение (например) «Сохранить»). 
-  **Меняем цвет фона**: _Очистка цветов_ .
-  Перетащите две кнопки в ячейку и правильно настроить их заголовки (т. е. _Сохранить_ и _удалить_), как показано ниже:

   [![Установка двух кнопок в нижнюю часть окна](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

На этом этапе также можно задать ограничения для ячеек и элементов управления для обеспечения адаптивный макет.

### <a name="adding-uikit-class-and-naming-controls"></a>Добавление класса UIKit и именование элементов управления

Существует несколько заключительных этапах создания нашей раскадровки. Сначала мы должны имя каждого из наших элементов управления в разделе **удостоверений > имя** , они могут использоваться в коде позже. Назовите их следующим образом:

-  **Заголовок UITextField** : _TitleText_
-  **Заметки о UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Удалить UIButton** : _DeleteButton_
-  **Сохранить UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Добавление кода

Остальная часть работы выполняется в Visual Studio на Mac или Windows с C#. Обратите внимание на то, что имена свойств, используемое в коде отражаются в этом пошаговом руководстве выше.

Во-первых, мы хотим создать `Chores` класс, который предоставляет способ получения и задания значения ID, имя, заметки и сделать логическое значение, чтобы мы могли использовать эти значения во всем приложении.

В вашей `Chores` класс добавьте следующий код:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Создайте `RootTableSource` класс, наследующий от `UITableViewSource`. 

Это различие между этим и представление таблицы без использования Storyboard `GetView` метод не должен буду ввести экземпляры ни одной из ячеек — `theDequeueReusableCell` метод всегда будет возвращать экземпляр прототипа ячейки (с соответствующим идентификатором).

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

Чтобы использовать `RootTableSource` создайте новую коллекцию в `ItemViewController`его конструктор:

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

Если вы запустите приложение теперь главном окне теперь загрузит и отображения списка из двух задач. При касании задачу перехода, определяется раскадровки вызовет на экране сведений отображается, но не отобразит никаких данных в данный момент.

Отправляемое «параметр» в segue, переопределить `PrepareForSegue` метод и задать свойства для `DestinationViewController` ( `TaskDetailViewController` в этом примере). Класс контроллера представления назначения будут созданы, но не является, но отобразить для пользователя — это означает, что можно задать свойства класса, но не изменять любые элементы управления пользовательского интерфейса:

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

В `TaskDetailViewController` `SetTask` метод назначает его параметры к свойствам, чтобы их можно было ссылаться в ViewWillAppear. Свойства элементов управления не может быть изменен в `SetTask` так как не существует при `PrepareForSegue` вызывается:

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

Переход будет открыть экран сведений и отображать данные выбранной задачи. К сожалению, отсутствует реализация для **Сохранить** и **удалить** кнопки. Перед реализацией кнопок, добавьте эти методы в **ItemViewController.cs** обновлять базовые данные и закрыть экран сведений:

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

Далее необходимо добавить кнопки `TouchUpInside` в обработчике событий `ViewDidLoad` метод **TaskDetailViewController.cs**. `Delegate` Ссылку на свойство, чтобы `ItemViewController` была создана в частности, поэтому мы можем вызвать `SaveTask` и `DeleteTask`, который закройте это представление как часть своей работы:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

Оставшиеся последняя часть функциональных возможностей для создания заключается в создании новых задач. В **ItemViewController.cs** добавьте метод, который создает новые задачи и откроется подробное представление. Для создания экземпляра представления из раскадровки используйте `InstantiateViewController` метод с `Identifier` для этого представления — в этом примере, который будет «detail»:

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

Наконец, привязывания кнопки на панели навигации в **ItemViewController.cs**в `ViewDidLoad` метод для вызова:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

На этом завершается пример раскадровки — готовое приложение выглядит так:

[![Готовое приложение](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

В примере:

-  Создание таблицы с прототипа содержимого, где, определенных для повторного использования для отображения списков данных. 
-  Создание таблицы со статического содержимого для построения форму ввода. Это включено изменение стиля таблицы и добавление разделов и ячеек и элементов управления пользовательского интерфейса. 
-  Создание объекта перехода и переопределить `PrepareForSegue` метод для уведомления целевое представление всех параметров, он требует. 
-  Загрузка представления раскадровки непосредственно с `Storyboard.InstantiateViewController` метод.



## <a name="related-links"></a>Связанные ссылки

- [StoryboardTable (пример)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
