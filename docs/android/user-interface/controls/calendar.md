---
title: Календарь
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d48af16f50c5a4482342d323b5c73b9c237cc83a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767980"
---
# <a name="calendar"></a>Календарь


## <a name="calendar-api"></a>Календарь API

Новый набор API-интерфейсы, представленные в Android 4 календаря поддерживает приложения, предназначенные для чтения или записи данных в поставщике календаря. Эти API-интерфейсы поддерживают широкий набор параметров взаимодействия с данными календаря, включая возможность считывать и записывать события, участники и напоминания. С помощью календаря поставщика приложения, данные, которые добавляются с помощью API-интерфейса будет отображаться в приложении встроенный календарь, входящий в состав Android 4.


## <a name="adding-permissions"></a>Добавление разрешений

При работе с новым календарем API-интерфейсы в приложении, первое, что нужно сделать является добавить необходимые разрешения для манифеста Android. Требуются разрешения, необходимо добавить `android.permisson.READ_CALENDAR` и `android.permission.WRITE_CALENDAR`, в зависимости от того чтение и запись данных календаря.


## <a name="using-the-calendar-contract"></a>С помощью контракта календаря

После настройки разрешений можно взаимодействовать с данными календаря, с помощью `CalendarContract` класса. Этот класс предоставляет модель данных, которые используются приложениями, когда они взаимодействуют с поставщиком календаря. `CalendarContract` Позволяет приложениям для разрешения URL-адреса в календаре сущности, такие как календари и события. Он также предоставляет возможность взаимодействия с различными полями в каждой сущности, такие как имя и идентификатор, или события начала и дату окончания календаря.

Давайте рассмотрим пример, использующий API календаря. В этом примере мы рассмотрим, как перечислить календари и связанные события, а также способы добавления нового события календаря.


## <a name="listing-calendars"></a>Список календарей

Во-первых давайте рассмотрим, как перечислить календари, которые были зарегистрированы в приложении "Календарь". Чтобы сделать это, можно создать `CursorLoader`. Появился в Android 3.0 (API 11), `CursorLoader` является альтернативный способ использования `ContentProvider`. Как минимум нам нужно будет указать Uri содержимого для календарей и столбцы, которые необходимо вернуть; Спецификация этот столбец называется _проекции_.

Вызов `CursorLoader.LoadInBackground` метод позволяет запросить содержимого поставщик данных, таких как поставщик календаря.
`LoadInBackground` Выполняет операцию реальную нагрузку и возвращает `Cursor` с результатами запроса.

`CalendarContract` Помогает нам при указании обоих содержимое `Uri` и проекции. Для получения содержимого `Uri` для выполнения запросов к календарям, можно просто использовать `CalendarContract.Calendars.ContentUri` свойства следующим образом:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

С помощью `CalendarContract` для указания, какой календарь столбцы, мы хотим выполняется так же просто. Мы просто добавьте поля в `CalendarContract.Calendars.InterfaceConsts` класса в массив. Например, следующий код включает идентификатор календаря, отображаемое имя и имя учетной записи:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id` Следует включать, если вы используете `SimpleCursorAdapter` для привязки данных к пользовательскому Интерфейсу, как мы увидим в ближайшее время. С помощью содержимого Uri и проекции, мы создаем экземпляр `CursorLoader` и вызвать `CursorLoader.LoadInBackground` метод для возврата курсора с данными календаря, как показано ниже:

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

Кроме того нам нужно указать пользовательский Интерфейс для каждого элемента списка, который мы поместить в отдельный файл XML следующим образом:

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

С этого момента это обычные Android код для привязки данных от положения курсора к пользовательскому Интерфейсу. Мы будем использовать `SimpleCursorAdapter` следующим образом:

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

В приведенном выше коде адаптера принимает столбцов, указанных в `sourceColumns` массива и записывает их в элементы пользовательского интерфейса в `targetResources` массива для каждой записи календаря в курсоре. Действие, используемое здесь является подклассом `ListActivity`; она включает `ListAdapter` свойство, к которому мы настроить адаптер.

Ниже приведен снимок конечный результат с данными календаря, отображаемых в `ListView`:

[![CalendarDemo, работающее в эмуляторе, отображении двух записей календаря](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>Список событий календаря

Далее рассмотрим, как перечислить события для заданного календаря.
Построенные на приведенном выше примере, будет представлен список событий, когда пользователь выбирает одну из календарей. Таким образом нам нужно будет обрабатывать Выбор элемента в предыдущем примере кода:

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

В этом коде мы создаем намерение откройте действие типа `EventListActivity`, передавая идентификатор календаря в назначение. Необходимо ввести код, чтобы знать, какой календарь для запроса событий. В `EventListActivity` `OnCreate` метод, мы можем извлечь идентификатор из `Intent` как показано ниже:

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

Теперь давайте события запросов для этого календаря идентификатор. Для запроса событий выполняется аналогично тому, как запросить список календарей в более ранних версиях этого времени, которые мы будем работать с `CalendarContract.Events` класса. В следующем коде создается запрос для получения событий:

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

В этом коде мы сначала нужно получить содержимое `Uri` для событий из `CalendarContract.Events.ContentUri` свойство. Затем мы указать столбцы событий, которые нужно извлечь в массиве eventsProjection.
Наконец, мы создаем экземпляр `CursorLoader` с этой информацией и вызова загрузчик `LoadInBackground` метод для возврата `Cursor` с данными событий.

Чтобы отобразить данные события в пользовательском Интерфейсе, мы используем разметку и код, как и раньше для отображения списка календарей. Опять же, мы используем `SimpleCursorAdapter` для привязки данных к `ListView` как показано в следующем коде:

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

Основное различие между этим кодом и код, который использовался ранее для отображения списка календарь является использование класса `ViewBinder`, который установлен в строке:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder` Позволяет последующего управления как значения привязки к представлениям. В этом случае мы используем его для преобразования времени начала события из миллисекунд в строку даты, как показано в следующей реализации:

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

Отобразится список событий, как показано ниже:

[![Снимок экрана примера приложения, отображение три события календаря](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>Добавление события календаря

Мы рассмотрели для чтения данных календаря. Теперь давайте посмотрим, как добавить события в календаре. Чтобы это работало, не забудьте включить `android.permission.WRITE_CALENDAR` разрешение упоминалось ранее. Чтобы добавить события в календаре, произойдет следующее.

1.  Создание `ContentValues` экземпляра.
1.  С помощью клавиш из `CalendarContract.Events.InterfaceConsts` класса для заполнения `ContentValues` экземпляра.
1.  Задать часовых поясов для события начала и окончания раз.
1.  Используйте `ContentResolver` для вставки данных события в календаре.


Приведенный ниже код иллюстрирует следующие действия:

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

Обратите внимание, что если мы не устанавливаем часового пояса, возникнет исключение типа `Java.Lang.IllegalArgumentException` будет создано. Так как значения времени событий должны быть выражены в миллисекундах с начала эпохи, мы создадим `GetDateTimeMS` метода (в `EventListActivity`) для преобразования нашей спецификации даты в формат миллисекунды:

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

Если добавить кнопку к списку событий пользовательского интерфейса и выполнить приведенный выше код обработчика события щелчка кнопки, событие добавляется к календарю и изменить в наш список, как показано ниже:

[![Снимок экрана примера приложения с следуют кнопки Добавить событие выборки событий календаря](calendar-images/13.png)](calendar-images/13.png#lightbox)

Если мы откройте приложение "Календарь", затем будет видно, что событие записывается существует также:

[![Снимок экрана: Отображение выбранного события календаря приложения календаря](calendar-images/14.png)](calendar-images/14.png#lightbox)

Как видите, Android доступ эффективна и проста для получения и сохранения данных календаря, позволяя приложениям легко интегрировать возможности календаря.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация календаря (пример)](https://developer.xamarin.com/samples/CalendarDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
