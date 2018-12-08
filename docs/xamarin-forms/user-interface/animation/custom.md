---
title: Пользовательской анимации в Xamarin.Forms
description: В этой статье показано, как использовать класс анимации Xamarin.FOrms для создания и Отмена анимации, синхронизации нескольких анимаций и создание анимации, анимации свойств, не анимировано в существующие методы анимации.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 157e044fd96cdeff87d8fb56029fe625b7312bf4
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056244"
---
# <a name="custom-animations-in-xamarinforms"></a>Пользовательской анимации в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)

_Класс анимации является стандартным блоком все Xamarin.Forms анимации, с помощью методов расширения в классе ViewExtensions, создание одного или нескольких объектов анимации. В этой статье показано, как использовать класс анимации для создания и Отмена анимации, синхронизации нескольких анимаций и создание анимации, анимации свойств, не анимировано в существующие методы анимации._


Число параметров должно быть указано при создании `Animation` объекта, включая начальное и конечное значения анимируемого свойства и обратный вызов, который изменяет значение свойства. `Animation` Объект также может поддерживать коллекцию дочерних анимаций, которые можно выполнить и синхронизированы. Дополнительные сведения см. в разделе [дочерних анимациях](#child).

Выполнение анимации, созданные с помощью [ `Animation` ](xref:Xamarin.Forms.Animation) класс, который может включать или не включать дочерние анимации, производится путем вызова [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) метод. Этот метод задает длительность анимации и среди других элементов, обратный вызов, который определяет, следует ли повторять анимацию.

## <a name="creating-an-animation"></a>Создание анимации

При создании [ `Animation` ](xref:Xamarin.Forms.Animation) объекта, как правило, менее трех параметров являются обязательными, как показано в следующем примере кода:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Этот код определяет анимацию [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `Image` ](xref:Xamarin.Forms.Image) экземпляр из значения 1 значение 2. Анимированное значение, которое является производным, Xamarin.Forms, передается методу обратного вызова, заданный как первый аргумент, где он используется для изменения параметра `Scale` свойство.

Анимация начинается с вызова [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) метод, как показано в следующем примере кода:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Обратите внимание, что [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) метод не возвращает `Task` объекта. Вместо этого уведомления предоставляются через методы обратного вызова.

Следующие аргументы указываются в `Commit` метод:

- Первый аргумент (*владельца*) определяет владельца анимации. Это может быть визуальный элемент, к которому применяется анимация, или другой визуальный элемент, например с помощью страницы.
- Второй аргумент (*имя*) определяет анимацию с именем. Имя объединяется с владельцем для уникальной идентификации анимации. Этот уникальный код затем может использоваться для определения, выполняется ли анимация ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))), или отмените его ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- Третий аргумент (*скорость*) указывает время в миллисекундах между вызовами метода обратного вызова, определенный в [ `Animation` ](xref:Xamarin.Forms.Animation) конструктор
- Четвертый аргумент (*длина*) указывает длительность анимации, в миллисекундах.
- Пятый аргумент (*плавности*) определяет функцию плавности для использования в анимации. Кроме того, функция плавности можно указать как аргумент [ `Animation` ](xref:Xamarin.Forms.Animation) конструктор. Дополнительные сведения о функции плавности, см. в разделе [функции плавности](~/xamarin-forms/user-interface/animation/easing.md).
- Шестой аргумент (*завершения*) — это обратный вызов, который будет выполняться при завершении анимации. Этот обратный вызов принимает два аргумента, с первым аргументом, указывающее, окончательное значение, а второй аргумент, `bool` установки `true` если анимация была отменена. Кроме того *завершения* обратного вызова можно указать в качестве аргумента для [ `Animation` ](xref:Xamarin.Forms.Animation) конструктор. Тем не менее, с помощью одной анимации Если *завершения* обратных вызовов указанных в свойствах `Animation` конструктор и `Commit` только обратного вызова, указанного в методе `Commit` метод будет выполняться.
- Седьмой аргумент (*повторите*) — это обратный вызов, который позволяет повторить анимацию. Он вызывается в конце анимации и возврат `true` указывает, что анимация должна повторяться.

Общий эффект — создать анимацию, которая увеличивает [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство [ `Image` ](xref:Xamarin.Forms.Image) от 1 до 2, в течение 2 секунд (2000 миллисекунд), с помощью [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) функция для реалистичной анимации. Каждый раз при завершении анимации, его `Scale` свойство сбрасывается в 1 и повторении анимации.

> [!NOTE]
> Параллельные анимации, под управлением независимо друг от друга, которые могут создаваться путем создания `Animation` для каждой анимации объекта и последующего вызова `Commit` метод для каждой анимации.

<a name="child" />

### <a name="child-animations"></a>Дочерних анимациях

[ `Animation` ](xref:Xamarin.Forms.Animation) Класс также поддерживает дочерних анимациях, который включает в себя создание `Animation` объект для других `Animation` объекты будут добавлены. Это позволяет ряд анимации должен быть запущен и синхронизированы. В следующем примере кода показано создание и запуск дочерних анимациях:

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Кроме того в примере кода можно написать более кратко, как показано в следующем примере кода:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

В обоих примерах кода родительского [ `Animation` ](xref:Xamarin.Forms.Animation) создается объект, к которому дополнительных `Animation` объекты затем будут добавлены. Первые два аргумента для [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) метод укажите время начала и окончания анимации дочерних. Значения аргументов должен находиться в диапазоне от 0 до 1 и представляют относительный период в пределах продолжительности анимации родительского указанного дочернего анимация будет активна. Таким образом, в этом примере `scaleUpAnimation` будут активными для первой половины анимации, `scaleDownAnimation` будут активными для второй половины анимации и `rotateAnimation` будет активна в течение всего периода.

Общий эффект является то, что анимация возникает более чем 4 секунды (4000 миллисекунд). `scaleUpAnimation` Анимирует [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство от 1 до 2, в течение 2 секунд. `scaleDownAnimation` Затем выполняет анимацию постепенно `Scale` свойство от 2 до 1, в течение 2 секунд. Хотя оба шкалы анимации, `rotateAnimation` анимирует [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) свойство от 0 до 360, более чем 4 секунды. Обратите внимание на то, что анимаций также использовать функции плавности. [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) Функция для реалистичной анимации вызывает [ `Image` ](xref:Xamarin.Forms.Image) изначально сжатие перед получением большего размера и [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) функция для реалистичной анимации вызывает `Image` станет меньше, чем его фактический размер, в конце полная анимация.

Существует ряд различий между [ `Animation` ](xref:Xamarin.Forms.Animation) объект, который использует дочерних анимациях, а второе не:

- При использовании дочерних анимациях *завершения* обратного вызова на анимации дочерних указывает на завершение дочерних и *завершения* обратного вызова передается `Commit` указывает метод, когда завершения всей анимации.
- При использовании дочерних анимациях, возвращая `true` из *повторите* обратного вызова на `Commit` не приведет к метод повтор анимации, но анимация будет продолжать работать без новые значения.
- При включении функции плавности в `Commit` метод и функцией плавности возвращает значение больше 1, анимация завершается. Если функция плавности возвращает значение меньше 0, значение присваивается значение 0. Чтобы использовать функцию плавности, которая возвращает значение меньше 0 или больше 1, он должен указан в одном из дочерних анимациях, а не в `Commit` метод.

[ `Animation` ](xref:Xamarin.Forms.Animation) Класс также включает [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) методы, которые можно использовать для добавления к родительскому элементу дочерних анимациях `Animation` объекта. Тем не менее их *начать* и *Готово* значения аргументов не ограничено только 0, 1, но только эту часть дочерних анимацию, которая соответствует диапазону от 0 до 1 будет активным. Например если `WithConcurrent` вызов метода определяет анимированный дочерних, предназначенного [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство от 1 до 6, но с *начать* и *Готово* значения -2 и 3, *начать* значение -2 соответствует `Scale` значение 1 и *Готово* значение 3 соответствует `Scale` значение 6. Так как значения вне диапазона от 0 до 1 не принимают участия в анимации, `Scale` свойства только анимируется от 3 до 6.

## <a name="canceling-an-animation"></a>Отмена анимации

Приложения можно отменить анимации с помощью вызова [ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) метод расширения, как показано в следующем примере кода:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Обратите внимание на то, что анимации однозначно идентифицируются сочетанием анимации владельца и имя анимации. Таким образом владельца и имя указано, когда выполнение анимации должен быть указан для отмены анимации. Таким образом, в примере кода будет немедленно отменить анимацию с именем `SimpleAnimation` , владельцем которой является страницы.

## <a name="creating-a-custom-animation"></a>Создание пользовательской анимации

Примеры, приведенные выше показали анимаций, столь же можно достичь с помощью методов в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класса. Однако преимущество [ `Animation` ](xref:Xamarin.Forms.Animation) класс является то, что он имеет доступ к метод обратного вызова, который выполняется при изменении анимированное значение. Это позволяет обратному вызову реализации любой требуемой анимации. Например, в следующем примере кода анимирует [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) свойства страницы, присвоив ему значение [ `Color` ](xref:Xamarin.Forms.Color) значениями, созданными [ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))метода с оттенок значения от 0 до 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

Результирующая анимация предоставляет внешний вид смещения фона страницы через цвета радуги.

Дополнительные примеры создания сложных анимаций, включая анимированный значок кривой Безье, см. в разделе [в главе 22 книги](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) из [Создание мобильных приложений с помощью Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Создание метода расширения пользовательской анимации

Методы расширения в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класс анимации свойства текущее значение к указанному значению. Это затрудняет для создания, например, `ColorTo` анимации метод, который может использоваться для анимации цвета из одного значения к другому, так как:

- Единственным [ `Color` ](xref:Xamarin.Forms.Color) свойство, определенное [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) класс является [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), не всегда нужный `Color` свойство для анимации.
- Часто текущее значение [ `Color` ](xref:Xamarin.Forms.Color) свойство [ `Color.Default` ](xref:Xamarin.Forms.Color.Default), который не является реальной цвет и который не может использоваться в вычислениях интерполяции.

Решение этой проблемы заключается в нет `ColorTo` метод предназначенных конкретным [ `Color` ](xref:Xamarin.Forms.Color) свойство. Вместо этого его можно создать с методом обратного вызова, который передает интерполированные `Color` обратно в вызывающий объект. Кроме того, метод принимает начала и окончания `Color` аргументы.

`ColorTo` Метод может быть реализован как метод расширения, который использует [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) метод в [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) класса для выполнения его функций. Это обусловлено `Animate` метод может использоваться для свойства целевого объекта, не относящиеся к типу `double`, как показано в следующем примере кода:

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) Метод требует *преобразования* аргументом, который является методом обратного вызова. Входные данные для этой функции обратного вызова — всегда `double` от 0 до 1. Таким образом `ColorTo` метод определяет свои собственные преобразования `Func` , принимающий `double` от 0 до 1, а возвращает [ `Color` ](xref:Xamarin.Forms.Color) значение, соответствующее значение. `Color` Значение вычисляется путем интерполяции [ `R` ](xref:Xamarin.Forms.Color.R), [ `G` ](xref:Xamarin.Forms.Color.G), [ `B` ](xref:Xamarin.Forms.Color.B), и [ `A` ](xref:Xamarin.Forms.Color.A) значения двух предоставленный `Color` аргументы. `Color` Значение затем передается в метод обратного вызова для приложения для конкретного свойства.

Такой подход позволяет `ColorTo` метод анимировать любой [ `Color` ](xref:Xamarin.Forms.Color) свойства, как показано в следующем примере кода:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

В этом примере кода `ColorTo` анимирует метод [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) и [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) свойства [ `Label` ](xref:Xamarin.Forms.Label), `BackgroundColor`свойства страницы и [ `Color` ](xref:Xamarin.Forms.BoxView.Color) свойство [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать [ `Animation` ](xref:Xamarin.Forms.Animation) класс для создания и Отмена анимации, синхронизации нескольких анимаций и создание анимации, анимации свойств, не анимируется с существующей анимации методы. `Animation` Класс является стандартным блоком все Xamarin.Forms анимации.


## <a name="related-links"></a>Связанные ссылки

- [Пользовательской анимации (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [Анимация](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
