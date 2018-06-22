---
title: Использование в Xamarin.Android ListView
description: ListView является важным компонентом пользовательского интерфейса приложений Android; он используется везде из коротких списков параметров меню в длинных списках контакты и Избранное. Он предоставляет простой способ представления прокрутки списка строк, которые может быть отформатирован с помощью встроенного стиля или настроить активно.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: c5c8222df16b265f3b44f3ee3fcd9053d827cf6a
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32437107"
---
# <a name="listview"></a>ListView

_ListView является важным компонентом пользовательского интерфейса приложений Android; он используется везде из коротких списков параметров меню в длинных списках контакты и Избранное. Он предоставляет простой способ представления прокрутки списка строк, которые может быть отформатирован с помощью встроенного стиля или настроить активно._


## <a name="overview"></a>Обзор

Представления списка и адаптеры включены в наиболее основные стандартные блоки приложений Android. `ListView` Класс предоставляет гибкий способ представления данных, будь то контекстного меню или прокручиваемого списка. Он обеспечивает удобство использования таких функций, как быстро прокрутки, индексов и выбора одного или нескольких помогут вам создавать мобильные пользовательские интерфейсы для приложений. Экземпляру `ListView` требуется *адаптер*, позволяющий заполнять его данными из представлений строк.

В этом руководстве объясняется, как реализовать `ListView` и различных `Adapter` классы в Xamarin.Android. Он также демонстрирует способы настройки внешнего вида `ListView`, и в нем описывается Важность повторного использования строк для уменьшения объема используемой памяти. Кроме того, есть некоторые обсуждение влияние на жизненный цикл действия `ListView` и `Adapter` использовать. Если вы работаете над кросс платформенных приложений с помощью Xamarin.iOS, `ListView` управления одинаковой для iOS `UITableView` (и Android `Adapter` аналогичен `UITableViewSource`).

Во-первых, краткий учебник вводит `ListView` с примером кода. Далее представлены ссылки на более подробные статьи по использованию `ListView` в реальных приложениях.


> [!NOTE]
> `RecyclerView` Мини-приложения — это версия более расширенные и гибкий способ `ListView`. Поскольку `RecyclerView` предназначен для преемником `ListView` (и `GridView`), мы рекомендуем использовать `RecyclerView` вместо `ListView` для разработки новых приложений. Дополнительные сведения см. в разделе [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Учебник ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) — [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) , создает список элементов прокрутки. Элементы списка автоматически вставляются в список с помощью [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

В этом учебнике вы создадите прокручиваемый список названия стран, которые считываются из массива строк. При выборе элемента списка появляется всплывающее сообщение положение элемента в списке.


Создание нового проекта с именем **HelloListView**.

Создайте XML-файл с именем **list_item.xml** и сохранит его в **ресурсы и макета или** папки. Вставьте следующее:

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

Откройте `MainActivity.cs` и измените класс для расширения [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (вместо [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

Вставьте следующий код для [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) метод:

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

Обратите внимание, что это не загружает файл макета для действия (это обычно делается с [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Вместо этого параметр [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) автоматически добавляет свойство [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) весь экран [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Этот метод принимает [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), который управляет массив элементов списка, которые будут помещены в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
[ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/) Конструктор принимает приложения [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), описание макета для каждого элемента списка (созданного на предыдущем шаге) и `T[]` или [ `Java.Util.IList<T>` ](https://developer.xamarin.com/api/type/Java.Util.IList/) массив объектов для вставки в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) (определяется далее).

[ `TextFilterEnabled` ](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/) Включает свойство текста для фильтрации [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/)таким образом, когда пользователь начинает вводить, список будет отфильтрован.

[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) Событие может использоваться для подписки на обработчики, если пользователь щелкает мышью. При создании записи в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) — нажатии обработчик вызывается и [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) отображается сообщение, с помощью текста из выбранной элемента.

Можно использовать схемы элемента списка, вместо того чтобы определять свой собственный файл макета для платформы [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Например, попробуйте использовать `Android.Resource.Layout.SimpleListItem1` вместо `Resource.Layout.list_item`.

Добавьте следующие `using` инструкции:

```csharp
using System;
```
Добавьте следующий массив строк, который является членом `MainActivity`:

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

Массив строк, которые будут помещены в [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Запустите приложение. В списке или введите для фильтрации, затем щелкните элемент, чтобы просмотреть сообщение. Результат должен быть примерно таким:

[![Снимок экрана: пример элемента управления ListView с названия стран](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Обратите внимание, что использование массива жестко заданная строка не является рекомендуемым способом разработки. Один для демонстрации используется в этом учебнике для простоты [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) мини-приложения. Наилучшим решением будет ссылаться на массив строк, определяемых к внешним ресурсам, например с `string-array` ресурсов в проекте **Resources/Values/Strings.xml** файла. Пример:

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

Для использования этих ресурсов строк для [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), замените исходный [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) строки со следующими:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Запустите приложение. Результат должен быть примерно таким:

[![Снимок экрана: пример элемента управления ListView с меньшего размера список имен](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Если продолжить с ListView

Остальные разделы (связанные ниже) просмотрите полный работа с `ListView` класс и типы адаптеров, с ним можно использовать различные типы. Эта структура выглядит следующим образом:

-   **Внешний вид** &ndash; части `ListView` управления и принципы их работы.

-   **Классы** &ndash; Общие сведения о классах, используемый для отображения `ListView`.

-   **Отображение данных в ListView** &ndash; отображение простой список данных, как реализовать `ListView's` рабочих функций; как использовать другую строку встроенные макеты; и как адаптеры освободить память, повторное использование представления строк.

-   **Пользовательское оформление** &ndash; изменять стиль `ListView` пользовательские макеты, шрифты и цвета.

-   **С помощью SQLite** &ndash; способы отображения данных из базы данных SQLite с `CursorAdapter`.

-   **Жизненный цикл действия** &ndash; вопросы проектирования при реализации `ListView` действий, включая где жизненного цикла следует заполнения данными, а также когда для освобождения ресурсов.

Обсуждение (разбивается на компоненты шесть) начинается с обзором `ListView` сам класс перед переносом все более сложные примеры его использования.

-   [Компоненты ListView и функции](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Заполнение ListView с данными](~/android/user-interface/layouts/list-view/populating.md)
-   [Настройка вида ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Использование CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Использование ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView и жизненный цикл действия](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Сводка

Этот набор разделов, которые введена `ListView` и некоторые примеры того, как использовать встроенные функции, предоставляемые `ListActivity`. Это обсуждалось пользовательские реализации `ListView` , разрешенных для цветной макеты и использует базу данных SQLite; он кратко коснулись релевантность жизненный цикл действия ваш `ListView` реализации.


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
- [Ссылку на класс ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Ссылку на класс ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Ссылку на класс BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Ссылку на класс ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Ссылку на класс CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
