---
title: Задание свойства ItemsSource средства выбора
description: Представление выбора является элемент управления для выбора элемента из списка данных. В этой статье объясняется, как заполнить управляющий элемент выбора с данными, задав свойство ItemsSource и как реагировать на выбор элемента пользователем.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 3f82e4b7d52988bfef9736ace8c476a9cd2da02b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994748"
---
# <a name="setting-a-pickers-itemssource-property"></a>Задание свойства ItemsSource средства выбора

_Представление выбора является элемент управления для выбора элемента из списка данных. В этой статье объясняется, как заполнить управляющий элемент выбора с данными, задав свойство ItemsSource и как реагировать на выбор элемента пользователем._

Была усовершенствована, Xamarin.Forms 2.3.4 [ `Picker` ](xref:Xamarin.Forms.Picker) представления, добавив возможность заполнить его данными, задав его [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) свойство и для извлечения выбранного элемента из [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) свойство. Кроме того, можно изменить цвет текста для выбранного элемента, задав [ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor) свойства [ `Color` ](xref:Xamarin.Forms.Color).

## <a name="populating-a-picker-with-data"></a>Заполнение управляющий элемент выбора с данными

Объект [ `Picker` ](xref:Xamarin.Forms.Picker) могут заполняться с помощью данных, задав его [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) свойства `IList` коллекции. Каждый элемент в коллекции могут иметь и выведенный из типов `object`. Элементы могут быть добавлены в XAML путем инициализации `ItemsSource` свойства из массива элементов:

```xaml
<Picker x:Name="picker" Title="Select a monkey">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Обратите внимание, что `x:Array` элемента требуется `Type` атрибут, указывающий, тип элементов в массиве.

Ниже приведен эквивалентный код на C#:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Реагирование на выбор элементов

Объект [ `Picker` ](xref:Xamarin.Forms.Picker) поддерживает выбор одного элемента за раз. Когда пользователь выбирает элемент, [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) вызывает событие [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) в целое число, представляющее индекс выбранного элемента в списке, обновляется свойство и [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) обновляется `object` представляющий выбранный элемент. [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) Свойство является отсчитываемый от нуля номер, указывающий элемент, выбранный пользователем. Если элемент не выбран, что происходит при [ `Picker` ](xref:Xamarin.Forms.Picker) сначала создается и инициализируется, `SelectedIndex` будет равно -1.

> [!NOTE]
> Элемент поведение выделения в [ `Picker` ](xref:Xamarin.Forms.Picker) можно настраивать на iOS с конкретной платформы. Дополнительные сведения см. в разделе [управление Выбор элемента](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

В следующем примере кода показано, как получить [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) значение свойства из [ `Picker` ](xref:Xamarin.Forms.Picker) в XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Ниже приведен эквивалентный код на C#:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Кроме того, обработчик событий может быть выполнен, когда [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) вызывает событие:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Этот метод получает [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) значение свойства и использует его для получения выбранного элемента из [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) коллекции. Это функционально эквивалентно получение выбранного элемента из [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) свойство. Обратите внимание, что каждый элемент в `ItemsSource` коллекция имеет тип `object`и поэтому должен быть приведен к `string` для отображения.

> [!NOTE]
> Объект [ `Picker` ](xref:Xamarin.Forms.Picker) может быть инициализировано для отображения указанного элемента, задав [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) или [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) свойства. Тем не менее, эти свойства должны быть заданы после инициализации [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) коллекции.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Заполнение управляющий элемент выбора с данными, с использованием привязки данных

Объект [ `Picker` ](xref:Xamarin.Forms.Picker) может также заполняться данными с помощью привязки данных для привязки его [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) свойства `IList` коллекции. В XAML это достигается за счет [ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension) расширение разметки:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

Ниже приведен эквивалентный код на C#:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) Свойство данных привязывает к `Monkeys` свойство модели связанное представление, которое возвращает `IList<Monkey>` коллекции. В следующем коде показано в примере `Monkey` класс, который содержит четыре свойства:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

При привязке к списку объектов, [ `Picker` ](xref:Xamarin.Forms.Picker) необходимо объяснить, какие свойства для отображения из каждого объекта. Это достигается путем установки [ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding) значение обязательного свойства из каждого объекта. В приведенных выше примеров кода `Picker` настроен на отображение каждой `Monkey.Name` значение свойства.

### <a name="responding-to-item-selection"></a>Реагирование на выбор элементов

Привязка данных может использоваться для присвоено объекту [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) значение свойства после их изменения:

```xaml
<Picker Title="Select a monkey"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

Ниже приведен эквивалентный код на C#:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) Свойство данных привязывает к `SelectedMonkey` свойство связанное представление модели, которые имеют тип `Monkey`. Таким образом, в том случае, когда пользователь выбирает элемент в [ `Picker` ](xref:Xamarin.Forms.Picker), `SelectedMonkey` будет установлено к выбранному `Monkey` объекта. `SelectedMonkey` Объекта данные отображаются в пользовательском интерфейсе объектом [ `Label` ](xref:Xamarin.Forms.Label) и [ `Image` ](xref:Xamarin.Forms.Image) представления:

![](populating-itemssource-images/monkeys.png "Выбор элемента")

> [!NOTE]
> Обратите внимание, что [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) и [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) свойства обоих поддерживают имеют двухсторонние привязки по умолчанию.

## <a name="summary"></a>Сводка

[ `Picker` ](xref:Xamarin.Forms.Picker) Представление является элемент управления для выбора элемента из списка данных. В этой статье описано, как заполнять `Picker` с данными, задав [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) свойство и как реагировать на выбор элемента пользователем. Этот подход, который появился в Xamarin.Forms 2.3.4, подход рекомендуется для взаимодействия с `Picker`.


## <a name="related-links"></a>Связанные ссылки

- [Средство выбора Demo (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey приложения (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Выбор привязки (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Средство выбора](xref:Xamarin.Forms.Picker)
