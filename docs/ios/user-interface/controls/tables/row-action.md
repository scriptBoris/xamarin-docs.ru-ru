---
title: Работа с действиями строк в Xamarin.iOS
description: В этом руководстве показано, как создавать пользовательские считывание действия для строк таблицы с UISwipeActionsConfiguration или UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105913"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Работа с действиями строк в Xamarin.iOS

_В этом руководстве показано, как создавать пользовательские считывание действия для строк таблицы с UISwipeActionsConfiguration или UITableViewRowAction_

![Демонстрация действия проведите по экрану в строках](row-action-images/action02.png)

iOS предоставляет два способа для выполнения действий на таблицу: `UISwipeActionsConfiguration` и `UITableViewRowAction`.

`UISwipeActionsConfiguration` появился в iOS 11 и используется для определения набора действий, которые необходимо выполнить тогда, когда пользователь вставляет _в любом направлении_ над строкой в табличном представлении. Это поведение похоже, собственного Mail.app 

`UITableViewRowAction` Класс используется для определения действие, которое будет иметь место, когда пользователь вставляет по горизонтали влево над строкой в табличном представлении.
Например, когда изменение таблицы, проводя пальцем влево в строке отображает **удалить** кнопку по умолчанию. Путем присоединения нескольких экземпляров `UITableViewRowAction` класс `UITableView`, может быть определено несколько пользовательских действий, каждый из которых свой собственный текст, форматирование и поведение.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Существует три действия, необходимые для реализации действия проведите по экрану с `UISwipeActionsConfiguration`:

1. Переопределить `GetLeadingSwipeActionsConfiguration` и/или `GetTrailingSwipeActionsConfiguration` методы. Эти методы возвращают `UISwipeActionsConfiguration`. 
2. Создать экземпляр `UISwipeActionsConfiguration` должны быть возвращены. Этот класс принимает массив `UIContextualAction`.
3. Создайте таблицу `UIContextualAction`.

Это описано более подробно в следующих разделах.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Реализация методов SwipeActionsConfigurations

`UITableViewController` (а также `UITableViewSource` и `UITableViewDelegate`) содержит два метода: `GetLeadingSwipeActionsConfiguration` и `GetTrailingSwipeActionsConfiguration`, которые используются для реализации набор действий, проведите по экрану на строку представления таблицы. Начальные действие прокрутки ссылается на жеста перетаскивания из левой части экрана на языке слева направо и из правой части экрана в языка справа налево. 

Следующий пример (из [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) образец) демонстрируется реализация начальные конфигурации проведите по экрану. Два действия создаются на основе контекстные действия, которым мы вернемся [ниже](#create-uicontextualaction). Эти действия передаются в только что инициализированный [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), который используется в качестве возвращаемого значения.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Создать экземпляр `UISwipeActionsConfiguration`

Создать экземпляр `UISwipeActionsConfiguration` с помощью `FromActions` метод, чтобы добавить новый массив `UIContextualAction`s, как показано в следующем фрагменте кода:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Важно отметить, что порядок отображения действий зависит от способа передачи их в массив. Например приведенный выше для ведущих вставляет отображаются действия как таким образом:

![Начальные действия жеста перетаскивания отображается на строку таблицы](row-action-images/action03.png)

Для конечных вставляет, действия будет отображаться, как показано на следующем рисунке:

![конечные считывание действия отображаются на строку таблицы](row-action-images/action04.png)

Этот фрагмент кода также используется новый `PerformsFirstActionWithFullSwipe` свойство. По умолчанию это свойство имеет значение true, это означает, что первое действие в массиве произойдет, если пользователь предъявляет полностью в строке. Если у вас есть действие, которое не является необратимым (например «удалить», это может быть не идеальным, и поэтому следует задать значение `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Создание `UIContextualAction`

Контекстно-зависимое действие —, где фактически создать действие, которое отображается, если пользователь предъявляет строку таблицы.

Для инициализации необходимо указать действие `UIContextualActionStyle`, title, а также `UIContextualActionHandler`. `UIContextualActionHandler` Принимает три параметра: действие, представление, отображаемое в действие и завершающий обработчик:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Можно изменять различные свойства визуальных элементов, такие как фоновый цвет или рисунок действия. В приведенном выше фрагменте кода демонстрируется добавление изображения к действию и установка его цвета фона на синий.

Когда контекстных действий будут созданы, их можно использовать для инициализации `UISwipeActionsConfiguration` в `GetLeadingSwipeActionsConfiguration` метод.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Для определения одного или нескольких действий пользовательских строк для `UITableView`, необходимо будет создать экземпляр `UITableViewDelegate` класса и переопределить `EditActionsForRow` метод. Пример:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Статический `UITableViewRowAction.Create` метод используется для создания нового `UITableViewRowAction` , будет отображаться **Hi** кнопки, когда пользователь вставляет по горизонтали влево над строкой в таблице. Позже новый экземпляр класса `TableDelegate` создается и прикрепляется к `UITableView`. Пример:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

При выполнении приведенный выше код и вставляет пользователя, оставшегося на строку таблицы **Hi** кнопка будет отображаться вместо **удалить** кнопка, которая отображается по умолчанию:

[![](row-action-images/action01.png "Кнопка Hi, что вместо кнопки Delete отображается")](row-action-images/action01.png#lightbox)

Если пользователь касается **Hi** кнопки `Hello World!` записываются в консоль в Visual Studio для Mac или Visual Studio при запуске приложения в режиме отладки.



## <a name="related-links"></a>Связанные ссылки

- [TableSwipeActions (пример)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (пример)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
