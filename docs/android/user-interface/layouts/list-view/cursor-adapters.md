---
title: С помощью CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 20311cc50c87638391d8b078c405bc61baceb373
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766667"
---
# <a name="using-cursoradapters"></a>С помощью CursorAdapters


## <a name="overview"></a>Обзор

Android предоставляет классы адаптеров специально для отображения данных из запроса базы данных SQLite.

 **SimpleCursorAdapter** – как и для `ArrayAdapter` , так как он может использоваться без создания подкласса. Просто укажите необходимые параметры (например, курсор и сведения о макете) в конструкторе, а затем назначьте `ListView`.

 **CursorAdapter** — значения базового класса, который может наследовать от когда требуется дополнительный контроль над привязку данных к элементам (например, скрытие и отображение элементов управления или изменения их свойств).

Курсор адаптеры позволяют высокой производительности для прокрутки в длинных списках данные, хранящиеся в SQLite. Код-потребитель должен определить SQL-запроса в `Cursor` объекта и затем описано, как создать и заполнить представлений для каждой строки.


## <a name="creating-an-sqlite-database"></a>Создание базы данных SQLite

Для демонстрации курсора адаптеров требуется простая реализация базы данных SQLite. Код в **SimpleCursorTableAdapter/VegetableDatabase.cs** содержит код и SQL, чтобы создать таблицу и заполнить некоторые данные.
Полный `VegetableDatabase` класса приведен ниже:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
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
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

`VegetableDatabase` Создавать экземпляры класса в `OnCreate` метод `HomeScreen` действия. `SQLiteOpenHelper` Базовый класс управляет установки файла базы данных и гарантирует, что SQL в его `OnCreate` метод выполняется только один раз. Этот класс используется в следующих двух примерах для `SimpleCursorAdapter` и `CursorAdapter`.

Запрос курсора *должен* имеют целочисленного столбца `_id` для `CursorAdapter` для работы. Если базовая таблица не имеет целочисленного столбца с именем `_id` затем использовать псевдоним столбца для другой уникальный целочисленный в `RawQuery` , составляющие курсор. Ссылаться на [Android docs](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) для получения дополнительных сведений.


### <a name="creating-the-cursor"></a>Создание курсора

В примерах используется `RawQuery` для включения SQL-запроса в `Cursor` объекта. Список столбцов, которое возвращается из курсора определяет столбцы данных, которые доступны для отображения в адаптере курсора. Код, который создает базу данных в **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` ниже показан метод:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Любой код, который вызывает `StartManagingCursor` должен также вызывать `StopManagingCursor`. В примерах используется `OnCreate` для запуска, и `OnDestroy` для закрытия курсора. `OnDestroy` Метод содержит этот код:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Как только приложение имеет в базу данных SQLite и создал объект курсора, как показано, он может использовать либо `SimpleCursorAdapter` или подкласс `CusorAdapter` для отображения строк в `ListView`.


## <a name="using-simplecursoradapter"></a>С помощью SimpleCursorAdapter

`SimpleCursorAdapter` Подобно `ArrayAdapter`, но специализированного для использования с SQLite. Он не требует создания подкласса — просто установить некоторые простые параметры, при создании объекта и затем назначьте его `ListView`в `Adapter` свойство.

Используются следующие параметры для конструктора SimpleCursorAdapter.

 **Контекст** — ссылку на включающего действия.

 **Макет** — это идентификатор ресурса представление строки для использования.

 **ICursor** — курсора, содержащего запрос SQLite для отображения данных.

 **Из** массив строк — массив строк, соответствующие именам столбцов в курсоре.

 **Чтобы** массив целых чисел — массив идентификаторов макета, которые соответствуют элементам управления в макете строки. Значение столбца, указанного в `from` массив будет привязан к ControlID, заданные в данном массиве, с таким же индексом.

`from` И `to` массивы должны иметь одинаковое число записей, поскольку они образуют сопоставление источника данных для элементов управления, макет в представлении.

**SimpleCursorTableAdapter/HomeScreen.cs** образец кода проводов вверх `SimpleCursorAdapter` следующим образом:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` — быстрый и простой способ отображения данных SQLite в `ListView`. Основным ограничением является то, можно привязать только значения столбцов для отображения элементов управления, он не позволяет изменить другие аспекты макет строки (например, отображение и скрытие элементов управления или изменение свойств).


## <a name="subclassing-cursoradapter"></a>Создание подклассов CursorAdapter

Объект `CursorAdapter` подкласс имеет те же преимущества производительности как `SimpleCursorAdapter` для отображения данных из SQLite, но он также предоставляет полный контроль над созданием и макет каждого представления строки. `CursorAdapter` Реализацию сильно отличается от создания подкласса `BaseAdapter` , так как он не переопределяет `GetView`, `GetItemId`, `Count` или `this[]` индексатора.

Получает рабочую SQLite базы данных требуется только для переопределения двух методов для создания `CursorAdapter` подкласс:

- **BindView** — имея представления, обновите его для отображения данных в предоставленный курсора.

- **NewView** — вызывается, когда `ListView` требуется новое представление для отображения. `CursorAdapter` Берет на себя повторное использование представления (в отличие от `GetView` метод регулярного адаптеров).

Подклассы адаптера в предыдущих примерах имеет методы для возврата числа строк, а также для извлечения текущего элемента — `CursorAdapter` этих методов не требуется, так как эти сведения могут быть достигнутой с сам курсор. Путем разделения Создание и заполнение каждого представления в этих двух методов `CursorAdapter` обеспечивает представление повторного использования. Это в отличие от обычных адаптеру там, где это возможно игнорировать `convertView` параметр `BaseAdapter.GetView` метода.


### <a name="implementing-the-cursoradapter"></a>Реализация CursorAdapter

Код в **CursorTableAdapter/HomeScreenCursorAdapter.cs** содержит `CursorAdapter` подкласс. Он содержит ссылку контекста, переданные в конструктор, чтобы получить доступ к `LayoutInflater` в `NewView` метод. Полного класса выглядит следующим образом:

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>Назначение CursorAdapter

В `Activity` , будет отображаться `ListView`, создает курсор и `CursorAdapter` назначить его к представлению списка.

Код, который выполняет это действие в **CursorTableAdapter/HomeScreen.cs** `OnCreate` ниже показан метод:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy` Содержит метод `StopManagingCursor` вызова метода, описанных выше.



## <a name="related-links"></a>Связанные ссылки

- [SimpleCursorTableAdapter (пример)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (пример)](https://developer.xamarin.com/samples/CursorTableAdapter/)
