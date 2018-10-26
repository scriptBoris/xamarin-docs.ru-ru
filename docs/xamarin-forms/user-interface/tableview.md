---
title: Xamarin.Forms TableView
description: В этой статье объясняется, как использовать класс Xamarin.Forms TableView для представления прокручивающиеся меню, параметры и формы ввода в приложениях.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: b8e851e735fa39d015e22ce511c39ad825bc97c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120002"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[TableView](xref:Xamarin.Forms.TableView) — это представление для отображения прокручиваемый список данных или возможных вариантов где строк, которые не используют тот же шаблон. В отличие от [ListView](~/xamarin-forms/user-interface/listview/index.md), TableView не поддерживает концепцию `ItemsSource`, поэтому элементы необходимо вручную добавлять как дочерние элементы.

Это руководство состоит из следующих разделов:

- **[Варианты использования](#Use_Cases)**  &ndash; вместо ListView или представления в TableView.
- **[Структура TableView](#TableView_Structure)**  &ndash; иерархии представлений, требуется в течение TableView.
- **[Внешний вид TableView](#TableView_Appearance)**  &ndash; параметры настройки для TableView.
- **[Встроенные ячеек](#Built-In_Cells)**  &ndash; параметров встроенные ячеек, включая [EntryCell](#EntryCell) и [SwitchCell](#SwitchCell).
- **[Пользовательские ячейки](#Custom_Cells)**  &ndash; как создать собственные пользовательские ячейки.

![](tableview-images/tableview-all-sml.png "Пример TableView")

<a name="Use_Cases" />

## <a name="use-cases"></a>Варианты использования

TableView полезен тогда, когда:

- представления списка параметров,
- Сбор данных в форме, или
- Отображение данных, который представляется по-разному из строки строки (например, номера, проценты и изображения).

TableView обрабатывает прокрутка и размещение строк в разделах привлекательным, требуется для указанных выше сценариев. `TableView` Элемент управления использует каждой платформы базовый эквивалентного представления, если она доступна, создание собственного вида для каждой платформы.

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>Структура TableView

Элементы в `TableView` упорядочены по разделам. В корне `TableView` — `TableRoot`, который является родительским для одного или нескольких `TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

Каждый `TableSection` состоит из заголовка и один или несколько ViewCells. Здесь мы видим, `TableSection` `Title` свойство значение *«Кольцо»* в конструкторе:

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

Чтобы выполнить тот же макет, как описано выше в XAML.

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

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>Внешний вид TableView

Предоставляет TableView `Intent` свойство, которое представляет собой перечисление, из следующих вариантов:

- **Данные** &ndash; для использования при отображении записей данных. Обратите внимание, что [ListView](~/xamarin-forms/user-interface/listview/index.md) может быть лучшим вариантом для прокрутки списков данных.
- **Форма** &ndash; для использования при TableView выступает в качестве формы.
- **Меню** &ndash; для использования при представлении меню, доступные для выбора.
- **Параметры** &ndash; для использования при отображении списка параметров конфигурации.

`TableIntent` Выбран, может повлиять на способ `TableView` появляется на каждой платформе. Даже если не очистить различия, рекомендуется выбрать `TableIntent` , наиболее точно соответствующий, как вы планируете использовать в таблице.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>Встроенные ячеек

Xamarin.Forms в состав встроенных ячеек для сбора и отображения сведений. Несмотря на то, что ListView и TableView можно использовать все же ячейки, ниже приведены наиболее важные для сценария TableView.

- **SwitchCell** &ndash; для представления и записи состояния true или false, а также текстовую метку.
- **EntryCell** &ndash; для представления и извлечение текста.

См. в разделе [внешний вид ячейки ListView](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) подробное описание [TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) и [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell).

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) Представляет элемент управления, используемый для представления и запись Вкл/Выкл или `true` / `false` состояние.

SwitchCells имеют одну строку текста для изменения и включить или выключить свойство. Оба эти свойства можно привязать.

- `Text` &ndash; текст, отображаемый рядом с параметром.
- `On` &ndash; следует ли отображать параметр как on или off.

Обратите внимание, что `SwitchCell` предоставляет `OnChanged` событий, дает возможность реагировать на изменения в состояние ячейки.

![](tableview-images/switch-cell.png "Пример SwitchCell")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) полезно при необходимости отображать текстовые данные, пользователь может редактировать. `EntryCell`s предложения, которые могут быть настроены следующие свойства:

- `Keyboard` &ndash; Клавиатуры для отображения во время редактирования. Существуют параметры для таких вещей, как числовые значения, электронной почты, номера телефонов, и т.д. [Документация по API см. в разделе](xref:Xamarin.Forms.Keyboard).
- `Label` &ndash; Текст метки для отображения справа от текстового поля ввода.
- `LabelColor` &ndash; Цвет текста метки.
- `Placeholder` &ndash; Текст, отображаемый в поле ввода, когда он равен null или пуст. Этот текст исчезает, когда начинается запись текста.
- `Text` &ndash; Текст в поле ввода.
- `HorizontalTextAlignment` &ndash; Горизонтальное выравнивание текста. Может быть, влево или вправо центру. [Документация по API см. в разделе](xref:Xamarin.Forms.TextAlignment).

Обратите внимание, что `EntryCell` предоставляет `Completed` событие, которое возникает, когда пользователь нажимает «Готово» на клавиатуре во время редактирования текста.

![](tableview-images/entry-cell.png "Пример EntryCell")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>Пользовательские ячейки
При встроенной ячейки недостаточно, пользовательские ячейки можно использовать для представления и записать данные способом, который лучше всего подходит для вашего приложения. Например можно представить ползунок, чтобы разрешить пользователю выбирать прозрачность изображения.

Все пользовательские ячейки должен быть производным от [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), тот же базовый класс, что все ячейки, встроенные типы использования.

Ниже приведен пример пользовательского ячейки:

![](tableview-images/custom-cell.png "Пример пользовательской ячейки")

### <a name="xaml"></a>XAML
XAML для создания макета выше приведен ниже:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
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
    </ContentPage.Content>
</ContentPage>

```

Делает гораздо выше XAML. Давайте разобьем ее:

- Корневой элемент в разделе `TableView` является `TableRoot`.
- Существует `TableSection` непосредственно под `TableRoot`.
- `ViewCell` Определен непосредственно в раздел таблицы. В отличие от `ListView`, `TableView` не требует этого custom (или любой), определенных в `ItemTemplate`.
- StackLayout используется для управления макет пользовательской ячейки. Здесь можно использовать любой макет.

### <a name="cnum"></a>C&num;

Так как `TableView` работает с статических данных или данных, необходимо вручную изменить, не предоставляет концепцию шаблон элемента. Вместо этого пользовательские ячейки можно вручную создать и поместить в таблицу. Обратите внимание, что метод создания пользовательской ячейки, наследует от `ViewCell`, а затем добавьте его, чтобы `TableView` как вы бы встроенные ячейки, также поддерживается.
Ниже приведен код c# для выполнения выше макета:

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

C# выше делает гораздо. Давайте разобьем ее:

- Корневой элемент в разделе `TableView` является `TableRoot`.
- Существует `TableSection` непосредственно под `TableRoot`.
- `ViewCell` Определен непосредственно в раздел таблицы. В отличие от `ListView`, `TableView` не требует этого custom (или любой), определенных в `ItemTemplate`.
- StackLayout используется для управления макет пользовательской ячейки. Здесь можно использовать любой макет.

Обратите внимание на то, что никогда не определен класс для пользовательской ячейки. Вместо этого `ViewCell`его представление имеет значение для конкретного экземпляра `ViewCell`.

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
