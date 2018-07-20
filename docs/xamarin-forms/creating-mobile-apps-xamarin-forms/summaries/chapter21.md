---
title: Сводка Глава 21. Transform
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 21. Transform'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 16fcdb345fd9aeb9201a00a0bb98d143e6468f01
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156773"
---
# <a name="summary-of-chapter-21-transforms"></a>Сводка Глава 21. Transform

Xamarin.Forms отображается на экране в расположение и размер определяется своим родительским элементом, который обычно является `Layout` или `Layout<View>` производных продуктов. *Преобразования* — это функция Xamarin.Forms, можно изменить, расположение, размер или даже ориентации.

Xamarin.Forms поддерживает три основных типа преобразований:

- *Перевод* &mdash; сдвинуть элемент по горизонтали или по вертикали
- *Масштаб* &mdash; изменить размер элемента
- *Поворот* &mdash; включить элемент вокруг точки или оси

В Xamarin.Forms масштабирование мощности; Это влияет на ширину и высоту равномерно. Поворот поддерживается как на двухмерную поверхность, экрана и в трехмерном пространстве. Нет, преобразование не неравномерного распределения (или само) и не обобщенного матричное преобразование.

Преобразования поддерживаются с восемь свойств типа `double` определяется `VisualElement` класса:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Все эти свойства обеспечиваются связываемые свойства. Они могли быть целями привязки данных и стили. [**В главе 22 книги. Анимация** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) показано, как эти свойства могут быть анимированы, но в некоторых примерах в этой главе показано, как можно анимировать их с помощью Xamarin.Forms [таймера](~/xamarin-forms/platform/device.md#Device_StartTimer).

Преобразование свойства определяют, как только элемент отображается и выполните *не* влияют на способ, воспринимается ли элемент в макете.

## <a name="the-translation-transform"></a>Преобразования перевода

Ненулевые значения из [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) свойства сдвинуть элемент горизонтально или вертикально.

[ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) программа позволяет вам поэкспериментировать с этими свойствами с двумя `Slider` элементы, которые управляют `TranslationX` и `TranslationY` свойства `Frame`. Преобразование также влияет на все дочерние элементы, `Frame`.

### <a name="text-effects"></a>Текстовые эффекты

Один обычно свойств преобразования используется немного смещения отрисовку текста. Это показано в [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) пример:

[![Тройной снимок смещает текст](images/ch21fg03-small.png "смещает текст")](images/ch21fg03-large.png#lightbox "смещает текст")

Другой эффект заключается в том, для подготовки к просмотру нескольких копий `Label` , чтобы он напоминал блок 3D, такие, как показано в [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) образца.

### <a name="jumps-and-animations"></a>Переходы и анимации

[ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) образец использует преобразование для перемещения `Button` всякий раз, когда он шифрованию, но основной целью является показать, что `Button` получает входные данные пользователя в расположении где Кнопка визуализируется.

[ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) пример аналогичен, но используется таймер для анимации `Button` из одной точки в другую.

## <a name="the-scale-transform"></a>Преобразование масштаба

[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) Преобразования можно увеличить или уменьшить отображаемый размер элемента. Значение по умолчанию — 1. Значение 0 приводит к элемент быть скрытым. Отрицательные значения вызывают элемента могут быть Повернуть на 180 градусов. `Scale` Свойство не влияет на `Width` или `Height` свойств элемента. Эти значения не изменяются.

Вы можете поэкспериментировать с `Scale` свойства с помощью [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) образца.

[ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) в нем демонстрируется разница между анимации `Scale` свойство `Button` и анимации `FontSize` свойство. `FontSize` Свойство влияет на способ `Button` воспринимается в макете; `Scale` не поддерживает свойство.

[ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) пример вычисляет `Scale` свойство, которое применяется к `Label` элемент, чтобы сделать его как можно большего размера при по-прежнему подгонки в пределах страницы.

### <a name="anchoring-the-scale"></a>Привязка шкалы

Элементы масштабирования в предыдущих трех примерах все увеличилось или уменьшилось размер относительно центра элемента. Другими словами элемент увеличивается или уменьшается в размерах одинаковым во всех направлениях. Только точка в центре элемента остается в том же расположении во время масштабирования.

Можно изменить центр масштабирования, задав [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) и [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) свойства. Эти свойства определяются относительно самого элемента. Для `AnchorX`, значение 0 означает левую сторону элемента, а в правую часть 1. Аналогичным образом для `AnchorY`, 0 находится в верхней и нижней является 1. Оба свойства имеют значения по умолчанию 0,5, которая является центром.

[ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) пример позволяет экспериментировать с `AnchorX` и `AnchorY` свойства, а также `Scale` свойство.

В iOS, используя значения по умолчанию из `AnchorX` и `AnchorY` свойства обычно несовместим с изменение ориентации телефона.

## <a name="the-rotation-transform"></a>Преобразование поворота

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) Свойства указываются в градусах и указывает поворот по часовой стрелке вокруг точки элементу, определенному в `AnchorX` и `AnchorY`. [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) позволяет экспериментировать с эти три свойства.

### <a name="rotated-text-effects"></a>Эффектов повернутого текста

[ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) в нем демонстрируется математики, необходимые для Рисование окружности с помощью 64 мало места (повернутый) `BoxView` элементов.

[ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) пример отображает несколько `Label` элементов при помощи ту же текстовую строку (повернутый) как периферийных зон.

[ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) пример отображает строку текста, отображаемый для упаковки в кружке.

### <a name="an-analog-clock"></a>Аналогично часам со стрелками

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека содержит [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) класс, который вычисляет углы для стрелки часов. Чтобы избежать зависимости платформы в ViewModel, а класс использует `Task.Delay` вместо таймера для поиска нового `DateTime` значение.

Также включены в **Xamarin.FormsBook.Toolkit** — [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) класс, реализующий `IValueConverter` и служит для округления второй угол до ближайшей секунды.

[ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) использует три Поворот `BoxView` элементы для рисования аналогично часам со стрелками.

[ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) использует `BoxView` для более сложных рисунков, включая делений помечает вокруг циферблата часов и передает то поворот мало расстоянием от своей:

[![Тройной снимок экрана часов BoxView](images/ch21fg17-small.png "аналоговый циферблата")](images/ch21fg17-large.png#lightbox "аналоговый циферблата")

Кроме [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) в класс **Xamarin.FormsBook.Toolkit** вызывает секундной стрелки будет немного смещают прежде чем продолжить, а затем переместить обратно в правильное положение.

### <a name="vertical-sliders"></a>Вертикальная ползунки?

[ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) образец демонстрирует, что `Slider` элементов может быть повернут на 90 градусов и по-прежнему работают. Тем не менее, трудно поместите эти повернут `Slider` элементы, так как в макете они по-прежнему отображаться горизонтальную.

## <a name="3d-ish-rotations"></a>Постараюсь трехмерных поворотов

[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) Свойство, по-видимому, поворот элемента вокруг трехмерного по оси x, чтобы в верхней и нижней части элемента показаться для перемещения к или от него. Аналогичным образом [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) кажется поворот вокруг оси y, чтобы сделать левую и правую части элемента показаться для перемещения к или от него элемента.

`AnchorX` Свойство влияет на `RotationY` , но не `RotationX`. `AnchorY` Свойство влияет на `RotationX` , но не `RotationY`. Вы можете поэкспериментировать с [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) образец для изучения взаимодействия этих свойств.

Трехмерная система координат содержится в разрешении Xamarin.Forms для левши. При наведении указательным пальцем левой руки по оси x, увеличение координирует (справа), и координирует пальца среднего в направлении возрастающий Y (вниз), затем все точки бегунок в направление увеличения координаты Z (выходит из экрана).

Кроме того для любого из трех осей, при наведении на левом бегунок в направление увеличения значения, то кривая одним касанием указывает направление поворота положительные значения угла поворота.



## <a name="related-links"></a>Связанные ссылки

- [Глава 21 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Глава 21-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
