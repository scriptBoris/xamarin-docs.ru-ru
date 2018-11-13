---
title: Вложенные свойства
description: В этой статье содержатся вводные присоединенных свойств и показано, как создавать и использовать их.
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: f9bd7e5be26b4721abe6756ecb6c5ff387c6f5e8
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563385"
---
# <a name="attached-properties"></a>Вложенные свойства

_Присоединенное свойство — это специальный тип привязки свойства, определенные в одном классе, но присоединен к другим объектам и распознается в XAML как атрибут, содержащий класс и имя свойства, разделенные точкой. В этой статье содержатся вводные присоединенных свойств и показано, как создавать и использовать их._

## <a name="overview"></a>Обзор

Присоединенные свойства enable объекта для присвоения значения для свойства, которое не определяет свой собственный класс. Например дочерние элементы можно использовать вложенные свойства для информирования родительского элемента о порядке их представления в пользовательском интерфейсе. [ `Grid` ](xref:Xamarin.Forms.Grid) Элемент управления позволяет строки и столбца дочернего элемента для путем задания `Grid.Row` и `Grid.Column` присоединенных свойств. `Grid.Row` и `Grid.Column` являются присоединенных свойств, так как они устанавливаются на элементы, являющиеся дочерними для `Grid`, а не для `Grid` сам.

Привязываемые свойства должны быть реализованы как присоединенные свойства в следующих сценариях:

- При отсутствии необходимо иметь свойство параметр механизм доступен для классов, отличных от определяющего класса.
- Когда класс представляет службу, которая легко интегрируется с другими классами.

Дополнительные сведения о свойствах привязки см. в разделе [привязываемые свойства](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="creating-and-consuming-an-attached-property"></a>Создание и использование вложенного свойства

Процесс создания присоединенное свойство выглядит следующим образом:

1. Создание [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляра с одним из [ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*) перегрузок метода.
1. Укажите `static` `Get` *PropertyName* и `Set` *PropertyName* методов как методы доступа для вложенных свойств.

### <a name="creating-a-property"></a>Создание свойства

При создании присоединенного свойства для использования на других типов, класса, в которой создается свойство не являются производными от [ `BindableObject` ](xref:Xamarin.Forms.BindableObject). Тем не менее *целевой* должен иметь свойство для методов доступа, или производным от него [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

Присоединенное свойство могут создаваться путем объявления `public static readonly` свойство типа [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). Возвращаемое значение одного из задается свойство, используемое [ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) перегрузок метода. Объявление должно быть в теле класса-владельца, но вне определения любого из членов.

Следующий код является примером присоединенное свойство:

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

Это создает присоединенное свойство с именем `HasShadow`, типа `bool`. Свойство принадлежит `ShadowEffect` класса, и имеет значение по умолчанию `false`. Соглашение об именовании для присоединенных свойств является, что идентификатор присоединенного свойства должен соответствовать имени свойства, указанные в `CreateAttached` метод, с добавленным к нему «Property». Таким образом, в приведенном выше примере идентификатор присоединенного свойства является `HasShadowProperty`.

Дополнительные сведения о создании привязываемые свойства, включая параметры, которые могут быть указаны во время создания, см. в разделе [создания и использования может быть привязано](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property).

### <a name="creating-accessors"></a>Создание методов доступа

Статические `Get` *PropertyName* и `Set` *PropertyName* методы необходимы как методы доступа для присоединенного свойства, в противном случае будет невозможно использовать в системе свойств вложенное свойство. `Get` *PropertyName* доступа должны соответствовать следующую сигнатуру:

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName* метод доступа должен возвращать значение, которое содержится в соответствующем `BindableProperty` для присоединенного свойства. Это достигается путем вызова [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) метод, передав идентификатор может быть привязано, для которого необходимо получить значение и затем приведение результирующее значение в требуемый тип.

`Set` *PropertyName* доступа должны соответствовать следующую сигнатуру:

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName* доступа следует устанавливать значение соответствующего `BindableProperty` для присоединенного свойства. Это достигается путем вызова [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод, передавая идентификатор может быть привязано, для которого требуется задать значение и присваиваемое значение.

Для обоих методов доступа *целевой* объект должен быть или производным от него [ `BindableObject` ](xref:Xamarin.Forms.BindableObject).

В следующем примере кода показаны методы доступа для `HasShadow` вложенного свойства зависимостей:

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consuming-an-attached-property"></a>Использование вложенного свойства

После создания вложенного свойства, его можно будет использовать из XAML или кода. В XAML это достигается путем объявления пространства имен с префиксом, с объявлением пространства имен, указывающее имя пространства имен Common Language Runtime (CLR) и при необходимости имя сборки. Дополнительные сведения см. в разделе [пространства имен XAML](~/xamarin-forms/xaml/namespaces.md).

В следующем примере кода показано пространство имен XAML для пользовательского типа, который содержит вложенное свойство, который определен в сборке, в качестве код приложения, который ссылается на пользовательский тип:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

Объявление пространства имен затем используется, когда установив это присоединенное свойство на определенный элемент управления, как показано в следующем примере кода XAML:

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>Использование вложенного свойства в стиле

Вложенные свойства можно также добавить к элементу управления стилем. В следующем примере показан код XAML *явные* стиль, который использует `HasShadow` вложенное свойство зависимостей, который может быть применен к [ `Label` ](xref:Xamarin.Forms.Label) элементов управления:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style) Могут применяться к [ `Label` ](xref:Xamarin.Forms.Label) , задав его [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства `Style` экземпляра с помощью `StaticResource`расширения разметки, как показано в следующем примере кода:

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

Дополнительные сведения о стилях см. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md).

## <a name="advanced-scenarios"></a>Расширенные сценарии

При создании присоединенного свойства, существует ряд необязательных параметров, которые могут устанавливаться для реализации сценариев расширенной присоединенное свойство. Это включает в себя обнаружение изменений свойств, проверку значений свойств и преобразование значений свойств. Дополнительные сведения см. в разделе [расширенные сценарии](~/xamarin-forms/xaml/bindable-properties.md#advanced).

## <a name="summary"></a>Сводка

В этой статье предоставляются общие сведения о присоединенных свойств, а также показано, как создавать и использовать их. Присоединенное свойство — это специальная привязываемые свойства, определенные в одном классе, но присоединенного к другим объектам и распознается в XAML как атрибуты, которые содержат класс и имя свойства, разделенные точкой.


## <a name="related-links"></a>Связанные ссылки

- [Привязываемые свойства](~/xamarin-forms/xaml/bindable-properties.md)
- [Пространства имен языка XAML](~/xamarin-forms/xaml/namespaces.md)
- [Эффект тени (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
