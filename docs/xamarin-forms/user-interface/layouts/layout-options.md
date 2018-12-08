---
title: Параметры макета в Xamarin.Forms
description: Каждое представление Xamarin.Forms имеет HorizontalOptions и VerticalOptions свойства, типа LayoutOptions. В этой статье объясняется влияние каждого значения LayoutOptions на выравнивание и расширения представления.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: a78911a13ca3682a18b0911d020d98445b4f560c
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059513"
---
# <a name="layout-options-in-xamarinforms"></a>Параметры макета в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)

_Каждое представление Xamarin.Forms имеет HorizontalOptions и VerticalOptions свойства, типа LayoutOptions. В этой статье объясняется влияние каждого значения LayoutOptions на выравнивание и расширения представления._

## <a name="overview"></a>Обзор

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) Структура инкапсулирует две настройки компоновки:

- **Выравнивание** — представление предпочитаемые выравнивания, который определяет его положение и размер в рамках его родительского макета.
- **Расширения** — используется только [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)и указывает, если представление следует использовать дополнительное пространство, если таковой имеется.

Эти параметры макета могут применяться к [ `View` ](xref:Xamarin.Forms.View)относительно своего родительского элемента, задав [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) или [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойство `View` к одному из открытого поля из [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) структуры. Открытые поля, как показано ниже:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`Start`, `Center`, `End`, И `Fill` поля используются для определения представления выравнивания в макете родительского:

- Для горизонтального выравнивания [ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start) позиций [ `View` ](xref:Xamarin.Forms.View) левой стороны родительского макета, а также для вертикального выравнивания, оно располагает `View` в верхней части родительского макета.
- Для горизонтального и вертикального выравнивания [ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center) горизонтальное или вертикальное выравнивание [ `View` ](xref:Xamarin.Forms.View).
- Для горизонтального выравнивания [ `End` ](xref:Xamarin.Forms.LayoutOptions.End) позиций [ `View` ](xref:Xamarin.Forms.View) правой стороны родительского макета, а также для вертикального выравнивания, оно располагает `View` внизу родительского макета.
- Для горизонтального выравнивания [ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill) гарантирует, что [ `View` ](xref:Xamarin.Forms.View) заполняет ширина родительского макета и выравнивание по вертикали, это гарантирует, что `View` заполняет Высота родительского макета.

`StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, И `FillAndExpand` значения используются для определения настроек выравнивание, и ли представление будет занимать больше места, если он доступен в родительском элементе [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> Значение по умолчанию представления [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства [ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Выравнивание

Выравнивание элементов управления, как представление будет располагаться в его родительской структуры при макета родительского содержит неиспользованное пространство (то есть родительского макета больше, чем общий размер всех его дочерних узлов).

Объект [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) только учитывает `Start`, `Center`, `End`, и `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) поля на дочерние представления, которые находятся в обратном направлении Чтобы `StackLayout` ориентации. Таким образом, дочерним представлениям в вертикально ориентирован `StackLayout` можно задать их [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) свойств с одним из `Start`, `Center`, `End`, или `Fill` полей. Аналогичным образом, дочерним представлениям в горизонтальном `StackLayout` можно задать их [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойств с одним из `Start`, `Center`, `End`, или `Fill` полей.

Объект [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) не учитывает `Start`, `Center`, `End`, и `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) поля на дочерние представления, которые находятся в направлении `StackLayout` ориентации. Таким образом, вертикальный `StackLayout` игнорирует `Start`, `Center`, `End`, или `Fill` поля, если они настроены на [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства дочерних представлений. Аналогичным образом, горизонтальный `StackLayout` игнорирует `Start`, `Center`, `End`, или `Fill` поля, если они настроены на [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) свойства дочерних представлений.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) Обычно переопределения размер запросы, заданные с помощью [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) и [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) свойства.

В следующем примере кода XAML демонстрирует вертикально ориентирован [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) где каждый дочерний элемент [ `Label` ](xref:Xamarin.Forms.Label) задает его [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) свойство к одному из четырех выравнивание полей [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) структуры:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Ниже приведен эквивалентный код на C#:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

Код приводит к компоновку, показанную на следующем снимке экрана:

[![](layout-options-images/alignment.png "Параметры выравнивания макета")](layout-options-images/alignment-large.png#lightbox "параметры выравнивания макета")

<a name="expansion" />

## <a name="expansion"></a>Расширения

Расширения управляет ли представление будет занимать больше места, если он доступен в рамках [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Если `StackLayout` содержит неиспользованное пространство (то есть `StackLayout` больше, чем общий размер всех его дочерних элементов), неиспользуемое пространство разделяется равномерно все дочерние представления, запросить расширения, установив их [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)или [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) поля, которое использует `AndExpand` суффикс. Обратите внимание, что при всех пространств `StackLayout` — используется, дополнительные возможности не оказывают влияния.

Объект [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) можно развернуть только дочерние представления в направлении ориентация. Таким образом, вертикальный `StackLayout` можно развернуть дочерние представления, устанавливающие их [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойств с одним из `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, или `FillAndExpand` поля, если `StackLayout` содержит неиспользованное пространство. Аналогичным образом, горизонтальный `StackLayout` можно развернуть дочерние представления, устанавливающие их [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) свойств с одним из `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, или `FillAndExpand` поля, если `StackLayout` содержит неиспользованное пространство.

Объект [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) Невозможно расширить дочерних представлений в направлении, противоположном ориентация. Таким образом, на вертикально ориентирован `StackLayout`, задание [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) свойство дочернего представления, чтобы [ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) имеет тот же эффект, что свойства [ `Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> Обратите внимание, что включение расширения не изменяется размер представления, если он не использует [ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand).

В следующем примере кода XAML демонстрирует вертикально ориентирован [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) где каждый дочерний элемент [ `Label` ](xref:Xamarin.Forms.Label) задает его [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойство одно из полей расширения [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) структуры:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

Ниже приведен эквивалентный код на C#:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

Код приводит к компоновку, показанную на следующем снимке экрана:

[![](layout-options-images/expansion.png "Параметры расширения разметки")](layout-options-images/expansion-large.png#lightbox "параметры расширения разметки")

Каждый [ `Label` ](xref:Xamarin.Forms.Label) занимает столько пространства в [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Тем не менее только последний `Label`, который устанавливает его [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) свойства [ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) имеют разный размер. Кроме того каждый `Label` отделяется маленький красный значок [ `BoxView` ](xref:Xamarin.Forms.BoxView), что позволяет пространство `Label` занимает легко просматривать.

## <a name="summary"></a>Сводка

В этой статье описано влияние, в которых [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) значения структуры зависит от того, выравнивание и расширения представления, относительно его родительского элемента. `Start`, `Center`, `End`, И `Fill` поля используются для определения представления выравнивания в макете родительский и `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, и `FillAndExpand` поля используются для определения Выравнивание предпочтения и определить ли представление будет занимать больше места, если он доступен в рамках [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).



## <a name="related-links"></a>Связанные ссылки

- [LayoutOptions (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
