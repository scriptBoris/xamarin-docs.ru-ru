---
title: Использование ListView в Xamarin.Android
description: ListView является достаточно важным элементом пользовательского интерфейса приложений Android; он используется везде из кратких списков из меню параметров для длинные списки контактов или "Избранное" internet. Он предоставляет простой способ для представления прокручиваемого списка строк, которые могут быть в формате с помощью встроенного стиля или настроить разными способами.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a30256722647bbea482970d0c4a751954810d99e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122651"
---
# <a name="listview"></a>ListView

_ListView является достаточно важным элементом пользовательского интерфейса приложений Android; он используется везде из кратких списков из меню параметров для длинные списки контактов или "Избранное" internet. Он предоставляет простой способ для представления прокручиваемого списка строк, которые могут быть в формате с помощью встроенного стиля или настроить разными способами._


## <a name="overview"></a>Обзор

Представления списка и адаптеры включены в наиболее основные стандартные блоки приложений Android. `ListView` Класс предоставляет гибкий способ представления данных, будь то контекстного меню или прокручиваемого списка. Он предоставляет удобные функции например прокрутки, индексирует и выбора одного или нескольких для построения мобильной пользовательские интерфейсы для приложений. Экземпляру `ListView` требуется *адаптер*, позволяющий заполнять его данными из представлений строк.

В этом руководстве объясняется, как реализовать `ListView` и различных `Adapter` классы в Xamarin.Android. Он также демонстрирует способы настройки внешнего вида `ListView`, и он обсуждается Важность повторного использования строки для снижения потребления памяти. Есть также некоторые обсуждение влияние на жизненный цикл действия `ListView` и `Adapter` использовать. Если вы работаете над кросс платформенных приложений с помощью Xamarin.iOS, `ListView` управления структурно аналогичные iOS `UITableView` (и Android `Adapter` аналогичен `UITableViewSource`).

Во-первых, представляет краткий учебник `ListView` с примера базовый код. Далее приведены ссылки на дополнительные разделы по использованию `ListView` в реальных приложениях.


> [!NOTE]
> `RecyclerView` Мини-приложение представляет собой более сложных и гибкую версию `ListView`. Так как `RecyclerView` должна быть преемником `ListView` (и `GridView`), мы рекомендуем использовать `RecyclerView` вместо `ListView` для разработки новых приложений. Дополнительные сведения см. в разделе [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Учебник по ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) — [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
Создает список элементов для прокрутки. Элементы списка автоматически вставляются в список с помощью [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

В этом руководстве вы создадите прокручиваемый список названия стран, которые считываются из массива строк. При выборе элемента списка, появляется всплывающее сообщение позицию элемента в списке.


Создайте новый проект с именем **HelloListView**.

Создание XML-файл с именем **list_item.xml** и сохраните его в **ресурсы/макета/** папки. Вставьте следующее:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Этот файл определяет макет для каждого элемента, который будет помещен в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Откройте `MainActivity.cs` и измените класс для расширения [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (а не [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

Вставьте следующий код для [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
метод:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Обратите внимание на то, что это не загружает файл макета для действия (это обычно делается с [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Вместо этого параметр [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
автоматически добавляет свойство [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
во весь экран из [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Этот метод принимает [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), который управляет массив элементов списка, которые будут помещены в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
В [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
Конструктор принимает приложения [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), описание макета для каждого элемента списка (созданный на предыдущем шаге) и `T[]` или [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
Массив объектов для вставки в [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
(определение приводится ниже.)

В [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
Фильтрация для текста включает свойство [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), таким образом, когда пользователь начинает ввод, список будет отфильтрован.

В [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
событие можно подписаться обработчики для щелчков мышью. Когда элемент в [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
является нажатии обработчик вызывается и [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
отображается сообщение, на основе текста щелкнутого элемента.

Можно использовать схемы элемента списка, предоставляемых платформой вместо определения свой собственный файл макета для [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Например, попробуйте использовать `Android.Resource.Layout.SimpleListItem1` вместо `Resource.Layout.list_item`.

Добавьте следующий `using` инструкции:

```csharp
using System;
```
Добавьте следующий массив строк как член `MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Это массив строк, которые будут помещены в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Запустите приложение. Можно в списке или ввести отфильтровать их, затем щелкните элемент, чтобы просмотреть сообщение. Результат должен быть примерно таким:

[![Пример снимка экрана с ListView с названия стран](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Обратите внимание, что с помощью жестко строковый массив не является рекомендуемым способом разработки. Один используется в этом руководстве для простоты для демонстрации [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
мини-приложение. Наилучшим решением будет ссылаться на массив строк, определенного внешнего ресурса, например со `string-array` ресурсов в проекте **Resources/Values/Strings.xml** файла. Пример:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

Для использования этих ресурсов строк для [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), замены исходного [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
Строка со следующими:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Запустите приложение. Результат должен быть примерно таким:

[![Пример снимка экрана с ListView с помощью меньшего размера список имен](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Продвигаясь дальше с ListView

Остальные разделы (ссылка ниже) воспользоваться исчерпывающие работа с `ListView` класс и различные типы типы адаптеров, которые можно использовать с ним. Эта структура выглядит следующим образом:

-   **Внешний вид** &ndash; части `ListView` управления и как они работают.

-   **Классы** &ndash; Общие сведения о классах, используемый для отображения `ListView`.

-   **Отображение данных в ListView** &ndash; как для отображения простой список данных, как реализовать `ListView's` удобные функции; как использовать различные встроенные строки макеты; и как адаптеры освободить память, повторное использование представлений строк.

-   **Пользовательское оформление** &ndash; изменять стиль `ListView` пользовательские макеты, шрифты и цвета.

-   **С помощью SQLite** &ndash; способ отображения данных из базы данных SQLite с `CursorAdapter`.

-   **Жизненный цикл действия** &ndash; рекомендации по проектированию при реализации `ListView` действий, в том числе, где в жизненном цикле необходимо заполнить данные и когда следует освободить ресурсы.

Обсуждение (разбить на части шесть) начинается с обзором `ListView` сам по себе класс перед тем как представить все более сложные примеры его использования.

-   [Компоненты ListView и функции](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Заполнение ListView данными](~/android/user-interface/layouts/list-view/populating.md)
-   [Настройка вида ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Использование CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Использование ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView и жизненный цикл действия](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Сводка

Этот набор разделов появился `ListView` и приведены некоторые примеры того, как использовать встроенные функции `ListActivity`. Это обсуждалось пользовательские реализации `ListView` , отводимое цветных макеты и использует базу данных SQLite и кратко рассмотрели релевантность жизненный цикл действия вашей `ListView` реализации.


## <a name="related-links"></a>Связанные ссылки

- [AccessoryViews (пример)](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid (пример)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (пример)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews (пример)](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView (пример)](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll (пример)](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex (пример)](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter (пример)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (пример)](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [Учебник по жизненному циклу действия](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Работа с таблицами и ячейками (в Xamarin.iOS)](~/ios/user-interface/controls/tables/index.md)
- [Ссылки на класс ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Ссылки на класс ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Ссылки на класс BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Ссылки на класс ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Ссылки на класс CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
