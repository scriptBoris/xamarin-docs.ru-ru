---
title: Сводка Глава 5. Работа с размерами
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 5. Работа с размерами'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: fd6694de756938ff564bed0923427fe62153116a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056088"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Сводка Глава 5. Работа с размерами

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Заметки на этой странице указывать области, где различаются Xamarin.Forms материал, представленный в книге.

Несколько размеров в Xamarin.Forms обнаружены в данный момент:

- Высота строки состояния iOS — 20
- `BoxView` Имеет стандартную ширину и высоту 40
- Значение по умолчанию `Padding` в `Frame` — 20
- Значение по умолчанию `Spacing` на `StackLayout` 6
- `Device.GetNamedSize` Метод возвращает числовой размер

Эти размеры не пикселей. Вместо этого они аппаратно независимых единицах, независимо друг от друга распознаются каждой платформы.

## <a name="pixels-points-dps-dips-and-dius"></a>Пиксели, точки, dps, частные интерфейсы и DIUs

На раннем этапе историю, Apple Mac и Microsoft Windows программисты работал в единицах точек. Тем не менее появлением отображает более высоким разрешением требуется более виртуализированных и абстрактный подход в экранные координаты. В мире Mac, программисты работал в единицах *точки*, традиционно 1/72 дюйма при Windows разработчики использовали *аппаратно независимых единицах* (DIUs) на основании 1/96 дюйма.

Мобильные устройства, тем не менее, обычно хранятся намного ближе к поверхности и имеют более высокое разрешение, чем desktop экраны, которые подразумевает, что можно скорректировать большей плотности пикселей.

Программисты, предназначенных для устройств Apple iPhone и iPad по-прежнему работать в единицах *точек*, но есть 160 точек на дюйм. В зависимости от устройства может быть 1, 2 или 3 пикселя в точку.

Аналогично Android. Программисты работают в единицах *density независимых пикселях* (dps), и связь между dps и пикселей основан на 160 dps в дюйме.

Телефоны Windows и мобильными устройствами также разработали коэффициенты масштабирования, которые подразумевают, что-то наподобие 160 аппаратно независимых единиц в дюйме.

> [!NOTE]
> Xamarin.Forms больше не поддерживает любого phone на основе Windows или мобильного устройства.

Таким образом Xamarin.Forms программиста, нацеленного на телефонах и планшетах можно предположить, что все единицы измерения основаны на следующий критерий:

- 160 единиц в дюйме, эквивалентно
- 64 единиц сантиметр

Только для чтения [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) и [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) свойства, определенные `VisualElement` «макет» значения по умолчанию &ndash;1. Только в том случае, если элемент был размера и размещено в макет будут эти свойства отражают фактический размер элемента в аппаратно независимых единицах. Этот размер включает какой-либо `Padding` задавать в элементе, но не `Margin`.

Визуальный элемент срабатывает [ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged) событий при его `Width` или `Height` был изменен. [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) образец использует это событие для отображения размера экрана программы.

## <a name="metrical-sizes"></a>Metrical размеров

[ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) использует [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) и [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) для отображения `BoxView` один дюйм высоту, а второй Расширенный сантиметры.

## <a name="estimated-font-sizes"></a>Размеры шрифтов, оценка

[ **Размеры шрифта** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) примере показано, как использовать 160-единицы дюймов правила для укажите размер шрифта в единицах точек. Визуальная целостность между платформами, при использовании этого метода лучше, чем `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Изменение размера текста по доступный размер

Это возможно в соответствии с путем вычисления блок текста в прямоугольнике, определенного `FontSize` из `Label` используйте следующие критерии:

- Междустрочный интервал — 120% от размера шрифта (130% на платформах Windows).
- Средняя ширина символа — 50% от размера шрифта.

[ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) этот метод продемонстрирован в примере. Эта программа написан до появления [ `Margin` ](xref:Xamarin.Forms.View.Margin) свойство было доступно, поэтому используется [ `ContentView` ](xref:Xamarin.Forms.ContentView) с [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) параметр, чтобы имитировать поле.

[![Тройной снимок предполагаемый размер](images/ch05fg07-small.png "текста по размеру доступный размер")](images/ch05fg07-large.png#lightbox "доступный размер по размеру текста")

## <a name="a-fit-to-size-clock"></a>Часы по размеру размер

[ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) образце показано использование [ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) запустить таймер, периодически уведомляет приложение, когда придет время обновить часы. Размер шрифта будет присвоено Шестая часть ширины страницы, чтобы сделать изображение как можно большего размера.

## <a name="accessibility-issues"></a>Проблемы со специальными возможностями

**EstimatedFontSize** программы и **FitToSizeClock** обе программы содержат слабая недостаток: Если пользователь изменяет параметры специальных возможностей телефона на платформе Android или Windows 10 Mobile, программа больше не можно рассчитать размер текст отображается на основе размера шрифта. [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) образец демонстрирует эту проблему.

## <a name="empirically-fitting-text"></a>Эмпирически помещении текста

Другим способом для соответствия размеру текста в прямоугольник является эмпирически вычислить размер отображаемого текста и настроить его вверх или вниз. Программа в вызовах книги [ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) на визуальный элемент, чтобы получить желаемый размер этого элемента. Метод является устаревшим, что вместо этого необходимо вызвать программы [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Для `Label`, первый аргумент должен быть Ширина контейнера (чтобы упаковки), а для второго аргумента должно быть установлено для `Double.PositiveInfinity` чтобы высота без ограничений. [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) этот метод продемонстрирован в примере.



## <a name="related-links"></a>Связанные ссылки

- [Глава 5 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Примеры Глава 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Глава 5 F# примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
