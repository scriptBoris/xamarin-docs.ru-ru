---
title: Профиль пользователя
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/22/2018
ms.openlocfilehash: 1eaae86ab9eacf007eca792d96e889db6f367922
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765510"
---
# <a name="user-profile"></a>Профиль пользователя

Android поддерживала перечислением контактов с [ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/) поставщика с момента API уровня 5. Например, список контактов является просто, как использовать [ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/) класса, как показано в следующем примере кода:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

Начиная с Android 4 (API уровня 14) [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/) класс доступен через `ContactsContract` поставщика. `ContactsContact.Profile` Предоставляет доступ к личный профиль владельца устройства, включая контактные данные, такие как владельца устройства имя и номер телефона.


## <a name="required-permissions"></a>Необходимые разрешения

Для чтения и записи контактные данные, приложения должны запрашивать `READ_CONTACTS` и `WRITE_CONTACTS` разрешения, соответственно.
Кроме того, для чтения и редактирования профиля пользователя, приложения должны запрашивать `READ_PROFILE` и `WRITE_PROFILE` разрешения.


## <a name="updating-profile-data"></a>Обновление данных профиля

После задания этих разрешений приложение может использовать обычные методы Android для взаимодействия с данными профиля пользователя. Например, чтобы обновить отображаемое имя профиля, вызовите [ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update) с `Uri` извлечь с помощью [ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/) свойства, как показано ниже:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Чтение данных профиля

Выполнение запроса для [ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/) чтений обратно данные профиля. Например следующий код будет считывать отображаемое имя профиля пользователя.

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>Перемещение профиля пользователя

Наконец, чтобы перейти к профилю пользователя, создайте с целью `ActionView` действия и `ContactsContract.Profile.ContentUri` затем передать его в `StartActivity` метод следующим образом:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

При выполнении приведенный выше код, профиль пользователя отображается, как показано на следующем снимке экрана:

[![Снимок экрана: Отображение профиля пользователя John Doe профиля](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

Работа с профилем пользователя аналогична взаимодействия с другими данными в Android, и он обеспечивает дополнительный уровень персонализации устройства.



## <a name="related-links"></a>Связанные ссылки

- [ContactsProviderDemo (пример)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
