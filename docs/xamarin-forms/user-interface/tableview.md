---
title: Xamarin.Forms TableView
description: В этой статье объясняется, как использовать класс Xamarin.Forms TableView для представления прокручивающиеся меню, параметры и формы ввода в приложениях.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: fda8c08cdbdab01f5d68b7f349d5f28f31316290
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832006"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[`TableView`](xref:Xamarin.Forms.TableView) — Это представление для отображения прокручиваемый список данных или возможных вариантов где строк, которые не используют тот же шаблон. В отличие от [ListView](~/xamarin-forms/user-interface/listview/index.md), `TableView` не поддерживает концепцию `ItemsSource`, поэтому необходимо вручную добавить элементы как дочерние элементы.

![](tableview-images/tableview-all-sml.png "Пример TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Варианты использования

[`TableView`](xref:Xamarin.Forms.TableView) полезен тогда, когда:

- представления списка параметров,
- Сбор данных в форме, или
- Отображение данных, который представляется по-разному из строки строки (например, номера, проценты и изображения).

[`TableView`](xref:Xamarin.Forms.TableView) Выполняет прокрутку и размещение строк в разделах привлекательным, требуется для указанных выше сценариев. `TableView` Элемент управления использует каждой платформы базовый эквивалентного представления, если она доступна, создание собственного вида для каждой платформы.

<a name="TableView_Structure" />

## <a name="structure"></a>Структура

Элементы в [ `TableView` ](xref:Xamarin.Forms.TableView) упорядочены по разделам. В корне `TableView` — [ `TableRoot` ](xref:Xamarin.Forms.TableRoot), который является родительским для одного или нескольких [ `TableSection` ](xref:Xamarin.Forms.TableSection) экземпляров. Каждый [ `TableSection` ](xref:Xamarin.Forms.TableSection) состоит из заголовка и один или несколько [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) экземпляров:

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
            <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

Ниже приведен аналогичный код C#:

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>Вид

[`TableView`](xref:Xamarin.Forms.TableView) предоставляет [ `Intent` ](xref:Xamarin.Forms.TableView.Intent) свойство, которое может быть задан любой из [ `TableIntent` ](xref:Xamarin.Forms.TableIntent) членов перечисления:

- `Data` — для использования при отображении записей данных. Обратите внимание, что [ListView](~/xamarin-forms/user-interface/listview/index.md) может быть лучшим вариантом для прокрутки списков данных.
- `Form` — для использования при TableView выступает в качестве формы.
- `Menu` — для использования при представлении меню, доступные для выбора.
- `Settings` — для использования при отображении списка параметров конфигурации.

[ `TableIntent` ](xref:Xamarin.Forms.TableIntent) Значением, выбираемым может повлиять на способ [ `TableView` ](xref:Xamarin.Forms.TableView) появляется на каждой платформе. Даже если не очистить различия, рекомендуется выбрать `TableIntent` , наиболее точно соответствующий, как вы планируете использовать в таблице.

Кроме того, цвет текста, отображаемый для каждого [ `TableSection` ](xref:Xamarin.Forms.TableSection) может быть изменена заданием `TextColor` свойства [ `Color` ](xref:Xamarin.Forms.Color).

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Встроенные ячеек

Xamarin.Forms в состав встроенных ячеек для сбора и отображения сведений. Несмотря на то что [ `ListView` ](xref:Xamarin.Forms.ListView) и [ `TableView` ](xref:Xamarin.Forms.TableView) можно использовать все же ячейки, [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell) и [ `EntryCell` ](xref:Xamarin.Forms.EntryCell)больше всего подходят для `TableView` сценария.

См. в разделе [внешний вид ячейки ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) подробное описание [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) и [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) Представляет элемент управления, используемый для представления и запись Вкл/Выкл или `true` / `false` состояние. Он определяет следующие свойства:

- `Text` — текст, отображаемый рядом с параметром.
- `On` — следует ли отображать параметр как on или off.
- `OnColor` — [ `Color` ](xref:Xamarin.Forms.Color) переключателя в положение on.

Все эти свойства можно привязать.

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) также предоставляет `OnChanged` событий, дает возможность реагировать на изменения в состояние ячейки.

![](tableview-images/switch-cell.png "Пример SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) полезно при необходимости отображать текстовые данные, пользователь может редактировать. Он определяет следующие свойства:

- `Keyboard` — Клавиатуры для отображения во время редактирования. Существуют параметры для таких вещей, как числовые значения, электронной почты, номера телефонов, и т.д. [Документация по API см. в разделе](xref:Xamarin.Forms.Keyboard).
- `Label` — Текст метки для отображения справа от текстового поля ввода.
- `LabelColor` — Цвет текста метки.
- `Placeholder` — Текст для отображения в поле ввода, если он пуст или равен null. Этот текст исчезает, когда начинается запись текста.
- `Text` — Текст в поле ввода.
- `HorizontalTextAlignment` — Горизонтальное выравнивание текста. Может быть, влево или вправо центру. [Документация по API см. в разделе](xref:Xamarin.Forms.TextAlignment).

[`EntryCell`](xref:Xamarin.Forms.EntryCell) также предоставляет `Completed` событие, которое возникает, когда пользователь нажимает кнопку «Готово» на клавиатуре во время редактирования текста.

![](tableview-images/entry-cell.png "Пример EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Пользовательские ячейки

При встроенной ячейки недостаточно, пользовательские ячейки можно использовать для представления и записать данные способом, который лучше всего подходит для вашего приложения. Например можно представить ползунок, чтобы разрешить пользователю выбирать прозрачность изображения.

Все пользовательские ячейки должен быть производным от [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), тот же базовый класс, что все ячейки, встроенные типы использования.

Ниже приведен пример пользовательского ячейки:

![](tableview-images/custom-cell.png "Пример пользовательской ячейки")

В следующем примере показан XAML, используемый для создания [ `TableView` ](xref:Xamarin.Forms.TableView) на снимках экрана выше:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
      <TableView Intent="Settings">
          <TableRoot>
              <TableSection Title="Getting Started">
                  <ViewCell>
                      <StackLayout Orientation="Horizontal">
                          <Image Source="bulb.png" />
                          <Label Text="left"
                                 TextColor="#f35e20" />
                          <Label Text="right"
                                 HorizontalOptions="EndAndExpand"
                                 TextColor="#503026" />
                      </StackLayout>
                  </ViewCell>
              </TableSection>
          </TableRoot>
      </TableView>
</ContentPage>
```

Ниже приведен аналогичный код C#:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

Корневой элемент в разделе [ `TableView` ](xref:Xamarin.Forms.TableView) — [ `TableRoot` ](xref:Xamarin.Forms.TableRoot)и [ `TableSection` ](xref:Xamarin.Forms.TableSection) непосредственно под `TableRoot`. [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) Определен непосредственно под `TableSection`и [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) используется для управления макет пользовательской ячейки, несмотря на то, что здесь может использоваться любой макет.

> [!NOTE]
> В отличие от [ `ListView` ](xref:Xamarin.Forms.ListView), [ `TableView` ](xref:Xamarin.Forms.TableView) не требует этого custom (или любой), определенных в `ItemTemplate`.

## <a name="row-height"></a>Высота строки

[ `TableView` ](xref:Xamarin.Forms.TableView) Класс имеет два свойства, которые могут использоваться для изменения высоты строки ячеек:

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) — Задает высоту каждой строки `int`.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) — строки имеют различные значения высоты, если значение `true`. Обратите внимание, что при задании этого свойства `true`, высоты строк автоматически вычисляются и применены с Xamarin.Forms.

При высоту содержимого ячейки в [ `TableView` ](xref:Xamarin.Forms.TableView) изменяется строка высота неявно обновится на Android и универсальной платформы Windows (UWP). Тем не менее, в iOS он должен иметь возможность для обновления, задав [ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows) свойства `true` и вызвав [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) метод.

В следующем примере показан XAML [ `TableView` ](xref:Xamarin.Forms.TableView) , содержащий [ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Когда [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) шифрованию, `OnViewCellTapped` выполняется обработчик событий:

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped` Обработчик событий показывает или скрывает второй [ `Label` ](xref:Xamarin.Forms.Label) в [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)и явным образом обновляет размер ячейки, вызвав [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) метод.

На следующих снимках экрана показано ячейки до касание после:

![](tableview-images/cell-beforeresize.png "ViewCell до изменения его размера")

Далее на снимках экрана Показать ячейки после касание после:

![](tableview-images/cell-afterresize.png "ViewCell после изменения размера")

> [!IMPORTANT]
> Если эта функция является чрезмерным, есть вероятность, strong снижения производительности.

## <a name="related-links"></a>Связанные ссылки

- [TableView (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)
