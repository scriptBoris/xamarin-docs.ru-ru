---
title: Синхронизация автономных данных с мобильными приложениями Azure
description: В этой статье объясняется, как добавить функцию автономной синхронизации в приложение Xamarin.Forms, которое использует внутренний сервер мобильных приложений Azure.
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058182"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>Синхронизация автономных данных с мобильными приложениями Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_Автономная синхронизация позволяет пользователям взаимодействовать с мобильными приложениями, просмотр, добавление или изменение данных, даже в том случае, где нет сетевого подключения. Изменения сохраняются в локальной базе данных, а когда устройство подключено к сети, изменения можно синхронизировать с экземпляром мобильных приложений Azure. В этой статье объясняется, как добавить функцию автономной синхронизации для приложения Xamarin.Forms._

## <a name="overview"></a>Обзор

[Пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) предоставляет `IMobileServiceTable` интерфейс, который представляет операции, которые могут выполняться для таблиц, сохраненных в экземпляре мобильных приложений Azure. Эти операции напрямую подключаться к экземпляру мобильных приложений Azure и завершится ошибкой, если мобильное устройство не имеет сетевого подключения.

Для поддержки автономной синхронизации, пакета SDK Azure Mobile Client поддерживает *синхронизировать таблицы*, которые предоставляются элементом `IMobileServiceSyncTable` интерфейс. Этот интерфейс обеспечивает же создания, чтения, обновления и удаления (CRUD) операции, как `IMobileServiceTable` интерфейс, но операций чтения или записи в локальном хранилище. Локальное хранилище не заполняется новыми данными из экземпляра мобильных приложений Azure до вызова *по запросу* данных. Аналогичным образом, данные не передаются экземпляру мобильных приложений Azure до вызова *принудительной* локальные изменения.

Автономная синхронизация также включает поддержку обнаружения конфликтов при изменении одной записи в локальном хранилище и в экземпляре мобильных приложений Azure, а пользовательское разрешение конфликтов. Конфликты могут быть обработаны, либо в локальном хранилище или в экземпляре мобильных приложений Azure.

Дополнительные сведения об автономной синхронизации см. в разделе [автономной синхронизации данных в мобильных приложениях Azure](/azure/app-service-mobile/app-service-mobile-offline-data-sync/) и [Включение автономной синхронизации для мобильного приложения Xamarin.Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/).

## <a name="setup"></a>Установка

Процесс интеграции автономной синхронизации между приложения Xamarin.Forms и экземпляром мобильных приложений Azure выглядит следующим образом:

1. Создайте экземпляр мобильных приложений Azure. Дополнительные сведения см. в разделе [использование мобильное приложение Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Добавить [Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) пакет NuGet для всех проектов в решение Xamarin.Forms.
1. (Необязательно) Включите проверку подлинности в экземпляра мобильных приложений Azure и приложения Xamarin.Forms. Дополнительные сведения см. в разделе [проверки подлинности пользователей с помощью мобильных приложений Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Следующий раздел содержит дополнительные инструкции по настройке проектов универсальной платформы Windows (UWP) для использования пакета Microsoft.Azure.Mobile.Client.SQLiteStore NuGet. Дополнительные настройки не обязательно использовать пакет Microsoft.Azure.Mobile.Client.SQLiteStore NuGet в iOS и Android.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

Чтобы использовать SQLite для универсальной платформы Windows (UWP), выполните следующие действия.

1. Установка [SQLite для универсальной платформы Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix) расширение Visual Studio в среде разработки.
1. В проекте UWP в Visual Studio, щелкните правой кнопкой мыши **ссылки > Добавить ссылку**, перейдите к **расширения** и добавьте **SQLite для универсальной платформы Windows** и  **Среда выполнения 2015 Visual C++ для приложений универсальной платформы Windows** расширение для проекта универсальной платформы Windows.

## <a name="initializing-the-local-store"></a>Инициализация Local Store

Необходимо инициализировать локальное хранилище, чтобы можно было выполнять операции с таблицей синхронизации. Для этого в проекте переносимой библиотеки классов (PCL) Xamarin.Forms решения:

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

Созданный новый локальной базы данных SQLite `MobileServiceSQLiteStore` класса, при условии, что он еще не существует. Затем `DefineTable<T>` метод создает таблицу в локальном хранилище, соответствующую полям в `TodoItem` введите, при условии, что он еще не существует.

Объект *контекст синхронизации* связан с `MobileServiceClient` экземпляра и отслеживает изменения, внесенные с помощью таблиц синхронизации. Контекст синхронизации обеспечивает, что очередь, которая отслеживает упорядоченный список операций создания, обновления и удаления (CUD), который будет отправлен к экземпляру мобильных приложений Azure позже. `IMobileServiceSyncContext.InitializeAsync()` Метод используется для связывания с контекстом синхронизации локального хранилища.

`todoTable` Поле является `IMobileServiceSyncTable`, и поэтому все операции CRUD использовать локальное хранилище.

## <a name="performing-synchronization"></a>Выполнение синхронизации

Локальное хранилище синхронизируется с Azure Mobile Apps экземпляром при `SyncAsync` вызывается метод:

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

`IMobileServiceSyncTable.PushAsync` Метод работает на контекст синхронизации, а не конкретной таблицы, а также отправляет все изменения CUD с момента последней отправки.

Выполняемые по запросу `IMobileServiceSyncTable.PullAsync` метод для одной таблицы. Первый параметр для `PullAsync` метод — это имя запроса, который используется только на мобильном устройстве. Результатами запроса от null имя при выполнении пакета SDK Azure Mobile Client *добавочной синхронизации*, где каждый раз, операция извлечения возвращает результаты, последние `updatedAt` отметки времени, в результатах хранятся в локальном системные таблицы. Последующие операции извлечения затем получать только записи после этой метки времени. Кроме того *полная синхронизация* достигается путем передачи `null` как имя запроса, в результате всех записей, извлекаемых при каждой операции извлечения. После любой операции синхронизации полученных данных вставляется в локальном хранилище.

> [!NOTE]
> Если для таблицы с ожидающими локальными обновлениями выполняется по запросу, по запросу сначала будет выполнена отправка в контекст синхронизации. Это сводит к минимуму конфликты между изменениями, которые уже поставлены в очередь и новые данные из экземпляра мобильных приложений Azure.

`SyncAsync` Также включает базовую реализацию для обработки конфликтов при изменении одной записи в локальном хранилище и на экземпляре мобильных приложений Azure. Если конфликт связан, что данные были обновлены в локальном хранилище и в случае мобильных приложений Azure `SyncAsync` метод обновляет данные в локальном хранилище из данных, хранящихся в экземпляре мобильных приложений Azure. При возникновении любого конфликта других `SyncAsync` метод удаляет локальное изменение. Это обрабатывает сценарий, где существует локальное изменение данных, которая удаляется из экземпляра мобильных приложений Azure.

В рабочем приложении, разработчикам следует написать собственный `IMobileServiceSyncHandler` обработка конфликтов реализация, которая подходит для их использования. Дополнительные сведения см. в разделе [использовать оптимистическую блокировку для разрешения конфликтов](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency) на портале Azure и [Подробнее о поддержке автономной в пакете SDK для управляемого клиента](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/) в блогах MSDN.

## <a name="purging-data"></a>Очистка данных

Можно снять таблиц в локальном хранилище данных с помощью `IMobileServiceSyncTable.PurgeAsync` метод. Этот метод поддерживает сценарии, например удаления устаревших данных, которые приложение больше не требуется. Например, только отображает пример приложения `TodoItem` экземпляров, которые не выполнены. Таким образом завершенные элементы больше не должны храниться локально. Очистка завершенных элементов из локального хранилища можно выполнить в следующим образом:

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

Вызов `PurgeAsync` также запускает операцию принудительной отправки. Таким образом любые элементы, которые будут помечены как завершенные локально будут отправляться к экземпляру мобильных приложений Azure до их удаления из локального хранилища. Тем не менее, при наличии ожидающих операций синхронизации с экземпляром мобильных приложений Azure, очистка породит `InvalidOperationException` Если `force` параметр имеет значение `true`. Альтернативным вариантом является изучаемый `IMobileServiceSyncContext.PendingOperations` свойство, которое возвращает количество ожидающих операций, которые еще не был отправлен к экземпляру мобильных приложений Azure и выполнять Очистка, только если свойство равно нулю.

> [!NOTE]
> Вызов `PurgeAsync` с `force` параметру присвоить `true` будут потеряны все ожидающие изменения.

## <a name="initiating-synchronization"></a>Запуск синхронизации

В приложении-примере `SyncAsync` метод вызывается с помощью `TodoList.OnAppearing` метод:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

Это означает, что приложение будет пытаться выполнить синхронизацию с экземпляром мобильных приложений Azure, при запуске.

Кроме того, можно будет запускать в iOS и Android с помощью по запросу на обновление в списке данных и на платформах Windows с помощью **синхронизации** кнопку в пользовательском интерфейсе. Дополнительные сведения см. в разделе [потяните, чтобы обновить](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh).

## <a name="summary"></a>Сводка

В этой статье описано, как добавить функцию автономной синхронизации для приложения Xamarin.Forms. Автономная синхронизация позволяет пользователям взаимодействовать с мобильными приложениями, просмотр, добавление или изменение данных, даже в том случае, где нет сетевого подключения. Изменения сохраняются в локальной базе данных, а когда устройство подключено к сети, изменения можно синхронизировать с экземпляром мобильных приложений Azure.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzureAuthOfflineSync (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Проверка подлинности пользователей с мобильными приложениями Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure SDK для мобильного клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
