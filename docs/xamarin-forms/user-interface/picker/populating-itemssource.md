---
title: Задание свойства ItemsSource элемент выбора
description: Представление выбора является элементом управления для выбора элемента из списка данных. В этой статье объясняется, как заполнять выбора с данными, задав свойство ItemsSource и реакция на выбор элемента пользователем.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: bf3940bc1bc0318bad4d785388f9dc9292af80ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30789043"
---
# <a name="setting-a-pickers-itemssource-property"></a>Задание свойства ItemsSource элемент выбора

_Представление выбора является элементом управления для выбора элемента из списка данных. В этой статье объясняется, как заполнять выбора с данными, задав свойство ItemsSource и реакция на выбор элемента пользователем._

Усовершенствованы Xamarin.Forms 2.3.4 [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) представления, добавив возможность заполнить его данными, установив его [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) свойство и для извлечения выбранного элемента из [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) свойство. Кроме того, можно изменить цвет текста для выбранного элемента, задав [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.TextColor/) свойства [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/).

## <a name="populating-a-picker-with-data"></a>Заполнение выбора с данными

Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) может заполняться данными, установив его [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) свойства `IList` коллекции. Каждый элемент в коллекции должен быть или производным от, введите `object`. Элементы могут быть добавлены в XAML путем инициализации `ItemsSource` свойства из массива элементов:

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
> Обратите внимание, что `x:Array` элемента требуется `Type` атрибут, указывающий тип элементов в массиве.

Ниже приведен эквивалентный код C#:

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

## <a name="responding-to-item-selection"></a>Реакция на выбор элемента

Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) поддерживает выбор одного элемента за раз. Когда пользователь выбирает элемент, [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) события [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) свойство обновляется в целое число, представляющее индекс элемента, выбранного в списке и [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) изменяются `object` представляет выбранный элемент. [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) Свойство является отсчитываемый от нуля номер, указывающий элемент, выбранный пользователем. Если элемент не выбран, что происходит при [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) сначала создается и инициализируется, `SelectedIndex` равно-1.

> [!NOTE]
> Элемент поведением при выборе в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) можно настраивать на iOS с конкретной платформы. Дополнительные сведения см. в разделе [управление Выбор элемента](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

В следующем примере кода показано, как получить [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) значение свойства из [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) в XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Ниже приведен эквивалентный код C#:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Кроме того, обработчик событий может быть выполнена, когда [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) события:

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

Этот метод получает [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) значение свойства и значение используется для извлечения выбранного элемента из [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) коллекции. Это функционально эквивалентно получение выбранного элемента из [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) свойство. Обратите внимание, что каждый элемент в `ItemsSource` коллекция имеет тип `object`и поэтому должен быть приведен к `string` для отображения.

> [!NOTE]
> Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) может быть инициализировано для отображения заданного элемента, задав [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) или [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) свойства. Тем не менее, эти свойства должны задаваться после инициализации [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) коллекции.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Заполнение выбора с данными с использованием привязки данных

Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) может также заполняться данными с помощью привязки данных для привязки его [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) свойства `IList` коллекции. В языке XAML, это можно сделать с помощью [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) расширения разметки:

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

Ниже приведен эквивалентный код C#:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) Привязывает данные свойства `Monkeys` свойство модели связанное представление, которое возвращает `IList<Monkey>` коллекции. В следующем примере кода показан `Monkey` класс, который содержит четыре свойства:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

При привязке к списку объектов, [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) необходимо объяснить, какое свойство для отображения каждого объекта. Это достигается путем установки [ `ItemDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemDisplayBinding/) свойства для обязательного свойства из каждого объекта. В следующем примере кода показано выше `Picker` заданы для отображения каждого `Monkey.Name` значение свойства.

### <a name="responding-to-item-selection"></a>Реакция на выбор элемента

Можно использовать привязку к данным присваивается объект [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) значения свойства при его изменении:

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

Ниже приведен эквивалентный код C#:

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

[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) Привязывает данные свойства `SelectedMonkey` свойство модели связанное представление, который относится к типу `Monkey`. Таким образом, в том случае, когда пользователь выбирает элемент в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), `SelectedMonkey` будет установлено к выбранному `Monkey` объекта. `SelectedMonkey` Данные объекта отображается в пользовательском интерфейсе, [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) и [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) представления:

![](populating-itemssource-images/monkeys.png "Выбор элемента")

> [!NOTE]
> Обратите внимание, что [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/) и [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) свойства обоих поддерживают имеют двухсторонние привязки по умолчанию.

## <a name="summary"></a>Сводка

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Представление — это элемент управления для выбора элемента из списка данных. В этой статье описано, как выполнить заполнение `Picker` с данными, задав [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/) свойство и реакция на выбор элемента пользователем. Этот подход, который впервые появился в Xamarin.Forms 2.3.4, который является рекомендуемым методом для взаимодействия с `Picker`.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация выбора (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Monkey приложения (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [Выбор привязываемые (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [Средство выбора](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
