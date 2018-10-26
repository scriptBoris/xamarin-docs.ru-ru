---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 90b4755cdb4b8248c2b731d070d720076d4dda40
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103001"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) Это мини-приложения, который представляет стрелку раскрывающегося списка для выбора элементов. В этом руководстве объясняется, как создать простое приложение, которое отображает список вариантов в "Счетчик", следуют изменения, которые отображают другие значения, связанные с выбранный вариант выбора.

## <a name="basic-spinner"></a>Основные "Счетчик"

В первой части этого руководства вы создадите простой "Счетчик" мини-приложение со списком различных планет. При выборе планеты всплывающее сообщение Отображение выбранного элемента:

[![Снимки экрана HelloSpinner приложения](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Создайте новый проект с именем **HelloSpinner**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий код XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

Обратите внимание, что [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) `android:text` атрибут и [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)в `android:prompt` атрибут оба ссылаются на один и тот же ресурс строки. Этот текст ведет себя как заголовок мини-приложения. При применении к [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), текст заголовка будет отображаться в диалоговом окне выбора, который отображается после выбора мини-приложения.

Изменить **Resources/Values/Strings.xml** и измените файл следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

Второй `<string>` элемент определяет строку заголовка, который ссылается [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) и [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) в макете выше.
`<string-array>` Элемент определяет список строк, которые будут отображаться как список в [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) мини-приложения.

Теперь откройте **MainActivity.cs** и добавьте следующие `using` инструкции:

```csharp
using System;
```

Затем вставьте следующий код для [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
метод:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

После `Main.axml` макета задается в виде содержимого [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) мини-приложения регистрируются в макете с [ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
В [`CreateFromResource()`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/)
Затем метод создает новый [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), который связывает каждого элемента в массиве строк исходный вид для [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (то есть, как каждый элемент будет отображаться в "Счетчик", при выборе) . `Resource.Array.planets_array` Ссылки Идентификаторов `string-array` определенный выше и `Android.Resource.Layout.SimpleSpinnerItem` код ссылается на один макет для стандартных "Счетчик" внешний вид, определенные платформой.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/)
вызывается для определения внешнего вида, для каждого элемента, при открытии мини-приложения. Наконец [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) присваивается связать все элементы с [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) , задав [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) свойство.

Теперь предоставляют метод обратного вызова, который notifys приложения, когда элемент выбран из [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Вот, как будет выглядеть этот метод:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

При выборе элемента, отправитель приводится к [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) таким образом, чтобы получить доступ к элементам. С помощью `Position` свойство `ItemEventArgs`, можно найти текст выбранного объекта и использовать ее для показа [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Запуск приложения; он должен выглядеть следующим образом:

[![Снимок экрана с примером "Счетчик", с помощью режима Mars, которые выбраны в качестве планете](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>С помощью пары ключ/значение "Счетчик"

Часто бывает необходимо использовать `Spinner` для отображения ключевых значений, которые связаны с каким-либо данных, используемых приложением. Так как `Spinner` работает непосредственно с помощью пары "ключ значение", необходимо хранить отдельно пары "ключ/значение", заполнить `Spinner` со значениями ключа, затем использовать положение выбранный ключ в "Счетчик" для поиска значения связанные данные. 

В следующих шагах **HelloSpinner** приложения изменяется для отображения средней температуры для выбранного планеты:

Добавьте следующий `using` инструкцию, чтобы **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Добавьте следующую переменную экземпляра к `MainActivity` класса.
Этот список будет содержать пары ключ/значение для планеты и их средней температуры:

```csharp
private List<KeyValuePair<string, string>> planets;
```

В `OnCreate` метод, добавьте следующий код перед `adapter` объявляется:

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

Этот код создает простой хранилище для планеты и их связанные средней температуры. (В реальном приложении базы данных обычно используется для хранения ключей и связанных с ними данных.)

Сразу после приведенный выше код добавьте следующие строки для извлечения ключей и поместить их в список (в порядке):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Передайте этот список, чтобы `ArrayAdapter` конструктор (вместо `planets_array` ресурсов):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Изменить `spinner_ItemSelected` таким образом выбранному положению используется для поиска значения (температуры), связанные с выбранной планете:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Запуск приложения; тост должен выглядеть следующим образом:

[![Пример выбора планеты, отображение температуры](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Ресурсы

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Некоторые части этой страницы, изменения с учетом работы создана и совместно используется Android откройте исходный проект и используются в соответствии с условиями, описанными в*
[*лицензии Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
