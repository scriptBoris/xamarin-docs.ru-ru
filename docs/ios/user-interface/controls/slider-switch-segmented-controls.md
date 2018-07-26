---
title: Ползунки, коммутаторы и Сегментированные элементы управления в Xamarin.iOS
description: В этом документе рассматриваются слайдов, коммутаторы и Сегментированные элементы управления в Xamarin.iOS, в которых описывается работа с ними, как программно, так и в конструкторе iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7df79cb6f225326dda6656fa9dfe9534e35f2457
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241995"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Ползунки, коммутаторы и Сегментированные элементы управления в Xamarin.iOS

<a name="Sliders" />

## <a name="sliders"></a>Ползунки

Элемент управления "ползунок" позволяет просто выделение числового значения в диапазоне. Элемент управления по умолчанию значение в диапазоне от 0 до 1, но можно настроить эти ограничения.

 [![](slider-switch-segmented-controls-images/image25a.png "\"Ползунок\"")](slider-switch-segmented-controls-images/image25a.png#lightbox)

На следующем рисунке показаны свойства, которые можно редактировать в конструкторе:

 [![](slider-switch-segmented-controls-images/image26a.png "Свойства \"ползунок\"")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Эти значения можно задать в коде, как показано ниже, включая, образующего обработчика, чтобы отобразить текущее выбранное значение в `UILabel` управления:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

Также можно настроить внешний вид ползунка, параметр

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

Настраиваемые ползунок выглядит следующим образом:

 [![](slider-switch-segmented-controls-images/image27a.png "Пользовательские \"ползунок\"")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Сейчас [ошибки](http://stackoverflow.com/a/19496179) вызывает `ThumbTint` для подготовки к просмотру во время выполнения не должным образом. Можно добавить следующую строку кода **перед** кода выше в качестве решения. [[Источника](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Вы можете использовать любой образ, так как он будет переопределено, но убедитесь, что он помещается _в_ каталог ресурсов и вызывается в коде.

<a name="Switch" />

## <a name="switch"></a>Параметр

iOS использует `UISwitch` виде логическое значение входных данных, который может быть представлен переключатель на других платформах. Пользователь может работать с элементом управления, переместив *thumb* между **Вкл/Выкл** позиций.

 [![](slider-switch-segmented-controls-images/image28a.png "Коммутатор")](slider-switch-segmented-controls-images/image28a.png#lightbox)

Можно настраивать внешний вид переключателя **панели свойств** конструктора, которая поможет вам контролировать состояние по умолчанию, **Вкл/Выкл оттенок** цвета и **ON/OFF изображение**. Это показано на рисунке ниже:

 [![](slider-switch-segmented-controls-images/image29a.png "Свойства коммутатора")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Свойства переключателя можно также задать в коде, например в приведенном ниже коде отобразится параметр со значением по умолчанию `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Сегментированные элементы управления

Сегментированный управления является возможность разрешить пользователям взаимодействовать с небольшим числом параметров. Каждый сегмент функции отдельных кнопок, и он располагается горизонтально. При использовании конструктора, сегментированных элемент управления можно найти в разделе **элементов > элементы управления**и должен выглядеть приблизительно так:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Сегментированный управления")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Это уникальная функция конструктора позволяет для каждого сегмента, выбираемый по отдельности в области конструктора, как показано ниже:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Сегментированный управления")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Это позволяет панели свойств, который будет использоваться для более точного управления свойства каждого сегмента. Вы увидите редактируемых свойств на следующем снимке экрана:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Сегментированный управления")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Следует отметить, сегментированных стиль элемента управления был объявлен устаревшим в iOS7, что таким образом, Настройка параметров для этого в приложении iOS7 не повлияет.

## <a name="related-links"></a>Связанные ссылки

- [Элементы управления (пример)](https://developer.xamarin.com/samples/Controls/)
- [Контроллер оповещения](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
