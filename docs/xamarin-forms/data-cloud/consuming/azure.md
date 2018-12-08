---
title: Использование мобильного приложения Azure
description: В этой статье, которая применяется только для мобильных приложений Azure, использовать серверной части Node.js, объясняется, как для запросов, вставки, обновления и удаления данных, хранящихся в таблице экземпляра мобильных приложений Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1ac68992d36627eb5d6aee0d4d19564ce63a3936
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052139"
---
# <a name="consuming-an-azure-mobile-app"></a>Использование мобильного приложения Azure

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Мобильные приложения Azure дают возможность разрабатывать приложения с помощью масштабируемых серверных систем, размещенных в службе приложений Azure, поддерживая мобильная проверка подлинности, автономной синхронизации и Push-уведомлений. В этой статье, которая применяется только для мобильных приложений Azure, использовать серверной части Node.js, объясняется, как для запросов, вставки, обновления и удаления данных, хранящихся в таблице экземпляра мобильных приложений Azure._

> [!NOTE]
> Начиная с 30 июня, все новые мобильные приложения Azure будут создаваться с TLS 1.2 по умолчанию. Кроме того, мы также рекомендуем существующих мобильных приложений Azure быть перенастроены на использование TLS 1.2. Сведения о том, как принудительное применение TLS 1.2 в мобильное приложение Azure, см. в разделе [принудительное применение TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112). Сведения о том, как настроить проекты Xamarin для использования TLS 1.2, см. в разделе [уровня безопасности транспорта (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Сведения о том, как для создания экземпляра мобильных приложений Azure, который можно использовать с Xamarin.Forms, см. в разделе [Создание приложения Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). После выполнения этих инструкций, загружаемый пример приложения можно настроить на использование экземпляра мобильных приложений Azure, задав `Constants.ApplicationURL` на URL-адрес экземпляра мобильных приложений Azure. Затем при выполнении примера приложения его будет подключаться к экземпляру мобильных приложений Azure, как показано на следующем снимке экрана:

![](azure-images/portal.png "Пример приложения")

Доступ к мобильным приложениям Azure осуществляется через [пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), и все подключения из примера приложения Xamarin.Forms для Azure выполняются по протоколу HTTPS.

> [!NOTE]
> В iOS 9 и более поздней версии приложение Transport Security (ATS) обеспечивает безопасных соединений между Интернет-ресурсов (например, server серверной части приложения) и приложения, тем самым предотвращая случайное раскрытие конфиденциальной информации. Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9, все подключения будут применяться требования к безопасности ATS. Если соединения не удовлетворяют этим требованиям, они вызовут сбой с исключением.
> Если невозможно использовать из могут быть присоединены ATS `HTTPS` протокола и безопасный обмен данными для Интернет-ресурсов. Это достигается путем обновления приложения **Info.plist** файла. Дополнительные сведения см. в разделе [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Использование экземпляра мобильного приложения Azure

[Пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) предоставляет `MobileServiceClient` класс, который используется в приложении Xamarin.Forms для доступа к экземпляру мобильных приложений Azure, как показано в следующем примере кода:

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

Когда `MobileServiceClient` создается экземпляр, URL-адрес приложения должен быть указан для идентификации экземпляра мобильных приложений Azure. Это значение можно получить с помощью панели мониторинга для мобильного приложения в [портал Microsoft Azure](https://portal.azure.com/).

Ссылку на `TodoItem` таблицы, хранящейся в экземпляре мобильных приложений Azure должен быть получен, прежде чем операции могут выполняться на данной таблице. Это достигается путем вызова `GetTable` метод `MobileServiceClient` экземпляр, который возвращает `IMobileServiceTable<TodoItem>` ссылки.

### <a name="querying-data"></a>Запросы к данным

Содержимое таблицы можно получить, вызвав `IMobileServiceTable.ToEnumerableAsync` метод, который асинхронно вычисляет запрос и возвращает результаты. Данные также могут быть отфильтрованы на стороне сервера, включая `Where` предложение в запросе. `Where` Предложение применяет предикат фильтрации строк для запроса к таблице, как показано в следующем примере кода:

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

Этот запрос возвращает все элементы из `TodoItem` поле которой `Done` равно `false`. Результаты запроса затем помещается в `ObservableCollection` для отображения.

### <a name="inserting-data"></a>Вставка данных

При вставке данных в экземпляре мобильных приложений Azure, новые столбцы будут созданы автоматически в таблице, при необходимости, указано, что Динамическая схема включена в экземпляре мобильных приложений Azure. `IMobileServiceTable.InsertAsync` Метод используется для вставки новой строки данных в указанной таблице, как показано в следующем примере кода:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

При создании запроса вставки, идентификатор не должен указываться в данных, передаваемых экземпляру мобильных приложений Azure. Если в запросе вставки содержит идентификатор `MobileServiceInvalidOperationException` будет создано.

После `InsertAsync` метод завершения, идентификатор данных в экземпляре мобильных приложений Azure будут заполнены в `TodoItem` экземпляра в приложении Xamarin.Forms.

### <a name="updating-data"></a>Обновление данных

При обновлении данных в экземпляре мобильных приложений Azure, новые столбцы будут созданы автоматически в таблице, при необходимости, указано, что Динамическая схема включена в экземпляре мобильных приложений Azure. `IMobileServiceTable.UpdateAsync` Метод используется для обновления существующих данных новыми данными, как показано в следующем примере кода:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

При выполнении запроса на обновление, необходимо указан идентификатор, который экземпляра мобильных приложений Azure можно определить данные будут обновляться. Это значение идентификатора хранится в `TodoItem.ID` свойство. Если запрос на обновление не содержит идентификатор нет способа обновления для экземпляра мобильных приложений Azure определить данные и поэтому `MobileServiceInvalidOperationException` будет создано.

### <a name="deleting-data"></a>Удаление данных

`IMobileServiceTable.DeleteAsync` Метод используется для удаления данных из таблицы мобильных приложений Azure, как показано в следующем примере кода:

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

При выполнении запроса на удаление, таким образом, чтобы sinstance мобильного приложения Azure можно определить данные для удаления необходимо указать идентификатор. Это значение идентификатора хранится в `TodoItem.ID` свойство. Если запрос delete не содержит идентификатор, нет способа для экземпляра мобильных приложений Azure определить данные для удаления и поэтому `MobileServiceInvalidOperationException` будет создано.

## <a name="summary"></a>Сводка

В этой статье описываются способы использования [пакета SDK Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) для запроса, вставки, обновления и удаления данных, хранящихся в таблице в экземпляре приложения мобильных служб Azure. Пакет SDK предоставляет `MobileServiceClient` класс, используемый для доступа к экземпляру мобильных приложений Azure в приложении Xamarin.Forms.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzure (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Создание приложения Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure SDK для мобильного клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
