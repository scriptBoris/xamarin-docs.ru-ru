---
title: Навигация с помощью клавиатуры
description: Вместо того чтобы использовать последовательность перехода по умолчанию, иногда это необходимо для настройки пользовательского интерфейса, указав последовательность с табуляцией с сочетанием свойства TabIndex и IsTapStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: f703dff56d2947c35a9bc76e0eb909bfe9023bac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131954"
---
# <a name="keyboard-navigation-in-xamarinforms"></a>В Xamarin.Forms с помощью клавиатуры

Некоторые пользователи могут иметь сложностей с использованием приложений, которые не предоставляют сочетания доступа. Задание последовательности табуляции для элементов управления включает навигацию с клавиатуры и подготавливает страницы приложений для получения входных данных в определенном порядке.

По умолчанию в последовательности табуляции элементов управления является том же порядке, в котором они перечислены в XAML, или программно добавить дочернюю коллекцию. Этот порядок является порядок, в котором элементы управления будет выполнен переход по с помощью клавиатуры, которая часто этот порядок по умолчанию оптимальный порядок. Однако порядок по умолчанию не всегда так же, как ожидаемом порядке, как показано в следующем примере кода XAML:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

На следующем снимке экрана показаны последовательности табуляции по умолчанию для этого примера кода:

![](keyboard-images/default-tab-order.png "Последовательность перехода по строкам")

Здесь последовательности табуляции на уровне строк и порядок следования элементов управления в XAML. Таким образом, нажатие клавиши Tab переходит к forename [ `Entry` ](xref:Xamarin.Forms.Entry) экземпляров, следуют surname `Entry` экземпляров. Тем не менее пользовательский интерфейс более интуитивно будет использовать первый столбец ТАВ, таким образом, чтобы нажимать клавишу Tab переходит по фамилии forename пары. Это можно сделать путем указания последовательности табуляции элементов управления вводом.

> [!NOTE]
> На универсальной платформе Windows сочетания клавиш можно определить, которые предоставляют интуитивно понятного для пользователей, для быстрого перехода и взаимодействия с приложения видимого пользовательского интерфейса клавиатуры, а не с помощью сенсорного ввода или мышь. Дополнительные сведения см. в разделе [задание VisualElement сочетаний клавиш](~/xamarin-forms/platform/platform-specifics/consuming/windows.md#visualelement-accesskeys).

## <a name="setting-the-tab-order"></a>Установка последовательности табуляции

`VisualElement.TabIndex` Свойство используется для указания порядка, в котором [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) экземпляры получают фокус, когда пользователь переходит между элементами управления, нажав клавишу Tab. По умолчанию значение свойства равно 0, и он может быть задан любой `int` значение.

Применяются следующие правила, когда с помощью последовательности табуляции по умолчанию или указав параметр `TabIndex` свойство:

 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) экземпляры с `TabIndex` , равным 0, добавляются в последовательности табуляции, в зависимости от порядка их объявления в XAML или дочерних коллекций.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) экземпляры с `TabIndex` больше 0, добавляются в последовательности табуляции зависимости от их `TabIndex` значение.
 - [`VisualElement`](xref:Xamarin.Forms.VisualElement) экземпляры с `TabIndex` меньше, чем 0, добавляются в последовательности табуляции и появляются перед всеми нулевое значение.
 - Конфликты для `TabIndex` устраняемые в порядке объявления.

После определения последовательности табуляции, нажатие клавиши Tab будет переключать фокус между элементами управления в восходящем `TabIndex` заказа, переходя в начало в достижении конечный элемент управления.

В следующем примере показан XAML `TabIndex` свойство задать элементы управления для ввода, чтобы включить навигацию по вкладкам первый столбец:

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

На следующем снимке экрана показаны последовательности табуляции для этого примера кода:

![](keyboard-images/correct-tab-order.png "Последовательность перехода по столбцам")

Здесь последовательности табуляции, основанного на столбцах. Таким образом, нажатие клавиши Tab переходит по фамилии forename [ `Entry` ](xref:Xamarin.Forms.Entry) пары.

## <a name="excluding-controls-from-the-tab-order"></a>Исключение из последовательности табуляции элементов управления

Кроме настройки последовательности табуляции элементов управления, может потребоваться исключить из последовательности табуляции элементов управления. Один из способов сделать это, задав [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement) свойства элементов управления для `false`, так как отключенные элементы управления, исключаются из последовательности табуляции.

Тем не менее может потребоваться исключить из последовательности табуляции элементов управления, даже в том случае, если они не отключены. Это можно сделать с помощью `VisualElement.IsTapStop` свойство, которое указывает ли [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) включается в ТАВ. Значение по умолчанию — `true`, и если его значение равно `false` элемент управления учитывается в инфраструктуре навигацию по вкладкам, независимо от того, если `TabIndex` имеет значение.

## <a name="supported-controls"></a>Поддерживаемые элементы управления

`TabIndex` И `IsTapStop` свойства поддерживаются для следующих элементов управления, которые принимает ввод с клавиатуры на одну или несколько платформ:

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Все эти элементы управления не вкладки, на всех платформах, способному получать фокус.

## <a name="related-links"></a>Связанные ссылки

- [Специальные возможности (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
