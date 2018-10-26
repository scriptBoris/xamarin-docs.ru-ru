---
title: Использование контактов ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 95d11ef692ec8b43c128cb55a21d0973151cd24a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120441"
---
# <a name="using-the-contacts-contentprovider"></a>Использование контактов ContentProvider

Код, что использует доступ к данным, предоставляемым `ContentProvider` не требуют наличия ссылки на `ContentProvider` класса вообще. Вместо этого Uri, которые используются для создания курсора над данными, предоставляемыми `ContentProvider`. Android использует Uri для поиска в системе для приложения, которое предоставляет `ContentProvider` с этим идентификатором. Uri представляет собой строку, обычно в виде обратного запроса DNS, таких как `com.android.contacts/data`.

Чтобы не заставлять разработчиков запомнить эту строку Android *контакты* поставщик предоставляет его метаданные в `android.provider.ContactsContract` класса. Этот класс используется для определения Uri `ContentProvider` а также имена таблиц и столбцов, которые могут запрашиваться.

Некоторые типы данных также требуют специальных разрешений для доступа к. Требуется встроенный список контактов `android.permission.READ_CONTACTS` разрешение в **AndroidManifest.xml** файла.

Существует три способа создания курсора из Uri:

1. **ManagedQuery()** &ndash; предпочтительным подходом в Android 2.3 (уровень API 10) и более ранних версиях `ManagedQuery` возвращает курсор, а также автоматически управляет обновление данных и закрытие курсора. Этот метод рекомендуется использовать в Android 3.0 (11 уровень API).

1. **ContentResolver.Query()** &ndash; возвращает неуправляемых курсор, который означает, что оно должно обновляться и явно закрыто в коде.

1. **CursorLoader(). LoadInBackground()** &ndash; появилось в 3.0. Android (уровень API 11), `CursorLoader` теперь является предпочтительным способом доступа к `ContentProvider` . `CursorLoader` запросы `ContentResolver` в фоновом потоке, поэтому пользовательский Интерфейс не будет блокироваться.
   Этот класс может осуществляться в более старых версиях Android с помощью библиотеки совместимости версии 4.


Каждый из этих методов имеет тот же базовый набор входных данных:

-  **URI** &ndash; полное имя `ContentProvider` .
-  **Проекция** &ndash; спецификация столбцов, выберите для курсора.
-  **Выбор** &ndash; аналогичен SQL `WHERE` предложение.
-  **SelectionArgs** &ndash; параметров должен быть замещен в выделении.
-  **SortOrder** &ndash; столбцов для сортировки.



## <a name="creating-inputs-for-a-query"></a>Создание входных данных для запроса

`ContactsProvider` Пример кода выполняет очень простой запрос к Android встроенный поставщик контактов. Не обязательно знать фактического Uri или имени столбца - все сведения, необходимые для запроса контакты `ContentProvider` доступен в виде константы, предоставляемые `ContactsContract` класса.

Независимо от того, какой метод используется для получения курсора, эти же объекты используются в качестве параметров, как показано в *ContactsProvider/ContactsAdapter.cs* файла:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

В этом примере `selection`, `selectionArgs` и `sortOrder` будет игнорироваться, задавая для них значение `null`.



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Создается курсор из Uri поставщика содержимого

После создания объектов параметров, они могут использоваться в одном из следующих трех способов:



### <a name="using-a-managed-query"></a>С помощью управляемых запроса

Приложения, предназначенные для Android 2.3 (уровень API 10) или более ранней версии, следует использовать этот метод:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Этот курсор будет управляться Android не нужен закрыть его.



### <a name="using-contentresolver"></a>С помощью ContentResolver

Доступ к `ContentResolver` напрямую, чтобы получить курсор от `ContentProvider` можно следующим образом:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Этот курсор является неуправляемым, поэтому он должен быть закрыт, когда больше не требуется.
Убедитесь, что код закрывает курсор, который открыт, в противном случае произойдет ошибка.

```csharp
cursor.Close();
```

Кроме того, можно вызвать `StartManagingCursor()` и `StopManagingCursor()` курсора «управление». Курсоры, управляемые автоматически деактивировать и повторно запрошена при остановке и перезапуске действий.



### <a name="using-cursorloader"></a>С помощью CursorLoader

Построение приложений для Android 3.0 (уровень API 11) или более новые этот метод следует использовать:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader` Гарантирует, что все операции с курсорами выполняются в фоновом потоке и может интеллектуально повторно использовать существующий курсор экземпляров действия после действия перезапуска (например, из-за изменения конфигурации) вместо этого, еще раз перезагрузить данные.

Можно также использовать более ранние версии Android `CursorLoader` , используя [библиотек поддержки v4](http://developer.android.com/tools/support-library/index.html).



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Отображение данных курсора, с помощью пользовательского адаптера

Для отображения изображений контакта мы будем использовать настраиваемый адаптер, а мы вручную устранить `PhotoId` ссылку, чтобы указать путь к файлу изображения.

Для отображения данных с помощью пользовательского адаптера, в примере используется `CursorLoader` для извлечения всех данных контакта в локальной коллекции в **FillContacts** метода из **ContactsProvider/ContactsAdapter.cs**:

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

Затем реализовать с помощью методов BaseAdapter `contactList` коллекции. Реализации адаптера, так же, как было бы с любой другой коллекцией &ndash; нет без специальной обработки, так как находится источник данных из `ContentProvider`:

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

Изображение отображается (если он существует) с помощью Uri к файлу изображения на устройстве. Приложение выглядит следующим образом:

[![Снимок экрана приложения, Отображение контактов в ListView; изображение отображается слева от одной записи](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

С помощью той же модели кода, приложение может обращаться к самых разнообразных системных данных, включая пользователя фотографий, видео и музыку.
Некоторые типы данных требуют специальных разрешений в проекте **AndroidManifest.xml**.



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Отображение данных курсора с SimpleCursorAdapter

Курсор может также отображаться с `SimpleCursorAdapter` (несмотря на то, что отображается только имя, не фото). Этот код демонстрирует использование `ContentProvider` с `SimpleCursorAdapter` (этот код не отображается в образце):

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

Ссылаться на [ListView и адаптерам](~/android/user-interface/layouts/list-view/index.md) для получения дополнительных сведений о реализации `SimpleCursorAdapter`.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация ContactsAdapter (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
