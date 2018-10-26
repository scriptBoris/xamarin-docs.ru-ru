---
title: Настройка внешнего вида ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: fef81fb5e5d2de79508b43a5612bf56af68d0772
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108175"
---
# <a name="customizing-a-listviews-appearance"></a>Настройка внешнего вида ListView


## <a name="overview"></a>Обзор

Макет отображаемых строк зависит от вида ListView. Чтобы изменить внешний вид `ListView`, используется структура другую строку.


## <a name="built-in-row-views"></a>Встроенные строки представления

Существуют двенадцать встроенных представлений, которые можно указать с помощью **Android.Resource.Layout**:

- **TestListItem** &ndash; однострочный текст с минимальным форматированием.

- **SimpleListItem1** &ndash; одна строка текста.

- **SimpleListItem2** &ndash; двух строк текста.

- **SimpleSelectableListItem** &ndash; одна строка текста, которая поддерживает выбор одного или нескольких элементов (добавляется в уровень API 11).

- **SimpleListItemActivated1** &ndash; аналогично SimpleListItem1, но цвет фона указывает, когда выбрана строка (добавлены в API уровня 11).

- **SimpleListItemActivated2** &ndash; аналогично SimpleListItem2, но цвет фона указывает, когда выбрана строка (добавлены в API уровня 11).

- **SimpleListItemChecked** &ndash; отображает галочки, указывающие выбора.

- **SimpleListItemMultipleChoice** &ndash; отображаются флажки, чтобы указать, которая обеспечивает выбор.

- **SimpleListItemSingleChoice** &ndash; отображает переключатели указывать выделение взаимоисключающими.

- **TwoLineListItem** &ndash; двух строк текста.

- **ActivityListItem** &ndash; однострочный текст с изображением.

- **SimpleExpandableListItem** &ndash; группирует строки по категориям и каждая группа может разворачивать и сворачивать.

Каждое представление встроенные строки имеет встроенный стиль, связанные с ней. Эти снимки экрана показано, как выглядит каждое представление.

[![Снимки экрана TestListItem, SimpleSelectableListItem, SimpleListitem1 и SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Снимки экрана SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked и SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Снимки экрана SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem и SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

**BuiltInViews/HomeScreenAdapter.cs** образец файла (в **BuiltInViews** решения) содержит код для создания экранов неразворачиваемого списка элементов. Представление установлено `GetView` метод следующим образом:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

Затем можно задать свойства представления, ссылаясь на идентификаторы стандартного элемента управления `Text1`, `Text2` и `Icon` под `Android.Resource.Id` (не задавайте свойства, представление не содержит или будет вызвано исключение):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

**BuiltInExpandableViews/ExpandableScreenAdapter.cs** образец файла (в **BuiltInViews** решения) содержит код для создания экрана SimpleExpandableListItem. Представление "Группа" задается в `GetGroupView` метод следующим образом:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

Дочернее представление задается в `GetChildView` метод следующим образом:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

Затем можно задать свойства для представления группы и дочернее представление, ссылаясь на стандарте `Text1` и `Text2` управления идентификаторами, как показано выше. На снимке экрана SimpleExpandableListItem (как показано выше) Пример представления группы одной строки (SimpleExpandableListItem1) и двух строк дочернее представление (SimpleExpandableListItem2). Кроме того можно настроить представление "Группа" для двух строк (SimpleExpandableListItem2) и дочернее представление можно настроить для одной строки (SimpleExpandableListItem1), или оба группу представления, а дочернее представление могут иметь одинаковое количество строк. 



## <a name="accessories"></a>Стандартные

Строки могут иметь стандартные добавлен справа от представления, чтобы указать состояние выбора:

- **SimpleListItemChecked** &ndash; создает список поддержкой единственного выбора с проверкой качестве индикатора.

- **SimpleListItemSingleChoice** &ndash; создает списки переключатель Тип кнопки, где возможна только один вариант.

- **SimpleListItemMultipleChoice** &ndash; создает списки типа checkbox, когда возможны несколько вариантов.

Упомянутые выше стандартные проиллюстрированы в следующих экранах, в порядке их соответствующих:

[![Снимки экрана из SimpleListItemChecked SimpleListItemSingleChoice и SimpleListItemMultipleChoice с "Стандартные"](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Для отображения одного этапа эти стандартные идентификатор ресурса требуется макет к адаптеру затем вручную настроить состояние выделения для нужные строки. Эта строка кода показано, как создать и назначить `Adapter` с помощью одного из этих макетов:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

`ListView` Сам поддерживает режимы другой вариант, независимо от периферийных устройств, для отображения. Чтобы избежать путаницы, используйте `Single` режим выбора с `SingleChoice` стандартные и `Checked` или `Multiple` режиме с `MultipleChoice` стиля. Режим выбора управляется `ChoiceMode` свойство `ListView`.


### <a name="handling-api-level"></a>Уровень обработки API

Более ранних версий Xamarin.Android реализуются в виде целого числа свойства перечисления. Последней версии был представлен соответствующие типы перечисления .NET, который значительно упрощает обнаружение возможные параметры.

В зависимости от того, какой уровень API вы ориентируетесь, `ChoiceMode` целого или перечисления. Пример файла **AccessoryViews/HomeScreen.cs** имеет блок закомментирован, если вы хотите применить Gingerbread API:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Выбор элементов программным образом

Задания вручную какие элементы «выбрано» выполняется с помощью `SetItemChecked` метод (он может быть вызван несколько раз для множественного выбора):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

Код также должен обнаруживать выделение по-разному из множественного выбора. Чтобы определить, какая строка была выбрана в `Single` использования режима `CheckedItemPosition` целочисленное свойство:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Чтобы определить, какие строки были выбраны в `Multiple` режиме, необходимо перебрать `CheckedItemPositions` `SparseBooleanArray`. Разреженный массив является как словарь, который содержит только записи где значение было изменено, поэтому необходимо перебрать весь массив ищете `true` значения, чтобы знать, что был выбран в списке как показано в следующем фрагменте кода:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Создание настраиваемой строки макетов

Четыре встроенных строки представления очень просты. Для отображения более сложные макеты (например, список сообщений электронной почты, твитов или контактная информация) представления является обязательным. Пользовательские представления обычно объявляются как файлы AXML в **ресурсы/макета** каталог и затем загрузить с помощью их идентификатор, настраиваемый адаптер ресурсов. Представление может содержать любое число классов отображения (например, TextViews, ImageViews и других элементов управления) с помощью пользовательских цветов, шрифтов и макет.

В этом примере отличается от предыдущих примерах в следующих случаях:

-  Наследует от `Activity` , а не `ListActivity` . Можно настроить для любой строки `ListView` , однако другие элементы управления также может быть включено в `Activity` макета (например, заголовок, кнопки или других элементов пользовательского интерфейса). В этом примере добавляется заголовок выше `ListView` для иллюстрации.

-  Требуется файл макета AXML для экрана; в предыдущих примерах `ListActivity` не требуется файл макета. Содержит этот AXML `ListView` управления объявления.

-  Требуется файл макета AXML для подготовки к просмотру каждой строки. Этот файл AXML содержит текстовых и графических элементов управления с помощью пользовательских шрифтов и цветов.

-  Использует файл XML необязательно настраиваемого селектора, чтобы задать внешний вид строки, когда он выбран.

-  `Adapter` Реализация возвращает пользовательского макета из `GetView` переопределить.

-  `ItemClick` должен быть объявлен по-разному (обработчик подключен к `ListView.ItemClick` вместо переопределения `OnListItemClick` в `ListActivity`).


Эти изменения описаны ниже, начиная с создания действия и представление пользовательских строк и затем охватывающий изменения адаптера и действия для их передачи.


### <a name="adding-a-listview-to-an-activity-layout"></a>Добавление разметки действия ListView

Так как `HomeScreen` больше не наследует от `ListActivity` он не имеет представление по умолчанию, поэтому для представления HomeScreen должны создаваться файл макета AXML. Например, представление будет иметь заголовок (с помощью `TextView`) и `ListView` для отображения данных. Макет определяется в **Resources/Layout/HomeScreen.axml** файла, как показано здесь:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

Преимущество использования `Activity` с помощью настраиваемого макета (а не `ListActivity`) заключается в возможности для добавления дополнительных элементов управления на экран, такие как заголовок `TextView` в этом примере.


### <a name="creating-a-custom-row-layout"></a>Создание настраиваемой строки макета

Требуется другой файл макета AXML для размещения пользовательского макета для каждой строки, которое будет отображаться в представлении списка. В этом примере эта строка будет иметь зеленым фоном, коричневый текст и изображения по правому краю. Android XML-разметку для объявления этого макета описан в **Resources/Layout/CustomView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Хотя макет пользовательских строк могут содержать множества различных элементов управления, производительность прокрутки может зависеть от сложных конструкций и использование образов (особенно если они должны быть загружена по сети). См. в статье Google Дополнительные сведения об адресации прокрутки проблем с производительностью.


### <a name="referencing-a-custom-row-view"></a>Ссылающийся на представление настраиваемой строки

В примере пользовательского адаптера реализуется в `HomeScreenAdapter.cs`. Ключевым методом является `GetView` где он загружает пользовательские AXML, используется идентификатор ресурса `Resource.Layout.CustomView`, а затем устанавливает свойства для каждого элемента управления в представлении перед возвращением. Показан класс полный адаптера:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>Ссылки на пользовательские ListView в действии

Так как `HomeScreen` класс теперь наследует от `Activity`, `ListView` поле объявляется в классе для хранения ссылки на элемент управления, объявленные в AXML:

```csharp
ListView listView;
```

Класс затем необходимо загрузить действия пользовательского макета AXML с помощью `SetContentView` метод. Его можно найти `ListView` элемента управления в макете затем создает и назначает адаптер и назначает обработчик щелчка. Код для метода OnCreate показан здесь:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Наконец `ItemClick` обработчик должен быть определен; в этом случае просто отображает `Toast` сообщение:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

Итоговый экран выглядит следующим образом:

[![Снимок экрана итоговый CustomRowView](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Настройка цвета селектор строк

При касании строки, оно должно быть выделено отзывы и предложения пользователей. При указании пользовательского представления в качестве цвета фона **CustomView.axml** ли оно также переопределяет выделения. Эта строка кода в **CustomView.axml** наборов фона на светло-зеленый, но он также означает, что нет визуального признака того, при касании строки:

```xml
android:background="#FFDAFF7F"
```

Чтобы снова включить поведение выделения, а также настроить цвет, который используется, значение атрибута background настраиваемого селектора вместо этого. Средство выбора будет объявлять как цвет фона по умолчанию так и цвет выделения. Файл **Resources/Drawable/CustomSelector.xml** содержит следующее объявление:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Чтобы сослаться на настраиваемого селектора, измените атрибут фона в **CustomView.axml** для:

```xml
android:background="@drawable/CustomSelector"
```

Выбранной строки и соответствующие `Toast` сообщение сейчас выглядит следующим образом:

[![Выбранной строки в оранжевый, с помощью всплывающее сообщение, отображение имени выбранной строки](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Предотвращение мерцание пользовательские макеты

Android пытается повысить производительность `ListView` прокрутка, кэширование сведения о макете. При наличии долго прокрутка списков данных также следует задать `android:cacheColorHint` свойство `ListView` объявление в определении действия AXML (чтобы совпадает со значением цвета фона макет пользовательских строк). Чтобы включить это указание может привести «мерцание» как пользователь перемещается по списку с цветами фона пользовательских строк.



## <a name="related-links"></a>Связанные ссылки

- [BuiltInViews (пример)](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews (пример)](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView (пример)](https://developer.xamarin.com/samples/CustomRowView/)
