---
title: Календарь
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d3cba67f27d5b7de7bdcbfa5439061067f759890
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112537"
---
# <a name="calendar"></a>Календарь


## <a name="calendar-api"></a>Календарь API

Новый набор календаря API-интерфейсы, представленные в Android 4 поддерживает приложения, предназначенные для чтения или записи данных к поставщику календаря. Эти API-интерфейсы поддерживают множество возможностей взаимодействия с данными календаря, включая возможность чтения и записи событий, участников и напоминания. С помощью поставщика календаря в приложении, данные, добавляемые через API-Интерфейс будет отображаться в приложении встроенный календарь, который поставляется с Android 4.


## <a name="adding-permissions"></a>Добавление разрешений

При работе с API-интерфейсы нового календаря в приложении, первое, что необходимо сделать — это добавить соответствующие разрешения в манифест Android. Разрешения, необходимо добавить, `android.permisson.READ_CALENDAR` и `android.permission.WRITE_CALENDAR`в зависимости от того операции чтения или записи данных календаря.


## <a name="using-the-calendar-contract"></a>С помощью контракта календаря

После настройки разрешений, возможностей взаимодействия с данными календаря с помощью `CalendarContract` класса. Этот класс предоставляет модель данных, который может использоваться приложениями, когда они взаимодействуют с поставщиком календаря. `CalendarContract` Позволяет приложениям для разрешения URI для сущности, календарь, такие как календарям и мероприятиям. Он также предоставляет возможность взаимодействовать с различными полями в каждой сущности, такие как имя и идентификатор, или события начала и дату окончания календаря.

Давайте рассмотрим пример, использующий API календаря. В этом примере мы рассмотрим, как перечислить календари и событиях, а также как добавить новое событие в календаре.


## <a name="listing-calendars"></a>Список календарей

Во-первых рассмотрим способ перечисления календари, которые были зарегистрированы в приложение «календарь». Чтобы сделать это, можно создать `CursorLoader`. Представленные в Android 3.0 (API 11), `CursorLoader` является предпочтительным способом доступа к `ContentProvider`. Как минимум нам нужно будет указать Uri содержимого для календарей и столбцы, которые мы хотим возвращать; Эта спецификация столбца называется _проекции_.

Вызов `CursorLoader.LoadInBackground` метод позволяющая запросить поставщика содержимого данных, таких как поставщик календаря.
`LoadInBackground` Выполняет операцию фактической нагрузки и возвращает `Cursor` с результатами запроса.

`CalendarContract` Помогает нам при указании обоих содержимое `Uri` и проекции. Для получения содержимого `Uri` для выполнения запросов к календарям, можно просто использовать `CalendarContract.Calendars.ContentUri` свойства следующим образом:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

С помощью `CalendarContract` для указания какой календарь столбцы, мы хотим выполняется так же просто. Мы просто добавляем поля в `CalendarContract.Calendars.InterfaceConsts` класс в массив. Например, следующий код включает идентификатор календаря, отображаемое имя и имя учетной записи:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id` Важно включить, если вы используете `SimpleCursorAdapter` для привязки данных к пользовательскому Интерфейсу, так как мы скоро увидим. С помощью содержимого Uri и проекции, мы создаем экземпляр `CursorLoader` и вызвать `CursorLoader.LoadInBackground` метод для возврата курсора с данными календаря, как показано ниже:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

Пользовательский Интерфейс для этого примера содержит `ListView`, с каждым элементом в списке, представляющий один календарь. Следующий код XML показывает разметку, включающую `ListView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

Кроме того мы должны указать пользовательский Интерфейс для каждого элемента списка, который мы поместить в отдельный файл XML, как показано ниже:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

С этого момента это просто обычный код для Android для привязки данных из курсора в пользовательский интерфейс. Мы будем использовать `SimpleCursorAdapter` следующим образом:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

В приведенном выше коде, адаптер принимает столбцов, указанных в `sourceColumns` массива и записывает их в элементы пользовательского интерфейса в `targetResources` массива для каждой записи календаря в курсоре. Действия, используемый здесь является подклассом `ListActivity`; он включает `ListAdapter` свойство, к которому мы настроить адаптер.

Ниже приведен снимок экрана, показывающий конечным результатом, с данными календаря в `ListView`:

[![CalendarDemo, работающее в эмуляторе, отображение двух записей календаря](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Список событий календаря

Далее давайте рассмотрим, как перечислить события для заданного календаря.
Основываясь на приведенном выше примере, мы приведем список событий, когда пользователь выбирает один из календари. Таким образом необходимо обработать Выбор элемента в предыдущем коде:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

В этом коде мы создаем объекта Intent, чтобы открыть действия типа `EventListActivity`, передав идентификатор календаря в назначение. Нам понадобится идентификатор знать, какой календарь для запроса событий. В `EventListActivity` `OnCreate` метод, мы можем извлечь идентификатор из `Intent` как показано ниже:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Теперь давайте события запросов для данного календаря идентификатор. Аналогично тому, как мы запросили список календарей в более ранних версиях процесс для запроса событий только в этом случае мы будем работать с `CalendarContract.Events` класса. Следующий код создает запрос для получения событий:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

В этом коде сначала мы получаем содержимое `Uri` для событий из `CalendarContract.Events.ContentUri` свойство. Затем мы указать столбцы событий, которые нужно извлечь в массиве eventsProjection.
Наконец, мы создаем экземпляр `CursorLoader` с этой информацией и вызова загрузчик `LoadInBackground` метод для возврата `Cursor` с данным событий.

Чтобы отобразить данные события в пользовательском Интерфейсе, можно использовать разметку и код так же, как мы делали раньше, чтобы отобразить список календарей. Опять же, мы используем `SimpleCursorAdapter` для привязки данных к `ListView` как показано в следующем коде:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

Основное различие между этим кодом и код, который мы использовали ранее, чтобы отобразить список календарей является использование `ViewBinder`, которое задается в строке:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder` Класс позволяет нам для последующего управления, как мы привязки значений к представлениям. В этом случае мы используем его для преобразования времени начала события из миллисекунд в строку даты, как показано в следующей реализации:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

Откроется список событий, как показано ниже:

[![Снимок экрана примера приложения, отображение три события календаря](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>Добавление события календаря

Мы узнали, как для чтения данных календаря. Теперь давайте посмотрим, как добавить событие в календаре. Чтобы это работало, не забудьте включить `android.permission.WRITE_CALENDAR` разрешение, мы упоминали ранее. Чтобы добавить событие в календаре, происходит следующее:

1.  Создание `ContentValues` экземпляра.
1.  Использовать ключи из `CalendarContract.Events.InterfaceConsts` класс для заполнения `ContentValues` экземпляра.
1.  Значение часового пояса для событий начала и окончания.
1.  Используйте `ContentResolver` для вставки данных события в календаре.


Эти действия показаны в следующем коде:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

Обратите внимание, что если мы не устанавливаем часового пояса, возникнет исключение типа `Java.Lang.IllegalArgumentException` будет создано. Так как значения времени событий должно быть выражено в миллисекундах с начала эпохи, мы создадим `GetDateTimeMS` метод (в `EventListActivity`) для преобразования в формат миллисекунды наших спецификации даты:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

Если мы добавляем кнопку Список событий пользовательского интерфейса и выполните приведенный выше код обработчика события щелчка кнопки, событие добавляется к календарю и обновляются в нашем списке, как показано ниже:

[![Снимок экрана: пример приложения с помощью событий календаря, следуют кнопки Добавить пример события](calendar-images/13.png)](calendar-images/13.png#lightbox)

Если мы откроем приложение «календарь», Далее будет рассмотрен, это событие записывается существует также:

[![Снимок экрана: приложение «календарь» Отображение выбранного события календаря](calendar-images/14.png)](calendar-images/14.png#lightbox)

Как вы видите, Android разрешает мощный и быстрый доступ к получать и сохранять данные календаря, благодаря которой приложения могут легко интегрировать возможности календаря.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация календаря (пример)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
