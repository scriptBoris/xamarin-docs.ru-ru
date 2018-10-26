---
title: Создание настраиваемого поставщика ContentProvider
description: Выше было показано, как получать данные от встроенной реализацией поставщик содержимого. В этом разделе объясняется, как создавать настраиваемого поставщика ContentProvider и затем использовать его данные.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108019"
---
# <a name="creating-a-custom-contentprovider"></a>Создание настраиваемого поставщика ContentProvider

_Выше было показано, как получать данные от встроенной реализацией поставщик содержимого. В этом разделе объясняется, как создавать настраиваемого поставщика ContentProvider и затем использовать его данные._

## <a name="about-contentproviders"></a>О поставщиков содержимого

Класс поставщика содержимого необходимо наследовать от `ContentProvider`. Оно должно состоять из внутреннего хранилища данных, используемый для ответа на запросы, и он должен предоставлять URI и типы MIME, принимая допустимые запросы для данных, константы, позволяющие коду-потребителю.

### <a name="uri-authority"></a>URI (центр)

`ContentProviders` осуществляется в Android с помощью Uri. Это приложение, предоставляющее `ContentProvider` задает URI, которые будет отвечать в его **AndroidManifest.xml** файла. При установке приложения эти URI регистрируются таким образом, могут обращаться другие приложения.

В Mono для Android, должна иметь класс поставщика содержимого `[ContentProvider]` атрибут для указания Uri (или URI), должны добавляться в **AndroidManifest.xml**.


### <a name="mime-type"></a>Тип MIME

Обычный формат для типов MIME состоит из двух частей. Android `ContentProviders` обычно используют эти две строки для первой части типа MIME:

1. `vnd.android.cursor.item` &ndash; Чтобы представлять одну строку, используйте `ContentResolver.CursorItemBaseType` константы в коде.

1. `vnd.android.cursor.dir` &ndash; для нескольких строк используйте `ContentResolver.CursorDirBaseType` константы в коде.

Во второй части MIME-тип, к конкретному приложению, а также следует использовать стандартный обратного запроса DNS с `vnd.` префикс. В примере кода используется `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Метаданные модели данных

Потребляющие приложения должны создавать запросы Uri для доступа к данных разного типа. Базовый Uri могут быть раскрыты для обращения к определенной таблице данных и также может включать параметры для фильтрации результатов. Столбцы и предложения, используемые с этот результирующий курсор для отображения данных также должны быть объявлены.

Чтобы убедиться, что только допустимые Uri запросы создаются, обычно для предоставления допустимых строк как постоянные величины. Это упрощает доступ `ContentProvider` , так как он делает значения с помощью функции завершения кода, а также блокирует опечаток в строках.

В предыдущем примере `android.provider.ContactsContract` класс представлен метаданные для данных контактов. Для наших пользовательских `ContentProvider` мы просто будет предоставлять константы в самом классе.


## <a name="implementation"></a>Реализация

Три шага для создания и использования пользовательского `ContentProvider`:

1. **Создать класс базы данных** &ndash; реализуйте `SQLiteOpenHelper`.

2. **Создание `ContentProvider` класс** &ndash; реализуйте `ContentProvider` с экземпляром базы данных, метаданных в виде значений констант и методы для доступа к данным.

3. **Доступ `ContentProvider` через его Uri** &ndash; заполнить `CursorAdapter` с помощью `ContentProvider`, доступ к которым осуществляется через его Uri.

Как уже упоминалось, `ContentProviders` могут использоваться из приложений, отличных от которых они определены. В этом примере данные используются в одном приложении, но имейте в виду, что другие приложения можно также получить доступ к его до тех пор, пока они знают Uri и сведения о схеме (который обычно указывается в виде постоянных значений).


## <a name="create-a-database"></a>Создание базы данных

Большинство `ContentProvider` реализации будет основываться на `SQLite` базы данных. Пример кода базы данных в **SimpleContentProvider/VegetableDatabase.cs** создает очень простая база данных двух столбцов, как показано:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

  public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
  public override void OnCreate(SQLiteDatabase db)
  {
    db.ExecSQL(create_table_sql);
    // seed with data
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
  }
  public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
  {
    throw new NotImplementedException();
  }
}
```

Сама реализация базы данных не требуется специальных усилий должен быть предоставлен с `ContentProvider`, однако если требуется привязать `ContentProvider's` данные `ListView` Управление затем уникальный целочисленный столбец с именем `_id` должны быть частью результирующий набор. См. в разделе [ListView и адаптерам](~/android/user-interface/layouts/list-view/index.md) Дополнительные сведения об использовании `ListView` элемента управления.


## <a name="create-the-contentprovider"></a>Создайте поставщик содержимого

Оставшейся части этого раздела приводятся пошаговые инструкции о том, как **SimpleContentProvider/VegetableProvider.cs** был построен пример класса.


### <a name="initialize-the-database"></a>Инициализация базы данных

Первым шагом является подкласс `ContentProvider` и добавить базу данных, которая будет использоваться.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

Остальная часть кода будут формировать реализацию фактического поставщика содержимого, которая позволяет данные могли быть обнаружены и запрос.



## <a name="add-metadata-for-consumers"></a>Добавление метаданных для потребителей

Существует четыре различных типа метаданных, который будет использоваться для предоставления в `ContentProvider` класса. Только необходимые полномочия, остальные выполняются по соглашению.

- **Центр** &ndash; `ContentProvider` атрибут *необходимо* добавляются к классу, таким образом, чтобы он регистрируется в Android при установке приложения.

- **URI** &ndash; `CONTENT_URI` предоставляется как константу, так как это простой в использовании в коде. Должно соответствовать полномочия, но включать схему и базовый путь.

- **Типы MIME** &ndash; списки результаты и единый рассматриваются как разные типы содержимого, поэтому мы определим два типа MIME для представления их.

- **InterfaceConsts** &ndash; обеспечивают постоянное значение для каждого столбца данных, позволяя коду-потребителю можно легко находить и обращаться к ним без риска опечаток.

Этот код показывает, каждый из этих элементов реализации, добавив к определению базы данных из предыдущего шага:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>Реализовать синтаксический анализ вспомогательный URI

Так как коду-потребителю коды URI используются для выполнения запросов из `ContentProvider`, нам нужно иметь возможность анализировать эти запросы, чтобы определить, какие данные для возврата. `UriMatcher` Класс может помочь при синтаксическом анализе URI, когда он был инициализирован с помощью Uri шаблоны, которые `ContentProvider` поддерживает.

`UriMatcher` В примере инициализируются с помощью двух URI:

1. *«com.xamarin.sample.VegetableProvider/vegetables»* &ndash; запрос, чтобы получить полный список овощи.

2. *«com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; где \# — это числовой параметр ( `_id` строки в базе данных). Местозаполнитель звездочки (»\*«) можно также использовать в соответствии с текстовым параметром.

В коде мы используем константы для ссылки на значения метаданных, как центр и БАЗОВЫМ\_путь. Коды возврата будет использоваться в методы, выполняющие синтаксический анализ, чтобы определить, какие данные для возврата Uri.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Этот код закрыта все `ContentProvider` класса. Ссылаться на [документации Google UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) для получения дополнительных сведений.


## <a name="implement-the-querymethod"></a>Реализуйте QueryMethod

Самый простой `ContentProvider` метод — `Query` метод. Реализация ниже используется `UriMatcher` проанализировать `uri` параметр и вызовите метод нужную базу данных. Если `uri` содержит параметр идентификатора разбивается целое число (с помощью `LastPathSegment`) и используется в запросе к базе данных.

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

`GetType` Необходимо также переопределить метод. Этот метод может вызываться для определения типа содержимого, который будет возвращаться для заданного Uri.
Это определит, потребляющему приложению способ обработки этих данных.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>Реализовать другие переопределения

Наш простой пример запрещены для редактирования или удаления данных, но должны быть реализованы методы Insert, Update и Delete так, добавьте их без реализации:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

На этом процесс базовый `ContentProvider` реализации. После установки приложения, он предоставляет данные будут доступны в приложении, но для любого другого приложения, который знает Uri для ссылки на него.


## <a name="access-the-contentprovider"></a>Доступ к ContentProvider

Один раз `VegetableProvider` был реализован, доступ к нему осуществляется так же, как поставщик контакты в начале этого документа: получение курсора с помощью указанного Uri, а затем использовать адаптер для доступа к данным.


## <a name="bind-a-listview-to-a-contentprovider"></a>Привязка ListView к ContentProvider

Для заполнения `ListView` с данными, мы используем Uri, который соответствует списку нефильтрованные овощей. В коде, мы используем значение константы `VegetableProvider.CONTENT_URI`, который мы знаем, разрешается в `com.xamarin.sample.vegetableprovider/vegetables`. Наши `VegetableProvider.Query` реализация возвращает курсор, который затем может быть привязан к `ListView`.

Код в `SimpleContentProvider/HomeScreen.cs` показывает, насколько он прост для отображения данных из `ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

Полученное приложение выглядит следующим образом:

[![Снимок экрана со списком Овощей, фруктов, со звукоизоляцией цветок, Legumes, Bulbs, Tubers приложения](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Получить элемент из ContentProvider

Использующее пакет приложение также может потребоваться доступ к отдельных строк данных, это можно сделать, создав другой идентификатор Uri, который ссылается на конкретную строку (например).

Используйте `ContentResolver` непосредственно к один элемент, путем построения Uri с необходимыми `Id`.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

Полный метод выглядит следующим образом:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>Связанные ссылки

- [SimpleContentProvider (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
