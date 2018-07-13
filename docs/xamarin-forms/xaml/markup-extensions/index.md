---
title: Расширения разметки XAML
description: Объясняются способы использования расширения разметки XAML Xamarin.Forms расширить функциональность и гибкость XAML, позволяя атрибутов элемента из источников, отличных от литеральные текстовые строки.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: d507ff3c74de6bb4ea36c1a7b7dc2cd5dd60823b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996744"
---
# <a name="xaml-markup-extensions"></a>Расширения разметки XAML

Расширения разметки XAML помочь расширить возможности и гибкость XAML, позволяя атрибутов элемента из источников, отличных от литеральные текстовые строки.

Например, обычно для параметра `Color` свойство `BoxView` следующим образом:

```xaml
<BoxView Color="Blue" />
```

Или можно разместить его в шестнадцатеричное значение цвета RGB:

```xaml
<BoxView Color="#FF0080" />
```

В любом случае текстовая строка, значение `Color` атрибут преобразуется в `Color` по [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) класса.

Вы можете вместо этого задать `Color` атрибут из значения, хранящегося в словаре ресурсов, или значение статического свойства класса, который вы создали или свойство типа `Color` другого элемента на странице или спрогнозированные из разделения значений цветового тона, насыщенности и яркости.

Все эти параметры являются возможно с помощью расширения разметки XAML. Пусть фразу «расширения разметки» должна вас пугать: расширения разметки XAML — *не* расширения XML. Даже с помощью расширения разметки XAML XAML всегда является юридические XML.

Расширения разметки — это просто другой способ выражения атрибут элемента. XAML расширения разметки обычно распознаются по параметр атрибута, заключенный в фигурные скобки:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Любой атрибут в фигурных скобках задан *всегда* расширения разметки XAML. Тем не менее как вы увидите, расширения разметки XAML можно ссылаться без использования фигурных скобок.

В этой статье, состоит из двух частей:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Использование расширений разметки XAML](consuming.md)  

Использование расширения разметки XAML, определенные в Xamarin.Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Создание расширений разметки XAML](creating.md)

Написать собственные пользовательские расширения разметки XAML.



## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Глава расширения разметки XAML из Xamarin.Forms (книга)](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Словари ресурсов](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Динамические стили](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Привязка данных](~/xamarin-forms/app-fundamentals/data-binding/index.md)
