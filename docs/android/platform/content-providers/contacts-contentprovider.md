---
title: Используя поставщик содержимого контактов
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 754b81cec7f1adbe5c7ff1c820260e162e226b15
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765845"
---
# <a name="using-the-contacts-contentprovider"></a>Используя поставщик содержимого контактов

Код, что использует доступ к данным, предоставляемым `ContentProvider` не требуют наличия ссылки на `ContentProvider` класса вообще. Вместо этого Uri используется для создания курсора над данными, предоставляемыми `ContentProvider`. Android использует Uri для поиска системы для приложения, которое предоставляет `ContentProvider` с таким идентификатором. Uri является строкой, обычно в формате обратного DNS, такие как `com.android.contacts/data`.

Вместо выполнения разработчики помните, эта строка Android *контактов* поставщик предоставляет его метаданные в `android.provider.ContactsContract` класса. Этот класс используется для определения Uri `ContentProvider` а также имена таблиц и столбцов, которые могут запрашиваться.

Также некоторые типы данных требуют специальных разрешений на доступ к. Требуется встроенный список контактов `android.permission.READ_CONTACTS` разрешение в **AndroidManifest.xml** файла.

Существует три способа создания курсора из Uri:

1. **ManagedQuery()** &ndash; является предпочтительным подходом в Android 2.3 (API уровня 10) и более ранних версиях `ManagedQuery` возвращает курсор, а также автоматически управляет обновление данных и закрытие курсора. Этот метод является устаревшим в версии 3.0 Android (API уровня 11).

1. **ContentResolver.Query()** &ndash; Возвращает неуправляемый курсор, который означает должны обновляться и явно закрыто в коде.

1. **CursorLoader(). LoadInBackground()** &ndash; появился в Android 3.0 (API уровня 11), `CursorLoader` теперь является альтернативный способ использования `ContentProvider` . `CursorLoader` запросы `ContentResolver` в фоновом потоке, поэтому пользовательский Интерфейс не блокируется.
   Этот класс может осуществляться в более старых версиях Android с помощью библиотеки совместимости версии 4.


Каждый из этих методов имеет тот же базовый набор входных данных.

-  **URI** &ndash; полное имя `ContentProvider` .
-  **Проекция** &ndash; спецификация столбцов, выберите для курсора.
-  **Выбор** &ndash; аналогична SQL `WHERE` предложения.
-  **SelectionArgs** &ndash; параметры, которые будут заменены в выделенном фрагменте.
-  **SortOrder** &ndash; столбцы, по которому выполняется сортировка.



## <a name="creating-inputs-for-a-query"></a>Создание входных данных для запроса

`ContactsProvider` Образец кода выполняет очень простой запрос к Android встроенный поставщик контактов. Необходимо знать фактические Uri или столбец имена - все сведения, необходимые для запроса контакты `ContentProvider` доступен в виде константы, предоставляемые `ContactsContract` класса.

Независимо от того, какой метод используется для получения курсора, эти же объекты используются в качестве параметров, как показано в *ContactsProvider/ContactsAdapter.cs* файла:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

В этом примере `selection`, `selectionArgs` и `sortOrder` будет игнорироваться, задавая для них `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Создается курсор из Uri поставщика содержимого

После создания объектов параметров, они могут использоваться в одном из следующих трех способов:



### <a name="using-a-managed-query"></a>С помощью управляемых запроса

Приложения, предназначенные для Android 2.3 (API уровня 10) или более ранней версии, следует использовать этот метод:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Этот курсор будет под управлением Android, поэтому необходимо закрыть его.



### <a name="using-contentresolver"></a>С помощью ContentResolver

Доступ к `ContentResolver` непосредственно для получения курсора к `ContentProvider` можно следующим образом:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Этот курсор является неуправляемым, поэтому он должен быть закрыт, больше не требуется.
Убедитесь, что код закрывает курсор, который открыт, в противном случае возникнет ошибка.

```csharp
cursor.Close();
```

Кроме того, можно вызвать метод `StartManagingCursor()` и `StopManagingCursor()` для управления курсора. Курсоры, управляемые автоматически деактивировать и повторно запросить при остановке и перезапуске действий.



### <a name="using-cursorloader"></a>С помощью CursorLoader

Построение приложений для Android 3.0 (API уровня 11) или более новой этот метод следует использовать:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader` Гарантирует, что все операции с курсорами производятся в фоновом потоке и можно интеллектуально повторно использовать существующие курсора экземпляров действия после действие перезапуска (например, из-за изменения конфигурации) вместо, перезагрузить данные еще раз.

Можно также использовать более ранних версиях Android `CursorLoader` класса с помощью [библиотеки поддержки v4](http://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Отображение данных курсора с помощью настраиваемого адаптера

Для отображения контактных изображения мы будем использовать пользовательский адаптер, чтобы вручную устранить `PhotoId` ссылку на путь к файлу изображения.

Чтобы отобразить данные с помощью настраиваемого адаптера, в этом примере `CursorLoader` получить все данные контакта в локальную коллекцию в **FillContacts** метод **ContactsProvider/ContactsAdapter.cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

Затем реализовать с помощью методов BaseAdapter `contactList` коллекции. Реализации адаптера, так же, как и с любой другой коллекции &ndash; нет без специальной обработки, поскольку данные, полученные из `ContentProvider`:

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

Изображение (если он существует) с использованием Uri на файл изображения на устройстве. Оно выглядит следующим образом:

[![Снимок экрана приложения Отображение контактов в ListView; изображение отображается слева от одной записи](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

С помощью той же модели кода, приложение может обращаться к разнообразных системных данных, включая пользователя фотографий, видео и музыки.
Некоторые типы данных требуют специальных разрешений в проекте **AndroidManifest.xml**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Отображение данных курсора с SimpleCursorAdapter

Курсор, также может отображаться с `SimpleCursorAdapter` (несмотря на то, что отображается только имя, не фотографии). Этот код показывает, как использовать `ContentProvider` с `SimpleCursorAdapter` (этот код не отображается в этом образце):

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Ссылаться на [представлениях ListView и адаптеры](~/android/user-interface/layouts/list-view/index.md) для получения дополнительных сведений о реализации `SimpleCursorAdapter`.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация ContactsAdapter (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
