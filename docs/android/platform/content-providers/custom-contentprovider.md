---
title: Создание пользовательских поставщик содержимого
description: Выше было показано, как использовать данные из встроенных реализации поставщик содержимого. В этом разделе объясняется, как построить пользовательский поставщик содержимого, а затем использовать его данные.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: 95d38fae1614bbb12ddafaeca60d50e63404ea95
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769033"
---
# <a name="creating-a-custom-contentprovider"></a>Создание пользовательских поставщик содержимого

_Выше было показано, как использовать данные из встроенных реализации поставщик содержимого. В этом разделе объясняется, как построить пользовательский поставщик содержимого, а затем использовать его данные._

## <a name="about-contentproviders"></a>О ContentProviders

Класс поставщика содержимого должен наследоваться от `ContentProvider`. Он должен состоять из внутреннего хранилища данных, используемый для ответа на запросы и он должен предоставлять URI и типы MIME как константы, код допустимых запросов для данных.

### <a name="uri-authority"></a>URI (центр)

`ContentProviders` осуществляется в Android с помощью Uri. Это приложение, предоставляющее `ContentProvider` задает URI, которые будет отвечать в его **AndroidManifest.xml** файла. При установке приложения эти URI регистрируются, чтобы другие приложения могли обращаться к ним.

Моно для Android, должна иметь класс поставщика содержимого `[ContentProvider]` атрибут для указания Uri (или URI), должны быть добавлены в **AndroidManifest.xml**.


### <a name="mime-type"></a>MIME-тип

Обычный формат для типов MIME состоит из двух частей. Android `ContentProviders` обычно используют эти две строки в первой части MIME-тип:

1. `vnd.android.cursor.item` &ndash; для представления одной строки, используйте `ContentResolver.CursorItemBaseType` константы в коде.

1. `vnd.android.cursor.dir` &ndash; несколько строк, используйте `ContentResolver.CursorDirBaseType` константы в коде.

Во второй части MIME-тип, относящихся к конкретному приложению и следует использовать стандартный обратного DNS с `vnd.` префикс. В примере кода используется `vnd.com.xamarin.sample.Vegetables`.


### <a name="data-model-metadata"></a>Метаданные модели данных

Потребитель нужен для построения запросов Uri для доступа к другим типам данных. Базовый Uri могут быть развернуты для обращения к определенной таблице данных и может включать параметры для фильтрации результатов. Столбцы и предложения, используемые с результирующий курсор для отображения данных также должны быть объявлены.

Чтобы убедиться, что создаются только допустимые запросы Uri, обычно предоставляют допустимые строки как постоянные значения. Это упрощает доступ `ContentProvider` , так как он становятся доступными через дополнение кода значения и предотвращает опечаток в строках.

В предыдущем примере `android.provider.ContactsContract` класс представлен метаданные для данных контактов. Для наших пользовательских `ContentProvider` мы просто будет предоставлять константы в самом классе.


## <a name="implementation"></a>Реализация

Существует три действия для создания и использования пользовательского `ContentProvider`:

1. **Создать класс базы данных** &ndash; реализуйте `SQLiteOpenHelper`.

2. **Создание `ContentProvider` класса** &ndash; реализуйте `ContentProvider` с экземпляром базы данных, метаданные в виде значения констант и методов доступа к данным.

3. **Доступ `ContentProvider` через URI-адрес** &ndash; заполнить `CursorAdapter` с помощью `ContentProvider`, доступ к которым осуществляется через URI-адрес.

Как уже упоминалось, `ContentProviders` может использоваться из приложений, отличный от которой они определены. В этом примере данные используются в одном приложении, но имейте в виду, что другие приложения смогут получать доступ при условии, что они знают Uri и сведения о схеме (которая обычно предоставляется в качестве значения констант).


## <a name="create-a-database"></a>Создание базы данных

Большинство `ContentProvider` реализации будет основываться на `SQLite` базы данных. Пример кода базы данных в **SimpleContentProvider/VegetableDatabase.cs** создает очень простой базе данных двух столбцов, как показано:

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

Сама реализация базы данных не требуется внимание должен быть предоставлен с `ContentProvider`, однако если требуется привязать `ContentProvider's` данные `ListView` Управление затем уникальный целочисленный столбец с именем `_id` должны быть частью результирующий набор. В разделе [представлениях ListView и адаптеры](~/android/user-interface/layouts/list-view/index.md) документа Дополнительные сведения об использовании `ListView` элемента управления.


## <a name="create-the-contentprovider"></a>Создайте поставщик содержимого

В оставшейся части этого раздела содержит пошаговые инструкции как **SimpleContentProvider/VegetableProvider.cs** был построен пример класса.


### <a name="initialize-the-database"></a>Инициализация базы данных

Первым шагом является подклассом `ContentProvider` и добавить базу данных, который будет использоваться.

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

Остальная часть кода сформирует реализацию поставщика фактического содержимого, которая позволяет обнаружить и запрашивать данные.



## <a name="add-metadata-for-consumers"></a>Добавление метаданных для потребителей

Существует четыре типа метаданных, которые мы собираемся предоставляли `ContentProvider` класса. Только центр сертификации является обязательным, остальные выполняются по соглашению.

- **Центр** &ndash; `ContentProvider` атрибута *должен* добавляются к классу, таким образом, чтобы она зарегистрирована на Android при установке приложения.

- **URI** &ndash; `CONTENT_URI` предоставляется как константа, который позволяет легко использовать в коде. Должно соответствовать центр сертификации, но включать схему и пути к базовой папке.

- **Типы MIME** &ndash; списки результаты и один рассматриваются как разные типы содержимого, поэтому определим два типа MIME для представления их.

- **InterfaceConsts** &ndash; предоставляют постоянное значение для каждого имени столбца данных, чтобы код можно легко найти и обращаться к ним без риска опечатки.

Этот код показывает, как реализуется каждый из этих элементов, добавив к определению базы данных из предыдущего шага:

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


## <a name="implement-the-uri-parsing-helper"></a>Реализация синтаксического анализа вспомогательный URI

Поскольку код использует URI для запросов из `ContentProvider`, нам нужно действовать может проанализировать эти запросы, чтобы определить, какие данные следует вернуть. `UriMatcher` Класс может помочь при синтаксическом анализе URI, когда он был инициализирован с Uri шаблоны, которые `ContentProvider` поддерживает.

`UriMatcher` В примере будет инициализирован двух URI:

1. *«com.xamarin.sample.VegetableProvider/vegetables»* &ndash; запрос, чтобы получить полный список овощей.

2. *«com.xamarin.sample.VegetableProvider/vegetables/\#»* &ndash; где \# — это числовой параметр ( `_id` строки в базе данных). Местозаполнитель звездочка (»\*«) также можно использовать для сопоставления параметра text.

В коде мы используем константы для ссылки на метаданные значения, такие как центр и БАЗОВЫМ\_пути. Коды возврата будет использоваться в методы, выполняющие синтаксический анализ, чтобы определить, какие данные следует возвращать Uri.

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

Этот код закрыта все `ContentProvider` класса. Ссылаться на [UriMatcher документации Google](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/) для получения дополнительных сведений.


## <a name="implement-the-querymethod"></a>Реализуйте QueryMethod

Наиболее простым `ContentProvider` — метод, чтобы реализовать `Query` метод. Реализация ниже, используется `UriMatcher` выполнить синтаксический анализ `uri` параметра и вызовите метод нужную базу данных. Если `uri` содержит идентификатор параметр разбивается целое число (с помощью `LastPathSegment`) и используется в запросе к базе данных.

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

`GetType` Метод также должен быть переопределен. Этот метод может вызываться, чтобы определить тип содержимого, будут возвращены для заданного Uri.
Это определит, что клиентское приложение способ обработки данных.

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


## <a name="implement-the-other-overrides"></a>Реализуйте другие переопределения

Наш простой пример не допускает редактирование или удаление данных, но должны быть реализованы методы Insert, Update и Delete таким образом, добавьте их без реализации:

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

На этом завершается базовый `ContentProvider` реализации. После установки приложения, он предоставляет данные будут доступны в приложении, но для любого приложения, который знает Uri для ссылки на него.


## <a name="access-the-contentprovider"></a>Доступ к поставщик содержимого

Один раз `VegetableProvider` был реализован, доступ к нему осуществляется так же, как поставщик контактов в начале этого документа: получение курсора, используя указанный универсальный код ресурса, а затем использовать адаптер для доступа к данным.


## <a name="bind-a-listview-to-a-contentprovider"></a>Привязка к поставщик содержимого ListView

Для заполнения `ListView` с данными, мы используем Uri, который соответствует списку нефильтрованные овощей. В коде используется значение константы `VegetableProvider.CONTENT_URI`, который мы знаем, что разрешается `com.xamarin.sample.vegetableprovider/vegetables`. Наш `VegetableProvider.Query` реализация возвращает курсор, который можно привязать к `ListView`.

Код в `SimpleContentProvider/HomeScreen.cs` показано, как простой для отображения данных из `ContentProvider`:

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

[![Снимок экрана со списком Овощей, фруктов, со звукоизоляцией цветов, Legumes, лампочки, Tubers приложения](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Получить один элемент из поставщик содержимого

Клиентское приложение также может потребоваться доступ к одной строки данных, это можно сделать, создав другой идентификатор Uri, который ссылается на определенные строки (например).

Используйте `ContentResolver` непосредственно к одного элемента, путем построения Uri с необходимыми `Id`.

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
