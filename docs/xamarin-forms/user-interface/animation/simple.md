---
title: Простая анимация в Xamarin.Forms
description: Класс ViewExtensions предоставляет методы расширения, которые могут использоваться для создания простой анимации. В этой статье демонстрирует создание и Отмена с помощью класса ViewExtensions анимации.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 124fc311d5e2c8c89353ba813df60f0bf1d0b34a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997075"
---
# <a name="simple-animations-in-xamarinforms"></a>Простая анимация в Xamarin.Forms

_Класс ViewExtensions предоставляет методы расширения, которые могут использоваться для создания простой анимации. В этой статье демонстрирует создание и Отмена с помощью класса ViewExtensions анимации._


[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) Класс предоставляет следующие методы расширения, которые могут использоваться для создания простой анимации:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимирует [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) свойства [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) анимирует [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) применяет уменьшаться или анимированных добавочное увеличение числа [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимирует [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) применяет уменьшаться или анимированных добавочное увеличение числа [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимирует [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимирует [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) анимирует [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) свойство [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

По умолчанию каждый анимация займет 250 миллисекунд. Тем не менее срок действия каждой анимации могут быть заданы при создании анимации.

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) Класс также включает [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) метод, который может использоваться для отмены любые анимации.

> [!NOTE]
> [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) Класс предоставляет [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) метода расширения. Тем не менее этот метод предназначен для использования макетов анимировать переходы между состояниями макета, содержащих размер и положение изменения. Таким образом, его следует использовать только с [ `Layout` ](xref:Xamarin.Forms.Layout) подклассы.

Методы расширения анимации в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класса являются все асинхронными и возврата `Task<bool>` объекта. Возвращает значение `false` при завершении анимации, и `true` при отмене анимации. Таким образом, методы анимации обычно применяется с `await` оператор, который позволяет легко определить, после завершения анимации. Кроме того он затем становится возможным создание последовательного анимации с помощью следующей анимации методы для выполнения после завершения предыдущего метода. Дополнительные сведения см. в разделе [составной анимации](#compound).

Если требуется, чтобы позволить анимации завершения в фоновом режиме, а затем `await` оператор можно опустить. В этом случае методы расширения анимации быстро вернуться после начала анимации с анимацией, выполняемых в фоновом режиме. Эту операцию следует выполнить преимущества при создании составного анимации. Дополнительные сведения см. в разделе [составной анимации](#composite).

Дополнительные сведения о `await` оператора, см. в разделе [обзор поддержки асинхронного](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Одной анимации

Каждый метод расширения в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) реализует операцию одной анимации, постепенно изменяет свойство от одного значения на другое значение за период времени. В данном разделе изучаются каждой операции анимации.

### <a name="rotation"></a>Вращение

В следующем примере кода показано использование [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод для анимации [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра, повернув более 2 секунд (2000 миллисекунд) до 360 градусов. [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод получает текущий [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство значение в начале анимации, а затем поворачивается от этого значения первого аргумента (360). После ее завершения, изображения [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство сбрасывается на 0. Это гарантирует, что `Rotation` свойство не остался в 360 после завершения анимации, которой не позволило бы дополнительных поворотов.

На следующих снимках экрана показано поворот выполняется на каждой платформе:

![](simple-images/rotateto.png "Анимация поворота")

### <a name="relative-rotation"></a>Относительный поворот

В следующем примере кода показано использование [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод, чтобы постепенно увеличить или уменьшить [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра, повернув 360 градусов из его начальной позиции более 2 секунд (2000 миллисекунд). [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод получает текущий [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство значение в начале анимации, а затем поворачивается от этого значения значению плюс первого аргумента (360). Это гарантирует, что каждой анимации всегда будет иметь поворот 360 градусов от начальной позиции. Таким образом Если анимацию вызывается во время анимации уже выполняется, он запустится из текущей позиции и может завершаться в позицию, которая не является шагом 360 градусов.

На следующих снимках экрана показано относительный поворот выполняется на каждой платформе:

![](simple-images/relrotateto.png "Анимация относительный вращения")

### <a name="scaling"></a>Масштабирование

В следующем примере кода показано использование [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) метод для анимации [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра, увеличив масштаб в два раза больше в течение 2 секунд (2000 миллисекунд). [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) Метод получает текущий [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) значение свойства (значение по умолчанию 1) в начале анимации и затем масштабируется от этого значения для первого аргумента (2). Это приводит к увеличением размера изображения в два раза больше.

На следующих снимках экрана показано, масштабирование выполняется на каждой платформе:

![](simple-images/scaleto.png "Масштабирование анимации")

### <a name="relative-scaling"></a>Масштабирование относительно

В следующем примере кода показано использование [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод для анимации [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра, увеличив масштаб в два раза больше в течение 2 секунд (2000 миллисекунд). [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод получает текущий [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) значение свойства для начала анимации и затем масштабирование от этого значения до значения, а также первого аргумента (2). Это гарантирует, что каждой анимации всегда будет масштабирования 2 от начальной позиции.

### <a name="scaling-and-rotation-with-anchors"></a>Масштабирования и поворота с помощью привязки

[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) И [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) свойства задать центр масштабирования или поворота для [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) и [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойства. Таким образом, их значения также влияют на [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) и [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) методы.

Учитывая [ `Image` ](xref:Xamarin.Forms.Image) , размещена в центре макета, в следующем примере кода демонстрируется смена изображения вокруг центра макета, задав его [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) свойство:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Для поворота [ `Image` ](xref:Xamarin.Forms.Image) экземпляр вокруг центра макета, [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) и [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) свойства должно быть присвоено величин относительно ширины и высоты `Image`. В этом примере центр `Image` определяется как в центре макет и поэтому по умолчанию `AnchorX` значение 0,5 изменять не обязательно. Тем не менее `AnchorY` свойство переопределяется значением в верхней части `Image` к центральной точке макета. Это гарантирует, что `Image` выполняет полный поворот 360 градусов относительно центральной точки макета, как показано на следующем снимке экрана:

![](simple-images/rotate-anchors.png "Анимация поворота с помощью привязки")

### <a name="translation"></a>Перевод

В следующем примере кода показано использование [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод для анимации [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) свойства [ `Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра путем преобразования его по горизонтали и вертикали более 1 секунды (1000 миллисекунд). [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод одновременно преобразует пиксели изображения 100 влево и вверх 100 пикселей. Это, поскольку первый и второй аргументы обоих отрицательные числа. Предоставляя положительных чисел преобразуют изображение вправо и вниз.

На следующих снимках экрана показано преобразование выполняется на каждой платформе:

![](simple-images/translateto.png "Перевод анимации")

> [!NOTE]
> Если элемент является изначально изложенные вне экрана, а преобразуется к экрану, после перевода входной макет элемента остается вне экрана, и пользователь не может взаимодействовать с ним. Таким образом рекомендуется, что представления должен быть размещен в его последней позицией, а затем любые необходимые выполняемые преобразования.

### <a name="fading"></a>Исчезание

В следующем примере кода показано использование [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод для анимации [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) свойство [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Этот код выполняет анимацию [ `Image` ](xref:Xamarin.Forms.Image) экземпляра путем: плавный переход на более чем 4 секунды (4000 миллисекунд). [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод получает текущий [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) значение свойства для начала анимации и затем затухание в от этого значения для первого аргумента (1).

На следующих снимках экрана показано исчезания выполняется на каждой платформе:

![](simple-images/fadeto.png "Постепенное исчезновение анимации")

<a name="compound" />

## <a name="compound-animations"></a>Составной анимации

Составной анимации представляет собой последовательный сочетание анимации и могут создаваться с помощью `await` оператор, как показано в следующем примере кода:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

В этом примере [ `Image` ](xref:Xamarin.Forms.Image) переводится более чем 6 секунд (6000 миллисекунд). Перевод `Image` использует пять анимации с `await` оператор, указывающий, последовательно выполняет каждой анимации. Таким образом методы следующей анимации выполняться после завершения предыдущего метода.

<a name="composite" />

## <a name="composite-animations"></a>Составной анимации

Составной анимации представляет собой сочетание анимации, где одновременно запускать два или несколько анимаций. Составной анимации могут создаваться при совмещении ожидаемой и не ожидать анимации, как показано в следующем примере кода:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

В этом примере [ `Image` ](xref:Xamarin.Forms.Image) масштабируется и одновременно поворачивать более чем 4 секунды (4000 миллисекунд). Масштабирование `Image` использует два последовательных анимации, возникших примерно в то же время, что поворот. [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Метод выполняется без `await` оператор и сразу же, что и первый [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) затем начала анимации. `await` Оператор на первом `ScaleTo` второй задерживает вызов метода `ScaleTo` вызов метода до первой `ScaleTo` завершения вызова метода. На этом этапе `RotateTo` анимации составляет половину способом завершения и `Image` будет Повернуть на 180 градусов. В течение последних 2 секунд (2000 миллисекунд) второй `ScaleTo` анимации и `RotateTo` анимации и завершения.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Параллельное выполнение нескольких асинхронных методов

`static` `Task.WhenAny` И `Task.WhenAll` методы используются для одновременного запуска нескольких асинхронных методов и таким образом, можно использовать для создания составного анимаций. Оба метода возвращают `Task` объекта и принять коллекцию методов, чтобы каждый возвращаемое `Task` объекта. `Task.WhenAny` Завершения метода, когда любой метод в свою коллекцию завершает выполнение, как показано в следующем примере кода:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

В этом примере `Task.WhenAny` вызов метода содержит две задачи. Первая задача поворачивает изображение на более чем 4 секунды (4000 миллисекунд), а вторая задача масштабирует изображение более 2 секунд (2000 миллисекунд). По завершении вторая задача `Task.WhenAny` завершения вызова метода. Тем не менее несмотря на то что [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) метод по-прежнему выполняется, второй [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) метод начинает.

`Task.WhenAll` Завершения метода, когда завершены все методы в его коллекции, как показано в следующем примере кода:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

В этом примере `Task.WhenAll` вызов метода содержит три задачи, каждая из которых выполняет более 10 минут. Каждый `Task` делает разное число поворотов всеохватывающее — 307 поворотов для [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 поворотов для [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))и 199 поворотов для [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Эти значения являются простых чисел, таким образом гарантируя, что смена не синхронизированы и поэтому не ведут повторяющихся шаблонов.

На следующих снимках экрана показано несколько поворотов выполняется на каждой платформе:

![](simple-images/multiple-rotations.png "Составной анимации")

## <a name="canceling-animations"></a>Отмена анимации

Приложения можно отменить одну или несколько анимаций с помощью вызова `static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) метод, как показано в следующем примере кода:

```csharp
ViewExtensions.CancelAnimations (image);
```

Немедленно, эта операция отменит все анимации, запущенных на [ `Image` ](xref:Xamarin.Forms.Image) экземпляра.

## <a name="summary"></a>Сводка

В этой статье демонстрируется создание и Отмена анимаций с помощью [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класса. Этот класс предоставляет методы расширения, которые могут использоваться для создания простой анимации, поворот, масштабирование, перевод и скрывать [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) экземпляров.


## <a name="related-links"></a>Связанные ссылки

- [Общие сведения о поддержке асинхронного выполнения](~/cross-platform/platform/async.md)
- [Простая анимация (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
