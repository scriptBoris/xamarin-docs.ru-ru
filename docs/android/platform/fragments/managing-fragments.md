---
title: Управление фрагментами
ms.prod: xamarin
ms.assetid: 02C5E8F0-32EF-4FD9-DC8B-04650E20722C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 107877d0e92d3a46101812b78bc0b414c0fbb320
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105471"
---
# <a name="managing-fragments"></a>Управление фрагментами

Чтобы упростить управление фрагментами, Android предоставляет `FragmentManager` класса. Каждое действие имеет экземпляр `Android.App.FragmentManager` , позволяющие найти и динамически изменить его фрагментов. Каждый набор этих изменений называется *транзакции*и выполняется с помощью одного из API-интерфейсы, содержащиеся в классе `Android.App.FragmentTransation`, которое управляется `FragmentManager`. Действие может запустить транзакцию следующим образом:

```csharp
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
```

Эти изменения фрагментам выполняются в `FragmentTransaction` экземпляра с помощью методов, таких как `Add()`, `Remove(),` и `Replace().` изменения применяются с помощью `Commit()`. Изменения в транзакции не выполняются немедленно.
Вместо этого они планируются для запуска в потоке пользовательского интерфейса, действия, как можно скорее.

В следующем примере показано, как добавить фрагмент в существующий контейнер:

```csharp
// Create a new fragment and a transaction.
FragmentTransaction fragmentTx = this.FragmentManager.BeginTransaction();
DetailsFragment aDifferentDetailsFrag = new DetailsFragment();

// The fragment will have the ID of Resource.Id.fragment_container.
fragmentTx.Add(Resource.Id.fragment_container, aDifferentDetailsFrag);

// Commit the transaction.
fragmentTx.Commit();
```

Если транзакция фиксируется после `Activity.OnSaveInstanceState()` является именем, будет создано исключение. Это происходит потому, что когда действие сохраняет свое состояние, Android также сохраняет состояние любого размещенного фрагментов. Если любой фрагмент транзакции будут зафиксированы после этой точки, состояние этих транзакций будут потеряны при восстановлении действия.

Существует возможность сохранения транзакции фрагмент в действие [стек переходов назад](http://developer.android.com/guide/topics/fundamentals/tasks-and-back-stack.html) с помощью вызова `FragmentTransaction.AddToBackStack()`. Это позволяет пользователю переходить назад с помощью фрагмента изменяется, когда **обратно** нажатии кнопки. Без вызова этого метода фрагментов, которые будут удалены будут уничтожены и будут недоступны, если пользователь перемещается обратно через действия.

В следующем примере показано, как использовать `AddToBackStack` метод `FragmentTransaction` для замены одного фрагмента, сохраняя состояние первый фрагмент в стеке переходов назад:

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


## <a name="communicating-with-fragments"></a>Взаимодействие с использованием фрагментов

*FragmentManager* было известно обо всех фрагментов, вложенные в действие и предоставляет два метода для поиска этих фрагментов:

-   **FindFragmentById** &ndash; этот метод будет найти фрагмент с помощью идентификатора, который был указан в файле макета или идентификатор контейнера, при добавлении фрагмента как часть транзакции.

-   **FindFragmentByTag** &ndash; этот метод используется, чтобы найти фрагмент, который содержит тег, который был указан в файле макета или который был добавлен в транзакции.

Справочник по фрагментов и действий `FragmentManager`, таким образом, те же методы используются для обмена данными и обратно, между ними. Приложение может найти ссылку фрагмента с помощью одного из этих двух методов, приведение ссылки к соответствующему типу и напрямую вызывать методы в фрагменте. Пример в следующем фрагменте кода:

Возможна также действие, которое используется `FragmentManager` для поиска фрагментов:

```csharp
var emailList = FragmentManager.FindFragmentById<EmailListFragment>(Resource.Id.email_list_fragment);
emailList.SomeCustomMethod(parameter1, parameter2);
```


### <a name="communicating-with-the-activity"></a>Связи с действия

Можно использовать фрагмента `Fragment.Activity` свойство для ссылки на узел. Путем приведения действие более конкретный тип, можно действие может вызывать методы и свойства своего узла, как показано в следующем примере:

```csharp
var myActivity = (MyActivity) this.Activity;
myActivity.SomeCustomMethod();
```
