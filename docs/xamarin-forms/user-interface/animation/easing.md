---
title: Функции плавности в Xamarin.Forms
description: Платформа Xamarin.Forms включает класс, стирание, который позволяет указать функции передачи, который определяет, как ускорить или замедлить работу, так как они выполняются в анимации. В этой статье показано, как использовать предварительно определенных функций плавности, а также как создавать пользовательские функции плавности.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 211f56e0d9f96383670be1d60421d3ac28eabe46
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052000"
---
# <a name="easing-functions-in-xamarinforms"></a>Функции плавности в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)

_Платформа Xamarin.Forms включает класс, стирание, который позволяет указать функции передачи, который определяет, как ускорить или замедлить работу, так как они выполняются в анимации. В этой статье показано, как использовать предварительно определенных функций плавности, а также как создавать пользовательские функции плавности._


[ `Easing` ](xref:Xamarin.Forms.Easing) Класс определяет ряд функций плавности, которые могут быть использованы анимации:

- [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn) Функция для реалистичной анимации подпрыгивает анимации в начале.
- [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut) Функция для реалистичной анимации подпрыгивает анимации в конце.
- [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn) Функция для реалистичной анимации медленно ускоряет анимации.
- [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut) Функция для реалистичной анимации ускоряет анимации в начале и замедляется по завершении анимации.
- [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut) Функция для реалистичной анимации быстро замедляется анимации.
- [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) Функция для реалистичной анимации используется постоянной скоростью, и является функцией плавности по умолчанию.
- [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn) Функция для реалистичной анимации плавно ускоряет анимации.
- [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut) Функция для реалистичной анимации плавно ускоряет анимации в начале и плавно замедляется анимации в конце.
- [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut) Функция для реалистичной анимации плавно замедляется анимации.
- [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) Функция для реалистичной анимации приводит к очень быстро ускорения в конце анимации.
- [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) Функция для реалистичной анимации вызывает быстро замедление в конце анимации.

`In` И `Out` суффиксы указывается, является ли заметно в начале анимации, в конце, так и в силу, предоставляемые функцией плавности.

Кроме того можно создать пользовательские функции плавности. Дополнительные сведения см. в разделе [функции плавности Custom](#customeasing).

## <a name="consuming-an-easing-function"></a>Использование функции плавности

Методы расширения анимации в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) класс позволяет функции плавности к указанным в качестве параметра последний метод, как показано в следующем примере кода:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Указав функцию для реалистичной анимации, скорость анимации становится нелинейных и создает эффект, предоставляемые функцией плавности. Пропуск функцию плавности, при создании анимации приводит к анимации использовать значение по умолчанию [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) функции плавности, которая создает линейную скорость.

Дополнительные сведения об использовании методов расширения анимации в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) , представлена в разделе [простой анимации](~/xamarin-forms/user-interface/animation/simple.md). Функции плавности могут также использоваться [ `Animation` ](xref:Xamarin.Forms.Animation) класса. Дополнительные сведения см. в разделе [Настройка анимации](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Пользовательские функции плавности

Существует три основных подхода к созданию пользовательской функции плавности:

1. Создание метода, принимающего `double` аргумента и возвращает `double` результат.
1. Создайте таблицу `Func<double, double>`.
1. Укажите в качестве аргумента для функции плавности [ `Easing` ](xref:Xamarin.Forms.Easing) конструктор.

Во всех трех случаях пользовательской функции плавности должен возвращать 0 для аргумента 0 и 1 для аргумента 1. Тем не менее любое значение могут быть возвращены значения аргументов 0 и 1. Каждый подход теперь будут рассматриваться в свою очередь.

### <a name="custom-easing-method"></a>Пользовательский метод для реалистичной анимации

Функции плавности могут быть определены как метода, принимающего `double` аргумента и возвращает `double` привести, как показано в следующем примере кода:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

`CustomEase` Метод усекает входящее значение для значения 0, 0,2, 0,4, 0.6, 0,8 и 1. Таким образом [ `Image` ](xref:Xamarin.Forms.Image) экземпляр преобразуется в дискретных переходов, а не плавно.

### <a name="custom-easing-func"></a>Custom, облегчая Func

Также можно определить пользовательскую функцию плавности как `Func<double, double>`, как показано в следующем примере кода:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

`CustomEase` `Func` Представляет функцию плавности, которая начинается быстро, замедляется и обращает курс и перестановка снова курс для ускорения быстро в конце. Следовательно, тогда как общую перемещение [ `Image` ](xref:Xamarin.Forms.Image) экземпляр вниз, он также временно обращает курс в середине анимации.

### <a name="custom-easing-constructor"></a>Пользовательский конструктор для реалистичной анимации

Также можно определить в качестве аргумента для функции плавности [ `Easing` ](xref:Xamarin.Forms.Easing) конструктора, как показано в следующем примере кода:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

Функции плавности указан как аргумент функции лямбда-выражения для [ `Easing` ](xref:Xamarin.Forms.Easing) конструктора, а также используется `Math.Cos` метод для создания медленно эффекта, притормаживается по `Math.Exp` метод. Таким образом [ `Image` ](xref:Xamarin.Forms.Image) экземпляр преобразуется, чтобы он отображался уменьшится до место окончательного хранения.

## <a name="summary"></a>Сводка

В этой статье показано, как использовать предварительно определенных функций плавности, а также как создавать пользовательские функции плавности. Платформа Xamarin.Forms включает [ `Easing` ](xref:Xamarin.Forms.Easing) класс, который позволяет указать функции передачи, управляющий как анимации ускорить или замедлить работу, так как они выполняются.



## <a name="related-links"></a>Связанные ссылки

- [Общие сведения о поддержке асинхронного выполнения](~/cross-platform/platform/async.md)
- [Функции плавности (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Замедление](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
