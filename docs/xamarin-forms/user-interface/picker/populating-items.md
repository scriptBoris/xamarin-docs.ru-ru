---
title: Добавление данных в средстве выбора элементов коллекции
description: Представление выбора является элементом управления для выбора элемента из списка данных. В этой статье объясняется, как заполнять выбора с данными, добавив его в коллекцию элементов и реакция на выбор элемента пользователем.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 63a72861895f79d2d0154297f88610ddb8bb8beb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30792658"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Добавление данных в средстве выбора элементов коллекции

_Представление выбора является элементом управления для выбора элемента из списка данных. В этой статье объясняется, как заполнять выбора с данными, добавив его в коллекцию элементов и реакция на выбор элемента пользователем._

## <a name="populating-a-picker-with-data"></a>Заполнение выбора с данными

Перед Xamarin.Forms 2.3.4, процесса заполнения [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) с данными было добавить данные для отображения только для чтения [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) коллекции, который относится к типу `IList<string>`. Каждый элемент в коллекции должен иметь тип `string`. Элементы могут быть добавлены в XAML путем инициализации `Items` свойства со списком `x:String` элементов:

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

Ниже приведен эквивалентный код C#:

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Помимо добавления данных с помощью `Items.Add` метод, данные также могут быть вставлены в коллекцию с помощью `Items.Insert` метод.

## <a name="responding-to-item-selection"></a>Реакция на выбор элемента

Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) поддерживает выбор одного элемента за раз. Когда пользователь выбирает элемент, [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) события и [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) свойство обновляется в целое число, представляющее индекс элемента, выбранного в списке. `SelectedIndex` Свойство является отсчитываемый от нуля номер, указывающий элемент, выбранный пользователем. Если элемент не выбран, что происходит при `Picker` сначала создается и инициализируется, `SelectedIndex` равно-1.

> [!NOTE]
> Элемент поведением при выборе в [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) можно настраивать на iOS с конкретной платформы. Дополнительные сведения см. в разделе [управление Выбор элемента](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode).

В следующем примере кода показан `OnPickerSelectedIndexChanged` метод обработчика событий, который является выполняются при [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/) события:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Этот метод получает [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) значение свойства и значение используется для извлечения выбранного элемента из [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) коллекции. Поскольку каждый элемент в `Items` коллекция `string`, может отображаться по [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) не требует приведения к типу.

> [!NOTE]
> Объект [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) может быть инициализировано для отображения заданного элемента, задав [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/) свойство. Тем не менее `SelectedIndex` свойство должно быть задано после инициализации [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) коллекции.

## <a name="summary"></a>Сводка

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) Представление — это элемент управления для выбора элемента из списка данных. В этой статье описано, как выполнить заполнение `Picker` с данными путем добавления его в [ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/) коллекции и реакция на выбор элемента пользователем. Это было процесс использования `Picker` до Xamarin.Forms 2.3.4.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрация выбора (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [Средство выбора](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
