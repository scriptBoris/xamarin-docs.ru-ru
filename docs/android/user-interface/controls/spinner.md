---
title: Spinner
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 039c3f3a177d62a43679facba821675c6d716a91
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774347"
---
# <a name="spinner"></a>Spinner

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) Это мини-приложение, представленных в раскрывающемся списке Выбор элементов. В этом руководстве описывается создание простого приложения, которое отображает список вариантов в счетчик, следуют изменения, которые отобразить другие значения, связанные с выбранный вариант.

## <a name="basic-spinner"></a>Базовый счетчик

В первой части этого учебника вы создадите простой счетчик мини-приложение, отображающее список планеты. При выборе планеты всплывающее сообщение Отображение выбранного элемента:

[![Снимки экрана примера приложения HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Создание нового проекта с именем **HelloSpinner**.

Откройте **Resources/Layout/Main.axml** и вставьте следующий XML-код:

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

Обратите внимание, что [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) `android:text` атрибута и [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) `android:prompt` оба атрибута ссылаются на один и тот же ресурс строки. Этот текст ведет себя как заголовок мини-приложения. При применении к [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), текст заголовка будет отображаться в диалоговом окне выбора, которое отображается после выбора мини-приложения.

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

Второй `<string>` элемент определяет строки заголовка, который ссылается [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) и [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) в макете выше.
`<string-array>` Элемент определяет список строк, отображаемых в списке [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) мини-приложения.

Теперь откройте **MainActivity.cs** и добавьте следующее `using` инструкции:

```csharp
using System;
```

Затем вставьте следующий код для [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) метод:

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

После `Main.axml` макета задается в виде содержимого [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) захвачен мини-приложение из макета с [ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
[ `CreateFromResource()` ](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/) Затем метод создает новый [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), который связывает каждого элемента в массиве строк исходного вида для [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (то есть, как каждый элемент отображается в "Счетчик" при выборе). `Resource.Array.planets_array` Ссылки на Идентификаторы `string-array` описанный выше и `Android.Resource.Layout.SimpleSpinnerItem` код ссылается на макет для внешнего вида стандартных "Счетчик", определенные платформой.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/) вызывается, чтобы определить внешний вид для каждого элемента при открытии мини-приложения. Наконец [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) равно связать все элементы с [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) , установив [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) свойство.

Теперь предоставляют метод обратного вызова, который notifys приложения, если элемент был выбран из [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Вот, как должны выглядеть этот метод.

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

При выборе элемента отправителя, приводится к [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) так, чтобы получить доступ к элементам. С помощью `Position` свойство `ItemEventArgs`, можно определить текст выбранного объекта и использовать его для отображения [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Запуск приложения; он должен выглядеть следующим образом:

[![Снимок экрана с примером "Счетчик", с помощью режима Mars, выбранном в качестве планета](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>С помощью пары ключ значение "Счетчик"

Часто бывает необходимо использовать `Spinner` для отображения значения ключей, которые связаны с какой-то данные, используемые приложением. Поскольку `Spinner` не работает непосредственно с пары "ключ значение", необходимо отдельно хранить пара ключ значение, заполнение `Spinner` со значениями ключа, затем использовать позицию текущего раздела в счетчик для поиска значения связанные данные. 

В следующих шагах **HelloSpinner** приложения изменяются так, чтобы отобразить значение температуры среднее для выбранного планеты:

Добавьте следующие `using` инструкции **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Добавьте следующую переменную экземпляра для `MainActivity` класса.
Этот список будет содержать пары ключ значение для планеты и их среднее температуры:

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

Этот код создает простой хранилище для планеты и их связанные среднее температуре. (В реальном приложении базы данных обычно используется для хранения ключей и связанные с ними данные.)

Сразу после кода выше добавьте следующие строки для извлечения ключей и поместить их в список (в порядке):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Этот список, чтобы передать `ArrayAdapter` конструктор (вместо `planets_array` ресурсов):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Изменить `spinner_ItemSelected` , чтобы выбранные позиции используется для поиска значения (температура), связанные с выбранной планеты:

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

[![Пример выбора планеты отображение температуры](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Ресурсы

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Некоторые части этой страницы, изменения на основе работы создан и совместно используются Android открыть исходный проект и используются в соответствии с условиями, описанной в*
[*Creative Commons 2.5 однозначного соответствия лицензий* ](http://creativecommons.org/licenses/by/2.5/).
