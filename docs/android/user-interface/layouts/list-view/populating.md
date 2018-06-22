---
title: Заполнение ListView с данными
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 83b398faf4fd634b7d492d372524b5fdd00163d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774854"
---
# <a name="populating-a-listview-with-data"></a>Заполнение ListView с данными


## <a name="overview"></a>Обзор

Для добавления строк к `ListView` необходимо добавить его макет и реализуйте `IListAdapter` с методами, `ListView` вызовы для заполнения сам. Android включает встроенные `ListActivity` и `ArrayAdapter` классы, которые можно использовать без определения любой пользовательский макет XML или из кода. `ListActivity` Автоматически создает класс `ListView` и предоставляет `ListAdapter` свойства для предоставления представления строк для отображения через адаптер.

Встроенные адаптеры идентификатора ресурса представление принимают в качестве параметра, используемый для каждой строки. Можно использовать встроенные ресурсы, такие как в `Android.Resource.Layout` вам требуется написать собственные.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>С помощью ListActivity и ArrayAdapter&lt;строки&gt;

Пример **BasicTable/HomeScreen.cs** показано, как использовать эти классы для отображения `ListView` только несколько строк кода:

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>Обработка строк щелкает

Обычно `ListView` также позволяют пользователю touch строки для выполнения некоторых операций (например, воспроизводить песню, или вызов контакт или отображение другой экран). Ответ на изменения пользователя должен быть один дополнительные метод реализован в `ListActivity` &ndash; `OnListItemClick` &ndash; следующим образом:

[![Снимок экрана SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Теперь пользователь могут соприкасаться строки и `Toast` , появляется предупреждение:

[![Снимок экрана из всплывающих выводимое затронутых строк](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Реализация ListAdapter

`ArrayAdapter<string>` отлично подходит из-за его простоты, но он является крайне ограничены. Однако зачастую имеется коллекция бизнес-сущности, а не только строки, которые необходимо выполнить привязку.
Например если данных представляет собой набор классов сотрудника, может возникнуть списка, чтобы просто отобразить имена каждого сотрудника. Чтобы настроить поведение `ListView` для управления отображением данных необходимо реализовать подкласс `BaseAdapter` переопределение четыре следующих элементов:

-   **Число** &ndash; в элемент управления, сколько строк данных.

-   **GetView** &ndash; возвратить представление для каждой строки заполняются данными.
    Этот метод имеет параметр для `ListView` для передачи в существующий, неиспользуемые строку для повторного использования.

-   **GetItemId** &ndash; возвращают идентификатор строки (обычно строки number, хотя он может быть любое значение long, что вам нравится).

-   **[int]** индексатора &ndash; для возврата данных, связанного с номером конкретной строки.

В примере кода в **BasicTableAdapter/HomeScreenAdapter.cs** показано, как создать подкласс `BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>С помощью настраиваемого адаптера

С помощью настраиваемого адаптера похож на встроенные `ArrayAdapter`, передавая `context` и `string[]` значений для отображения:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Так как в этом примере используется тот же макет строк (`SimpleListItem1`) конечного приложения будет выглядеть так же в предыдущем примере.


### <a name="row-view-re-use"></a>Представление строк повторного использования

В этом примере существует только шесть элементов. Поскольку экрана можно разместить до восьми, нет строк повторного использования требуется. При отображении сотен или тысяч строк, однако было бы потерей памяти для создания сотен или тысяч `View` объекты, если только восемь поместиться на экране одновременно. Чтобы избежать такой ситуации, когда строки удаляются из экрана, на котором представления помещается в очередь для повторного использования. При переходе пользователя, `ListView` вызовы `GetView` для запроса нового представления для отображения &ndash; Если доступные прохождении неиспользуемого представления в `convertView` параметра. Если это значение равно null, то кода следует создавать экземпляр нового представления, в противном случае можно повторно установить свойства этого объекта и использовать его повторно.

`GetView` Метод следует использовать эту модель для повторного использования представления строк:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Реализации пользовательского адаптера должен *всегда* повторного использования `convertView` объект, прежде чем создавать новые представления, чтобы они выполняются не хватает памяти при отображении длинных списках.

Некоторые реализации адаптера (такие как `CursorAdapter`) нет `GetView` метод, вместо этого они требуют два метода `NewView` и `BindView` которого принудительно выполнить повторное использование строки, разделение обязанностей `GetView` на две методы. Отсутствует `CursorAdapter` примере позднее в этом документе.


## <a name="enabling-fast-scrolling"></a>Включение быстрого прокрутки

Быстрый прокрутки помогают пользователю выполнять прокрутку в длинных списках, предоставляя дополнительные «дескриптор», используемый в качестве полосу прокрутки для прямого доступа к части списка. На этом снимке экрана показано дескриптор быстрого прокрутки:

[![Снимок экрана fast прокрутки с дескриптором прокрутки](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Вызывает дескриптор быстрого прокрутки отображаться сложнее, чем параметр `FastScrollEnabled` свойства `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Добавление раздела индекса

Индекс секции предоставляет дополнительные отзывы пользователей, когда они fast прокрутке длинный список &ndash; показано прокручены в «раздел». Для индекса раздел отображаться подкласс адаптера должен реализовать `ISectionIndexer` интерфейса для ввода текста в зависимости от отображаемых строк индекса:

[![Снимок экрана из H отображаются над разделом, начинающегося с H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Для реализации `ISectionIndexer` необходимо добавить к адаптеру три метода:

-   **GetSections** &ndash; приведен полный список раздела названия индексов, которые могут отображаться. Этот метод требует массив объектов Java, поэтому код должен создать `Java.Lang.Object[]` из коллекции .NET. В нашем примере возвращается список начальных символов в списке как `Java.Lang.String` .

-   **GetPositionForSection** &ndash; возвращает первую позицию строки для данной секции индекса.

-   **GetSectionForPosition** &ndash; возвращает индекс секции, отображаемый для данной строки.


Пример `SectionIndex/HomeScreenAdapter.cs` файл реализует эти методы и дополнительный код в конструктор. Конструктор строит индекс секции, циклический перебор всех строк и извлечения первым знаком заголовок (элементы должны быть уже отсортированы для правильной работы).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

С помощью структуры данных, созданные `ISectionIndexer` методы являются очень простой:

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

Ваш названия разделов индекса не требуется для сопоставления фактического разделы 1:1. Именно поэтому `GetPositionForSection` метод существует.
`GetPositionForSection` дает возможность сопоставить все индексы находятся в списке индекс для любой разделы находятся в представлении списка. Например в индексе, может быть «z», но нет раздел таблицы для каждого символа, вместо «z» сопоставление с 26, он может быть сопоставлен с должны сопоставляться 25 или 24 или любое другое индекс раздела «z».



## <a name="related-links"></a>Связанные ссылки

- [BasicTableAndroid (пример)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (пример)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (пример)](https://developer.xamarin.com/samples/FastScroll/)
