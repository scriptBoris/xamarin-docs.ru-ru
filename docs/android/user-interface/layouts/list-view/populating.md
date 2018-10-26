---
title: Заполнение ListView данными
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 57c69223a01074ed15714026b7e9ec4e995808e0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103183"
---
# <a name="populating-a-listview-with-data"></a>Заполнение ListView данными


## <a name="overview"></a>Обзор

Для добавления строк в `ListView` необходимо добавить его макет и реализуйте `IListAdapter` с методами, `ListView` вызовы для заполнения сам. Android включает встроенные `ListActivity` и `ArrayAdapter` классы, которые можно использовать без определения любой пользовательский макет XML или кода. `ListActivity` Класс автоматически создает `ListView` и предоставляет `ListAdapter` свойство, которое предоставляет представлений строк для отображения через адаптер.

Встроенные адаптеры принимают идентификатора ресурса представления в качестве параметра, используемый для каждой строки. Можно использовать встроенные ресурсы, например, в `Android.Resource.Layout` , не нужно писать собственные.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>С помощью ListActivity и ArrayAdapter&lt;строки&gt;

Пример **BasicTable/HomeScreen.cs** демонстрируется использование этих классов для отображения `ListView` только несколько строк кода:

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


### <a name="handling-row-clicks"></a>Обработка строк нажимает кнопку

Обычно `ListView` также позволит пользователю touch строки для выполнения некоторых действий (например, воспроизведение песни, или вызов контакт или отображения другого экрана). Реагировать на штрихи пользователь должен быть один дополнительные метод реализован в `ListActivity` &ndash; `OnListItemClick` &ndash; следующим образом:

[![Снимок экрана SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Теперь пользователь могут соприкасаться строки и `Toast` , появляется предупреждение:

[![Снимок экрана из всплывающее уведомление, отображаемый при касании строки](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Реализация ListAdapter

`ArrayAdapter<string>` отлично подходит из-за его простоты, но он очень ограничены. Тем не менее зачастую имеется коллекция бизнес-сущности, а не просто строки, которые вы хотите выполнить привязку.
Например если данных представляет собой коллекцию классов сотрудников, может возникнуть списка, чтобы просто отобразить имена каждого сотрудника. Чтобы настроить поведение `ListView` для управления отображением данных необходимо реализовать подкласс `BaseAdapter` переопределение следующих четырех элементов:

-   **Число** &ndash; в элемент управления, сколько строк данных.

-   **GetView** &ndash; возвратить представление для каждой строки, заполненную данными.
    Этот метод имеет параметр для `ListView` для передачи в существующий, неиспользуемые строку для повторного использования.

-   **GetItemId** &ndash; возвращают идентификатор строки (обычно строки number, несмотря на то, что он может быть любое значение long, что вам нравится).

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


### <a name="using-a-custom-adapter"></a>С помощью пользовательского адаптера

Использование пользовательского адаптера, подобно встроенной `ArrayAdapter`, передавая `context` и `string[]` для отображения значений:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Так как в этом примере используется один и тот же макет строк (`SimpleListItem1`) полученное приложение будет выглядеть в предыдущем примере.


### <a name="row-view-re-use"></a>Повторное использование представления строк

В этом примере существует только шесть элементов. Поскольку экрана можно разместить восемь, не строку повторного использования требуется. При отображении сотни или тысячи строк, однако было бы к повышенному расходу памяти для создания сотен или тысяч `View` объекты, если только восемь умещается на экране одновременно. Чтобы избежать такой ситуации, когда строка скрывается с экрана, на котором его представления помещается в очередь для повторного использования. Во время прокрутки, `ListView` вызовы `GetView` для запроса отображения новых представлений &ndash; Если доступны его, неиспользуемое представление передается в `convertView` параметра. Если это значение равно null, то ваш код должен создать новый экземпляр представления, в противном случае можно повторно установить свойства этого объекта и использовать его повторно.

`GetView` Метод необходимо следовать этому шаблону для повторного использования представлений строк:

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

Реализации пользовательского адаптера следует *всегда* повторного использования `convertView` объект, прежде чем создавать новые представления, чтобы они не выполняются не хватает памяти при отображении длинных списков.

Некоторые реализации адаптера (такие как `CursorAdapter`) нет `GetView` метод, вместо этого они требуют два метода `NewView` и `BindView` которой обеспечивают повторного использования строки путем разделения ответственности `GetView` на две части методы. Существует `CursorAdapter` пример далее в этом документе.


## <a name="enabling-fast-scrolling"></a>Включение быстрого прокрутка

Быстрый прокрутка помогает пользователю выполнять прокрутку длинных списках, предоставляя дополнительные «дескриптор», используемый в качестве полосу прокрутки для прямого доступа к части списка. На этом снимке экрана показан маркер быстрого прокрутки:

[![Снимок экрана: Быстрая прокрутка с дескриптором прокрутки](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Вызывает дескриптор быстрый прокрутки для отображения простой установке `FastScrollEnabled` свойства `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Добавление раздела индекса

Индекс раздела предоставляет дополнительный отзыв для пользователей, когда они быстрой прокрутке длинного списка &ndash; нанесенными они выполнена прокрутка «раздел». Чтобы индекс раздела для отображения подкласс адаптера должен реализовать `ISectionIndexer` интерфейс для ввода текста в зависимости от отображаемых строк индекса:

[![Снимок экрана из H отображаются над разделом, который начинается с H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Для реализации `ISectionIndexer` необходимо добавить три метода к адаптеру:

-   **GetSections** &ndash; предоставляет полный список разделе названия индексов, которые могут отображаться. Этот метод требует массив объектов Java, поэтому код должен создать `Java.Lang.Object[]` из коллекции .NET. В нашем примере он возвращает список начальных символов в списке как `Java.Lang.String` .

-   **GetPositionForSection** &ndash; возвращает первую позицию строки для данной секции индекса.

-   **GetSectionForPosition** &ndash; возвращает индекс раздела для отображения для конкретной строки.


Пример `SectionIndex/HomeScreenAdapter.cs` файл реализует эти методы и дополнительный код в конструктор. Конструктор строит индекс раздела, цикла по каждой строке и извлечения первый символ заголовка (элементы должны быть уже отсортированы для правильной работы).

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

С помощью структуры данных, созданные `ISectionIndexer` методы очень просты:

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

Нет необходимости вашей заголовки разделов индекса сопоставляются фактическое разделы 1:1. Вот почему `GetPositionForSection` метод существует.
`GetPositionForSection` дает возможность сопоставить любые индексы являются в списке индекса для любой разделы находятся в представлении списка. Например имеется «z» в индексе, но нет раздел таблицы для каждой буквы, вместо «z» сопоставление с 26, он может сопоставить с 25 или 24 или любого другого индекса раздел «z» должны сопоставляться.



## <a name="related-links"></a>Связанные ссылки

- [BasicTableAndroid (пример)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (пример)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (пример)](https://developer.xamarin.com/samples/FastScroll/)
