---
title: API внешнего вида в Xamarin.iOS
description: iOS позволяет применить параметры visual свойств на уровне статический класс, а не к отдельным объектам, таким образом, чтобы изменение применяется ко всем экземплярам этого элемента управления в приложении.
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 0dd9832a2e4dd0803f92d6e3923fe178252211f4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103573"
---
# <a name="appearance-api-in-xamarinios"></a>API внешнего вида в Xamarin.iOS

_iOS позволяет применить параметры visual свойств на уровне статический класс, а не к отдельным объектам, таким образом, чтобы изменение применяется ко всем экземплярам этого элемента управления в приложении._

Эта функция предоставляется в Xamarin.iOS с помощью статического `Appearance` свойство для всех элементов управления UIKit, которые ее поддерживают. Внешний вид (такие как свойства как оттенок цвета и фоновое изображение) таким образом легко настраивается для предоставления согласованного внешнего вида приложения. Внешний API был введен в iOS 5 и хотя некоторые его части, которые стали нерекомендуемыми в iOS 9 это по-прежнему хороший способ достижения некоторые стили и темы эффектов в приложениях Xamarin.iOS.

## <a name="overview"></a>Обзор

iOS позволяет настроить внешний вид множество элементов управления UIKit стандартным элементам управления, в соответствующих элементах фирменного стиля, которые необходимо применить к приложению.

Чтобы применить пользовательское оформление двумя способами:

- **Непосредственно в экземпляре элемента управления** — можно задать цвет оттенка, фоновое изображение и заголовок выровнен (а также некоторых других атрибутов) на множество элементов управления, включая панели инструментов, панели навигации, кнопок и ползунков.

- **Настройка параметров по умолчанию на внешний вид статического свойства** — настраиваемые атрибуты для каждого элемента управления предоставляются через `Appearance` статическое свойство. Все настройки, применяемых для этих свойств будет использоваться по умолчанию для любого элемента управления этого типа, созданный после свойству.

Внешний вид пример приложения демонстрирует все три метода, как показано на эти снимки экрана:

 [![](introduction-to-the-appearance-api-images/appearance01.png "Внешний вид пример приложения демонстрирует все три метода")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

Начиная с версии iOS 8 внешний вид прокси-сервера была расширена для TraitCollections.
 `AppearanceForTraitCollection` можно задать внешний вид по умолчанию в коллекции конкретного признака. Дополнительные сведения см. в [введение в раскадровки](~/ios/user-interface/storyboards/unified-storyboards.md) руководства.


## <a name="setting-appearance-properties"></a>Задание свойств внешнего вида

На первом экране статический класс внешний вид используется для стиля кнопки и желтый/оранжевый следующим образом:

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

Стили зеленый элементов задаются следующим образом, в `ViewDidLoad` метод, который переопределяет значения по умолчанию и *внешний вид* статический класс:

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>Использование UIAppearance в Xamarin.Forms

API внешнего вида могут быть полезны, при [Задание стиля приложений iOS](~/xamarin-forms/platform/ios/theme.md#uiappearance) в решения Xamarin.Forms. Несколько строк в `AppDelegate` класс может помочь в реализации конкретных цветовую схему без создания [пользовательское средство отрисовки](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).


### <a name="custom-themes-and-uiappearance"></a>Пользовательские темы и UIAppearance

iOS позволяет многие визуальные атрибуты пользователя, элементы управления интерфейса можно будет «темы» с помощью *UIAppearance* API-интерфейсы для принудительного все экземпляры конкретного элемента управления, чтобы выглядеть одинаково. Он предоставляется как свойство внешнего вида на многие классы элементов управления интерфейса пользователя, а не для отдельных экземпляров элемента управления. Задание статического свойства отображения `Appearance` свойство влияет на все элементы управления этого типа в приложении.

Чтобы лучше понять принцип, рассмотрим пример.

Чтобы изменить конкретный `UISegmentedControl` чтобы пурпурного оттенок, мы ссылку определенный элемент управления на экран в наших `ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

Кроме того задайте значение в панели свойств конструктора: 

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Оттенок панель свойств")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

На следующем рисунке показано, что это задает оттенок только с именем «sg1» элемента управления.

 [![](introduction-to-the-appearance-api-images/image53.png "Параметр оттенок отдельного элемента управления")](introduction-to-the-appearance-api-images/image53.png#lightbox)

Чтобы задать множество элементов управления таким образом будет полностью неэффективно, поэтому вместо него статический `Appearance` свойство в самом классе. Это показано в следующем коде:

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

Теперь на следующем рисунке показаны оба Сегментированные элементы управления с внешним видом, равным пурпурный:

 [![](introduction-to-the-appearance-api-images/image54.png "Настройка внешнего вида элемента управления оттенок")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` свойства должны задаваться на ранних этапах жизненного цикла приложения, такие как в AppDelegate `FinishedLaunching` событий, или в ViewController, прежде чем будут отображаться соответствующие элементы управления.


Ссылаться на [Знакомство с API внешнего вида](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) более подробные сведения.


## <a name="related-links"></a>Связанные ссылки

- [Внешний вид (пример)](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [Справочник по протоколу UIAppearance](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Внешний вид в Xamarin.Forms](~/xamarin-forms/platform/ios/theme.md#uiappearance)
