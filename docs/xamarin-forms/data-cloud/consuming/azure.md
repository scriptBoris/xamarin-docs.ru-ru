---
title: Использование мобильного приложения Azure
description: В этой статье, применим только для мобильных приложений Azure, использовать Node.js серверной части, описание запроса, вставки, обновления и удаления данных, хранящихся в таблице в экземпляре мобильных приложений Azure.
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 73d74b59ef6e59028eec7cad19feec21908b6329
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269048"
---
# <a name="consuming-an-azure-mobile-app"></a>Использование мобильного приложения Azure

_Мобильные приложения Azure позволяют разрабатывать приложения с масштабируемой внутренних серверов, размещенных в службе приложений Azure, с поддержкой проверки подлинности мобильных устройств, автономной синхронизации и push-уведомлений. В этой статье, применим только для мобильных приложений Azure, использовать Node.js серверной части, описание запроса, вставки, обновления и удаления данных, хранящихся в таблице в экземпляре мобильных приложений Azure._

> [!NOTE]
> Начиная с 30 июня, все новые мобильные приложения Azure будут создаваться с TLS 1.2 по умолчанию. Кроме того, также рекомендуется существующих мобильных приложений Azure быть перенастроены на использование TLS 1.2. Сведения о том, как применять TLS 1.2 в мобильные приложения Azure в разделе [применять TLS 1.2](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-1112). Сведения о настройке проектов Xamarin для использования TLS 1.2 см. в разделе [Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md).

Сведения о том, как создать экземпляр мобильных приложений Azure, могут быть использованы Xamarin.Forms см. в разделе [Создание приложения Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/). После выполнения этих инструкций можно настроить загружаемый пример приложения следует использовать экземпляр мобильных приложений Azure, задав `Constants.ApplicationURL` URL-адрес экземпляра мобильных приложений Azure. Затем при запуске образца приложения его подключатся к экземпляра мобильных приложений Azure, как показано на следующем снимке экрана:

![](azure-images/portal.png "Образец приложения")

Доступ к мобильных приложений Azure осуществляется через [пакет SDK Azure Mobile клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), и все соединения с Xamarin.Forms примера приложения в Azure выполняются по протоколу HTTPS.

> [!NOTE]
> В iOS 9 и более поздней версии безопасность транспорта приложения (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, приложение серверных) и приложения, чтобы предотвратить случайное раскрытие конфиденциальных сведений. Поскольку ATS включена по умолчанию в приложениях, разработанных для iOS 9, все соединения будут применяться ATS требования безопасности. Если соединения не удовлетворяют этим требованиям, произойдет сбой с исключением.
> Если это не позволяет использовать ATS могут быть присоединены из `HTTPS` протокола и безопасную передачу Интернет-ресурсов. Это можно сделать путем обновления приложения **Info.plist** файла. Дополнительные сведения см. [безопасность транспорта приложения](~/ios/app-fundamentals/ats.md).

## <a name="consuming-an-azure-mobile-app-instance"></a>Использование экземпляра Azure мобильного приложения

[Пакет SDK Azure Mobile клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) предоставляет `MobileServiceClient` класс, который используется приложением Xamarin.Forms для доступа к экземпляру мобильных приложений Azure, как показано в следующем примере кода:

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

Ссылку на `TodoItem` таблице, хранящейся в экземпляре Azure мобильные приложения должны быть получены до выполнения операций на данной таблице. Это достигается путем вызова `GetTable` метод `MobileServiceClient` экземпляра, который возвращает `IMobileServiceTable<TodoItem>` ссылки.

### <a name="querying-data"></a>Запросы к данным

Содержимое таблицы, можно получить, вызвав `IMobileServiceTable.ToEnumerableAsync` метод, который асинхронно выполняет запрос и возвращает результаты. Данные также могут быть отфильтрованы стороне сервера, включая `Where` предложение в запросе. `Where` Предложение применяет предикат в запросе к таблице, фильтрации строк, как показано в следующем примере кода:

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

Этот запрос возвращает все элементы из `TodoItem` таблица, `Done` свойства равен `false`. Результаты запроса затем помещается в `ObservableCollection` для отображения.

### <a name="inserting-data"></a>Вставка данных

При вставке данных в экземпляре мобильных приложений Azure, новые столбцы будут созданы автоматически в таблицу, при необходимости, отображаемая, Динамическая схема включена в экземпляре мобильных приложений Azure. `IMobileServiceTable.InsertAsync` Метод используется для вставки новой строки данных в указанной таблице, как показано в следующем примере кода:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

При создании запроса вставки, идентификатор не должны указываться в данных, передаваемых экземпляру мобильных приложений Azure. Если запрос insert содержит идентификатор `MobileServiceInvalidOperationException` будет создано.

После `InsertAsync` метод завершения, идентификатор данных в экземпляре Azure мобильные приложения, которые будут заполнены в `TodoItem` экземпляра в приложении Xamarin.Forms.

### <a name="updating-data"></a>Обновление данных

При обновлении данных в экземпляре мобильных приложений Azure, новые столбцы будут созданы автоматически в таблицу, при необходимости, отображаемая, Динамическая схема включена в экземпляре мобильных приложений Azure. `IMobileServiceTable.UpdateAsync` Метод используется для обновления существующих данных новыми данными, как показано в следующем примере кода:

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

При выполнении запроса на обновление, идентификатора необходимо указать, чтобы экземпляр мобильные приложения Azure могли определить обновляемые данные. Это значение идентификатора хранится в `TodoItem.ID` свойство. Если запрос на обновление не содержит идентификатор нет возможности для экземпляра мобильных приложений Azure определить данные обновления и поэтому `MobileServiceInvalidOperationException` будет создано.

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

При выполнении запроса delete, идентификатора необходимо указать, чтобы sinstance мобильного приложения Azure можно определить данные для удаления. Это значение идентификатора хранится в `TodoItem.ID` свойство. Если запрос delete не содержит идентификатор, нет возможности для экземпляра мобильных приложений Azure определить данные для удаления и поэтому `MobileServiceInvalidOperationException` будет создано.

## <a name="summary"></a>Сводка

В этой статье описано, как использовать [пакет SDK Azure Mobile клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) для запроса, вставки, обновления и удаления данных, хранящихся в таблице в экземпляре приложения мобильных служб Azure. Пакет SDK предоставляет `MobileServiceClient` класс, используемый приложением Xamarin.Forms для доступа к экземпляру мобильных приложений Azure.


## <a name="related-links"></a>Связанные ссылки

- [TodoAzure (пример)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [Создание приложения Xamarin.Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Пакет SDK для Azure мобильного клиента](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
