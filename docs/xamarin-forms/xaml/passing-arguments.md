---
title: Передача аргументов в XAML
description: В этой статье демонстрируется использование атрибутов XAML, которые могут использоваться для передачи аргументов конструктора не по умолчанию, для вызова методов фабрики, а также указать тип универсального аргумента.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 60ca55999033adc375a139195c9c3e62da6d0184
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563762"
---
# <a name="passing-arguments-in-xaml"></a>Передача аргументов в XAML

_В этой статье демонстрируется использование атрибутов XAML, которые могут использоваться для передачи аргументов конструктора не по умолчанию, для вызова методов фабрики, а также указать тип универсального аргумента._

## <a name="overview"></a>Обзор

Часто бывает необходимо создавать экземпляры объектов с помощью конструкторов, которые требуют аргументов или путем вызова метода статического создания. Это можно сделать в XAML с помощью `x:Arguments` и `x:FactoryMethod` атрибуты:

- `x:Arguments` Атрибута можно задать аргументы конструктора для конструктора не по умолчанию, или объект объявления метода фабрики. Дополнительные сведения см. в разделе [передав аргументы конструктора](#constructor_arguments).
- `x:FactoryMethod` Атрибут используется для указания фабричный метод, который может использоваться для инициализации объекта. Дополнительные сведения см. в разделе [вызов методов фабрики](#factory_methods).

Кроме того `x:TypeArguments` атрибут может использоваться для указания аргументов универсального типа в конструктор универсального типа. Дополнительные сведения см. в разделе [указания аргумента универсального типа](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Передача аргументов конструктора

Можно передать аргументы конструктора не по умолчанию с помощью `x:Arguments` атрибута. Каждый аргумент конструктора должны быть заключены в XML-элемент, представляющий тип аргумента. Xamarin.Forms поддерживает следующие элементы для основных типов:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

В следующем примере кода показано использование `x:Arguments` атрибут с тремя [ `Color` ](xref:Xamarin.Forms.Color) конструкторы:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Число элементов в пределах `x:Arguments` тегов и типы из этих элементов должен соответствовать одному из [ `Color` ](xref:Xamarin.Forms.Color) конструкторы. `Color` [Конструктор](xref:Xamarin.Forms.Color.%23ctor(System.Double)) с одним параметром требует оттенки серого значение от 0 (черный) до 1 (белый). `Color` [Конструктор](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) с тремя параметрами требует красного, зеленого и синего значение в диапазоне от 0 до 1. `Color` [Конструктор](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) с четырьмя параметрами добавляет альфа-канал в качестве четвертого параметра.

На следующих снимках экрана показано результат вызова каждой [ `Color` ](xref:Xamarin.Forms.Color) конструктор с заданным аргументом значениями:

![](passing-arguments-images/passing-arguments.png "BoxView.Color сопровождается x: Arguments")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Обращение к методам фабрики

Фабричные методы можно вызывать в XAML путем указания метода имя, используя `x:FactoryMethod` атрибута и его аргументов, с помощью `x:Arguments` атрибута. Метод фабрики является `public static` метод, который возвращает объекты или значения совпадает с типом класса или структуры, который определяет методы.

[ `Color` ](xref:Xamarin.Forms.Color) Структура определяет ряд методов фабрики, а также в следующем примере кода демонстрируется вызывающий три из них:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

Число элементов в пределах `x:Arguments` тегов и типы из этих элементов должен соответствовать аргументы вызываемого метода фабрики. [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) Фабричный метод требует четыре [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) параметры, которые представляют значения красного, зеленого, синего и альфа-канала, в диапазоне от 0 до 255, соответственно. [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) Фабричный метод требует четыре [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) параметры, которые представляют оттенок, насыщенность, яркость и альфа-значения, в диапазоне от 0 до 1, соответственно. [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) Фабричный метод требует [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) , представляющий шестнадцатеричное число (A) цвета RGB.

На следующих снимках экрана показано результат вызова каждой [ `Color` ](xref:Xamarin.Forms.Color) фабричный метод с заданным аргументом значениями:

![](passing-arguments-images/factory-methods.png "BoxView.Color сопровождается x: FactoryMethod- и x: Arguments")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Указание аргумента универсального типа

Аргументы универсального типа для универсального типа конструктора можно задать с помощью `x:TypeArguments` атрибута, как показано в следующем примере кода:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) Класс — это универсальный класс и должен быть создан при помощи `x:TypeArguments` атрибут, который соответствует целевому типу. В [ `On` ](xref:Xamarin.Forms.On) класс, [ `Platform` ](xref:Xamarin.Forms.On.Platform) атрибут может принимать один `string` значение или несколько разделенных запятыми `string` значения. В этом примере [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin) свойству конкретной платформы [ `Thickness` ](xref:Xamarin.Forms.Thickness).

## <a name="summary"></a>Сводка

В этой статье показано, с помощью атрибутов XAML, которые могут использоваться для передачи аргументов конструктора не по умолчанию, для вызова методов фабрики, а также указать тип универсального аргумента.


## <a name="related-links"></a>Связанные ссылки

- [Пространства имен языка XAML](~/xamarin-forms/xaml/namespaces.md)
- [Передача аргументов конструктора (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [Обращение к методам фабрики (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
