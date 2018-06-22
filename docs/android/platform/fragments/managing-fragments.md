---
title: Управление фрагментов
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: b39067b0cb1bbb344866761042db0125fd4a4dc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763921"
---
# <a name="managing-fragments"></a>Управление фрагментов

Чтобы упростить управление фрагментов, предоставляет Android `FragmentManager` класса. Каждое действие имеет экземпляр `Android.App.FragmentManager` , будет найти, или динамически изменять свои фрагменты. Каждого набора этих изменений называется *транзакции*и выполняется с помощью одного из интерфейсов API, содержащиеся в классе `Android.App.FragmentTransation`, который находится под управлением `FragmentManager`. Действие может запустить транзакцию следующим образом:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Эти изменения во фрагментах, выполняются в `FragmentTransaction` экземпляра с помощью методов, таких как `Add()`, `Remove(),` и `Replace().` затем изменения применяются с помощью `Commit()`. Изменения в транзакции не выполняются немедленно.
Вместо этого они планируются для запуска в потоке пользовательского интерфейса, действия, как можно быстрее.

Следующий пример демонстрирует добавление фрагмента в существующий контейнер:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Если транзакция фиксируется после `Activity.OnSaveInstanceState()` является именем, будет создано исключение. Это происходит потому, что когда действие сохраняет свое состояние, Android также сохраняет состояние любого размещенного фрагментов. Любой фрагмент транзакции будут зафиксированы после этой точки, состояние эти транзакции будут потеряны при восстановлении действия.

Существует возможность сохранения транзакций фрагмент в действие [стек](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) путем вызова `FragmentTransaction.AddToBackStack()`. Это позволяет пользователю перемещаться вперед с использованием фрагментов изменяется, когда **обратно** нажатии кнопки. Без вызова этого метода фрагментов, которые будут удалены будут уничтожены и будут недоступны, если пользователь переходит назад по действия.

В следующем примере показано, как использовать `AddToBackStack` метод `FragmentTransaction` для замены одного фрагмента, сохраняя состояние первый фрагмент в стеке назад:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// Replace the fragment that is in the View fragment_container (if applicable).
fragmentTx.Replace(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Add the transaction to the back stack.
fragmentTx.AddToBackStack(null);

// Commit the transaction.
fragmentTx.Commit();
```


## <a name="communicating-with-fragments"></a>Взаимодействие с помощью фрагментов

*FragmentManager* сведения обо всех фрагментов, которые присоединены к действию и предоставляет два метода для поиска этих фрагментов:

-   **FindFragmentById** &ndash; этот метод найдет фрагмента с помощью Идентификатором, который был указан в файле макета или контейнера, при добавлении фрагмента в рамках транзакции.

-   **FindFragmentByTag** &ndash; этот метод используется для поиска фрагмент, который содержит тег, который был указан в файле макета или который был добавлен в транзакции.

Справочник по фрагментов и действий `FragmentManager`, поэтому те же методы, используемые для взаимодействия и обратно, между ними. Приложение может найти ссылку фрагмента с помощью одного из следующих двух методов, приведение к соответствующему типу ссылки и напрямую вызывать методы в фрагменте. Пример в следующем фрагменте кода:

Возможно, что действие, которое используется `FragmentManager` для поиска фрагментов:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Взаимодействие с действием

Существует возможность использовать фрагмент `Fragment.Activity` свойство для своего узла ссылки. Путем приведения к более конкретному типу действия, возможна действие для вызова методов и свойств в узле, как показано в следующем примере:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
