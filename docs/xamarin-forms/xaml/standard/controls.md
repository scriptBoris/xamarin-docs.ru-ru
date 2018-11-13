---
title: Элементы управления XAML Standard (Предварительная версия)
description: В этой статье рассматриваются XAML стандартных элементов управления, доступных в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: b9bf0e1ba14f4e8584bfd8492776ac7c8668df87
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563326"
---
# <a name="xaml-standard-preview-controls"></a>Элементы управления XAML Standard (Предварительная версия)

![Предварительный просмотр](~/media/shared/preview.png)

На этой странице перечислены доступные в предварительной версии, вместе с их эквивалент Xamarin.Forms XAML стандартные элементы управления.

Также приведен список элементов управления, которые имеют имена, новые свойства и перечисления в XAML Standard.

## <a name="controls"></a>Элементы управления

|Xamarin.Forms|XAML Standard|
|--- |--- |
|Frame|Border|
|Средство выбора|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Метка|TextBlock|
|Ввод|TextBox|
|Параметр|ToggleSwitch|
|ContentView|UserControl|


## <a name="properties-and-enumerations"></a>Свойства и перечисления

|Элементы управления Xamarin.Forms с помощью обновленные свойства|Свойство Xamarin.Forms или перечисления|XAML Standard эквивалент|
|--- |--- |--- |
|Кнопка, запись, метки, DatePicker, редактор, SearchBar, TimePicker|TextColor|Foreground|
|VisualElement|BackgroundColor|Фон *|
|Кнопка выбора|BorderColor, OutlineColor|BorderBrush|
|Кнопка|BorderWidth|BorderThickness|
|ProgressBar|Ход выполнения|Значение|
|Кнопка, запись, метки, редактор, SearchBar, диапазон, шрифт|FontAttributesBold, курсив, None|FontStyleItalic норм.|
|Кнопка, запись, метки, редактор, SearchBar, диапазон, шрифт|FontAttributes|FontWeights * полужирный, обычный|
|InputView|KeyboardDefault, URL-адрес, номер, телефон, текст, разговор, отправить по электронной почте|InputScopeNameValue * по умолчанию, URL-адрес, номер, TelephoneNumber, текст, чат, EmailNameOrAddress|
|StackPanel|StackOrientation|Ориентация *|

> [!IMPORTANT]
> Элементы, помеченные * являются неполными в текущей предварительной версии

## <a name="related-links"></a>Связанные ссылки

- [Предварительная версия NuGet](https://aka.ms/xf-xamlstandard-nuget)
