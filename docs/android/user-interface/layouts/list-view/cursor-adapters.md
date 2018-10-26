---
title: Использование CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: fbdd0f2ea000f0cf46178c615e7526bf7f210a41
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103043"
---
# <a name="using-cursoradapters"></a>Использование CursorAdapters


## <a name="overview"></a>Обзор

Android предоставляет классы адаптеров специально для того, чтобы отобразить данные из запроса базы данных SQLite.

 **SimpleCursorAdapter** – как и для `ArrayAdapter` , так как он может использоваться без создания подклассов. Просто указать необходимые параметры (например, сведения курсора и макет) в конструкторе, а затем назначить `ListView`.

 **CursorAdapter** — значения базового класса, который может наследовать от когда требуется дополнительный контроль над привязку данных к элементам управления макета (например, скрытие/отображение элементов управления или изменения их свойств).

Курсор адаптеры позволяют высокой производительности для прокрутки длинных списков данных, которые хранятся в SQLite. Код-потребитель необходимо определить SQL-запроса в `Cursor` объекта и затем описывается, как создать и заполнить представления для каждой строки.


## <a name="creating-an-sqlite-database"></a>Создание базы данных SQLite

Для демонстрации курсора адаптеров требуется простая реализация базы данных SQLite. Код в **SimpleCursorTableAdapter/VegetableDatabase.cs** содержит код и SQL, чтобы создать таблицу и заполнить ее некоторыми данными.
Полный `VegetableDatabase` класс показан здесь:

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

`VegetableDatabase` Класс будет создаваться в `OnCreate` метод `HomeScreen` действия. `SQLiteOpenHelper` Базовый класс управляет установки файла базы данных и гарантирует, что SQL в его `OnCreate` метод выполняется только один раз. Этот класс используется в следующих двух примерах для `SimpleCursorAdapter` и `CursorAdapter`.

Запрос курсора *необходимо* работаете с целочисленным столбцом `_id` для `CursorAdapter` для работы. Если базовая таблица не имеет целочисленного столбца с именем `_id` затем использовать псевдоним столбца для другое уникальное целое число в `RawQuery` , составляющие курсор. Ссылаться на [Android документация](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) для получения дополнительных сведений.


### <a name="creating-the-cursor"></a>Создание курсора

В примерах используется `RawQuery` для включения SQL-запроса в `Cursor` объекта. Список столбцов, который возвращается из курсора определяет столбцы данных, доступные для отображения в адаптере курсора. Код, который создает базу данных в **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` метода показан здесь:

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Любой код, который вызывает `StartManagingCursor` вызывать `StopManagingCursor`. В примерах используется `OnCreate` для запуска, и `OnDestroy` для закрытия курсора. `OnDestroy` Метод содержит следующий код:

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Когда приложение будет в базу данных SQLite и создал объект курсора, как показано, он может использовать либо `SimpleCursorAdapter` или подкласс `CusorAdapter` для отображения строк в `ListView`.


## <a name="using-simplecursoradapter"></a>С помощью SimpleCursorAdapter

`SimpleCursorAdapter` Подобно `ArrayAdapter`, но специализированного, для использования с SQLite. Он не требуется создание подклассов — просто установить некоторые простые параметры, при создании объекта и затем назначить его `ListView`в `Adapter` свойство.

Используются следующие параметры для конструктора SimpleCursorAdapter.

 **Контекст** — ссылку на содержащего их действия.

 **Макет** — идентификатор ресурса представления строки для использования.

 **ICursor** — курсора, содержащего запрос SQLite для отображения данных.

 **Из** массив строк — массив строк, соответствующие именам столбцов в курсоре.

 **Чтобы** массив целых чисел — массив идентификаторов макета, которые соответствуют элементам управления, в макете строки. Значение столбца, указанного в `from` массив будет привязан к ControlID, указанные в этом массиве, в тот же индекс.

`from` И `to` массивы должны иметь одинаковое количество записей, поскольку они образуют сопоставление источника данных для элементов управления макетом в представлении.

**SimpleCursorTableAdapter/HomeScreen.cs** пример кода провода вверх `SimpleCursorAdapter` следующим образом:

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` — это быстрый и простой способ отображения данных SQLite в `ListView`. Основным ограничением является, что он может только привязать значения столбцов для отображения элементов управления, он не позволяет изменить другие характеристики макет строк (например, отображение/скрытие элементов управления или изменение свойств).


## <a name="subclassing-cursoradapter"></a>Создание подкласса CursorAdapter

Объект `CursorAdapter` подкласс имеет те же преимущества производительности, что `SimpleCursorAdapter` для отображения данных из SQLite, но он также предоставляет полный контроль над созданием и макет каждого представления строки. `CursorAdapter` Реализации очень отличается от создания подклассов `BaseAdapter` , так как он не переопределяет `GetView`, `GetItemId`, `Count` или `this[]` индексатора.

Учитывая рабочее SQLite базы данных, требуется только переопределить два метода для создания `CursorAdapter` подкласс:

- **BindView** — учитывая представления, обновить его для отображения данных в предоставленный курсор.

- **NewView** — вызывается, когда `ListView` требуется новое представление для отображения. `CursorAdapter` Позаботится о повторное использование представлений (в отличие от `GetView` метод регулярных адаптеров).

Подклассы адаптера в предыдущих примерах имеет методы для возврата количества строк и для извлечения текущего элемента — `CursorAdapter` этих методов не требуется, так как эти сведения можно получить из сам курсор. Путем разделения Создание и заполнение каждого представления в этих двух методов `CursorAdapter` обеспечивает повторное использование представления. Это вступает в противоречие с регулярных адаптера можно игнорировать `convertView` параметр `BaseAdapter.GetView` метод.


### <a name="implementing-the-cursoradapter"></a>Реализация CursorAdapter

Код в **CursorTableAdapter/HomeScreenCursorAdapter.cs** содержит `CursorAdapter` подкласс. Он хранит ссылку контекста, переданного в конструктор, позволяя получать доступ к `LayoutInflater` в `NewView` метод. Завершенный класс выглядит следующим образом:

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

В `Activity` , будет отображаться `ListView`, создает курсор и `CursorAdapter` затем назначить его к представлению списка.

Код, который выполняет это действие в **CursorTableAdapter/HomeScreen.cs** `OnCreate` метода показан здесь:

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy` Содержит метод `StopManagingCursor` вызов метода, описанных ранее.



## <a name="related-links"></a>Связанные ссылки

- [SimpleCursorTableAdapter (пример)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (пример)](https://developer.xamarin.com/samples/CursorTableAdapter/)
