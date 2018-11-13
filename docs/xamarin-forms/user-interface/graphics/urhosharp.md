---
title: Использование UrhoSharp в Xamarin.Forms
description: В этой статье объясняется, как можно использовать UrhoSharp Добавление трехмерной графики в приложения Xamarin.Forms для расширенного представления.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 921419aa2361d9c9e8f1a25bc357c2dd2729e6b6
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563642"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Использование UrhoSharp в Xamarin.Forms

## <a name="what-is-urhosharp"></a>Что такое UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) — мощная подсистема 3D для разработчиков Xamarin и .NET. [Введение](~/graphics-games/urhosharp/introduction.md) приведены дополнительные сведения о библиотеке UrhoSharp и [эти заметки](~/graphics-games/urhosharp/using.md) описывается программирование сцен и действий.

UrhoSharp можно использовать для отображения графики в приложениях Xamarin.Forms.
Это [пример](https://github.com/xamarin/urho-samples/tree/master/FormsSample) показано, как UrhoSharp может использоваться для создания интерактивной трехмерной диаграммы:

![](urhosharp-images/ios-animation.gif "Трехмерной диаграмме интерактивные UrhoSharp в iOS")
![](urhosharp-images/android-animation.gif "трехмерной диаграмме интерактивные UrhoSharp в Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Добавление пакетов UrhoSharp Nuget

Прежде чем использовать UrhoSharp, разработчики должны добавить пакет UrhoSharp Nuget в свое решение. В этом руководстве предполагается, что проект Xamarin.Forms с помощью iOS, Android и .NET Standard проект библиотеки. Весь код будет записываться в проекте библиотеки .NET Standard; но UrhoSharp Nuget необходимо добавить проекты iOS и Android слишком.

Пакет UrhoSharp.Forms Nuget содержит все объекты, необходимые для создания объектов UrhoSharp. Пакет nuget UrhoSharp.Forms включает `UrhoSurface` класс, который используется для размещения UrhoSharp в Xamarin.Forms.
Чтобы начать, щелкните правой кнопкой мыши **пакетов** папки в проект библиотеки .NET Standard и выберите **добавить пакеты...** . Введите условие поиска **UrhoSharp.Forms**выберите **UrhoSharp для Xamarin.Forms**, затем нажмите кнопку **Add Package**.

[![](urhosharp-images/add-package-sml.png "Пакеты диалоговое окно добавления")](urhosharp-images/add-package.png#lightbox "пакетов диалоговое окно добавления")

Пакет UrhoSharp.Forms NuGet будет добавлен в проект:

![](urhosharp-images/packages.png "Папка пакетов")

Повторите описанные выше действия для проектов для конкретной платформы (например, iOS и Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Пошаговое руководство: Добавление UrhoSharp в приложение Xamarin.Forms

В следующих шагах описано в примере UrhoSharp в Xamarin.Forms кода:

1. [Создание структуры на странице Xamarin Forms](#1)
2. [Добавить UrhoSurface](#2)
3. [Создание приложения Urho](#3)
4. [Добавление класса диаграммы UrhoSurface](#4)
5. [Взаимодействие с UrhoSharp](#5)

Обратите внимание, что в образце используется C# 6 функции и может не компилироваться в более ранних версиях Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. Создание страницы Xamarin Forms

В приведенном ниже коде показана страница Xamarin.Forms `UrhoPage` перед любой код с Urho был добавлен:

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. Добавить UrhoSurface

UrhoSharp могут размещаться в `ContentPage` как и другие элементы Xamarin.Forms.
Фрагмент кода ниже показано `UrhoSurface` добавлен на страницу Xamarin.Forms:

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. Создание приложения Urho

Ссылаться на `Charts` класс для реализации трехмерной графики Urho, используемый в этом примере. Показана структура базовый код ниже — Обратите внимание, что класс реализует интерфейс `Urho.Application` которого отличается от `Xamarin.Forms.Application` класс, который реализуется в **App.cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

[UrhoSharp документации](~/graphics-games/urhosharp/index.md) содержит дополнительные сведения о создании трехмерных сцен и действий.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Добавление класса диаграммы UrhoSurface

Используйте `UrhoSurface.Show<T>` универсальный метод, чтобы добавить это приложение Urho страницу Xamarin.Forms. Приведенный ниже фрагмент кода показывает дополнительный код, необходимый для создания `Charts` класса:

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Примечание: `Show<T>` метод является асинхронным и должен быть вызван с `await` ключевое слово.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. Взаимодействие с UrhoSharp

Пример позволяет столбцов диаграммы выбрать и изменить. `Charts` Предоставляет `Bars` и `SelectedBar` для этого взаимодействия.

Каждая «строка» есть обработчик событий выбора, добавленные после `Charts` класс визуализирован, перебор предоставляемым `Bars` коллекции:

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

Обработчик событий использует значение Xamarin.Forms `Slider` элемента управления, чтобы высота данной строки:

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Наконец, связать два `Slider` управляет так, что при изменении их значения на холсте UrhoSharp затрагивается. Первого ползунка поворачивает изображение на трехмерной диаграммы и положение второго ползунка Корректирует высоту выбранной строки.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Анимацию с [начало страницы](#) Показать работу образца.

## <a name="summary"></a>Сводка

На этой странице отображаются как UrhoSharp может использоваться для добавления данных трехмерной визуализации в Xamarin.Forms. Чтение [UrhoSharp документации](~/graphics-games/urhosharp/index.md) Дополнительные сведения о том, как для построения кадром Urho, которые могут быть включены в приложениях Xamarin.Forms с помощью метода, показанного выше.


## <a name="related-links"></a>Связанные ссылки

- [Пример диаграммы (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
