---
title: Привязываемые свойства
description: В этой статье содержатся вводные привязываемые свойства, а также показано, как создавать и использовать их.
ms.prod: xamarin
ms.assetid: 1EE869D8-6FE1-45CA-A0AD-26EC7D032AD7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: 8dc53c37894af70d5183fe5c44b018fdf25af616
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563853"
---
# <a name="bindable-properties"></a>Привязываемые свойства

_В Xamarin.Forms функциональные возможности параметров среды выполнения (CLR) расширен с помощью свойства связывания. Может быть привязано — это специальный тип свойства, где значение свойства отслеживается системой свойств Xamarin.Forms. В этой статье содержатся вводные привязываемые свойства, а также показано, как создавать и использовать их._

## <a name="overview"></a>Обзор

Привязываемые свойства расширяют функциональные возможности свойства CLR, связывание свойства с [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) типа, не связывание свойства с полем. Привязываемые свойства служит для предоставления системы свойств, который поддерживает привязку данных, стили, шаблоны и значения, заданные через родительско дочерних отношений. Кроме того привязываемые свойства можно указать значения по умолчанию, проверку значений свойств и обратных вызовов, отслеживающих изменения свойств.

Свойства должны быть реализованы как привязываемые свойства должно поддерживать одну или несколько из следующих компонентов:

- Выступает в качестве допустимого *целевой* свойство для привязки данных.
- Задание свойства с помощью [стиля](~/xamarin-forms/user-interface/styles/index.md).
- Предоставление значением свойства по умолчанию, который отличается от по умолчанию для типа свойства.
- Проверка значения свойства.
- Мониторинг изменений свойств.

Примеры Xamarin.Forms привязываемые свойства [ `Label.Text` ](xref:Xamarin.Forms.Label.Text), [ `Button.BorderRadius` ](xref:Xamarin.Forms.Button.BorderRadius), и [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation). Каждое свойство привязки имеет соответствующий `public static readonly` свойство типа [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) , который предоставляется для одного класса и является идентификатором связываемые свойства. Например, соответствующий идентификатор свойство, используемое для `Label.Text` свойство [ `Label.TextProperty` ](xref:Xamarin.Forms.Label.TextProperty).

<a name="consuming-bindable-property" />

## <a name="creating-and-consuming-a-bindable-property"></a>Создание и использование может быть привязано

Процесс создания может быть привязано выглядит следующим образом:

1. Создание [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляра с одним из [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create*) перегрузок метода.
1. Определять методы доступа свойств для [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляра.

Обратите внимание, что все [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляры должны создаваться в потоке пользовательского интерфейса. Это означает, что только код, который выполняется в потоке пользовательского интерфейса можно получить или задать значение может быть привязано. Тем не менее `BindableProperty` экземпляров может осуществляться из других потоков маршалинг в поток пользовательского интерфейса с помощью [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) метод.

### <a name="creating-a-property"></a>Создание свойства

Чтобы создать `BindableProperty` экземпляра, содержащего класса должен быть производным от [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) класса. Тем не менее `BindableObject` класс высокого уровня в иерархии классов, поэтому большинство классов, используемый для привязки свойств поддержки функциональные возможности пользовательского интерфейса.

Может быть привязано могут создаваться путем объявления `public static readonly` свойство типа [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty). Возвращаемое значение одного из задается свойство, используемое [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) перегрузок метода. Объявление должно быть в теле [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) производного класса, но вне определения любого из членов.

Как минимум, идентификатор должен быть указан при создании [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), а также следующие параметры:

- Имя [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty).
- Тип свойства.
- Тип объекта-владельца.
- Значение по умолчанию для свойства. Это гарантирует, что свойство всегда возвращает значение определенного по умолчанию, если оно не установлено, и он может отличаться от значения по умолчанию для типа свойства. Значение по умолчанию будут восстановлены при [ `ClearValue` ](xref:Xamarin.Forms.BindableObject.ClearValue(Xamarin.Forms.BindableProperty)) на свойство, используемое вызывается метод.

Ниже приведен пример привязки свойства, с идентификатором и значения четыре обязательных параметров:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null);
```

При этом создается [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляр с именем `EventName`, типа `string`. Свойство принадлежит `EventToCommandBehavior` класса, и имеет значение по умолчанию `null`. Соглашение об именовании привязываемые свойства — это что может быть привязано идентификатор должен соответствовать имени свойства, указанные в `Create` метод, с добавленным к нему «Property». Таким образом, в приведенном выше примере идентификатор может быть привязано является `EventNameProperty`.

При необходимости при создании [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляра, следующие параметры можно указать:

- Режим привязки. Используется для указания направления, в котором будут распространены изменения значений свойств. В режиме привязки по умолчанию, изменения распространялись из *источника* для *целевой*.
- Делегат проверки, который будет вызываться, когда свойство имеет значение. Дополнительные сведения см. в разделе [обратные вызовы проверки](#validation).
- Делегат, который будет вызываться при изменении значения свойства изменено свойство. Дополнительные сведения см. в разделе [обнаружение изменений свойств](#propertychanges).
- Свойство, изменение делегата, который будет вызываться, когда изменится значение свойства. Этот делегат имеет ту же сигнатуру, что делегат изменения свойства.
- Делегат значение принудительного, который будет вызываться при изменении значения свойства. Дополнительные сведения см. в разделе [приведение обратных вызовов значение](#coerce).
- Объект `Func` , используемый для инициализации значения свойства по умолчанию. Дополнительные сведения см. в разделе [создающий значение по умолчанию с Func](#defaultfunc).

### <a name="creating-accessors"></a>Создание методов доступа

К свойствам должны использовать синтаксис свойства для доступа к может быть привязано. `Get` Доступа должна возвращать значение, которое содержится в соответствующее свойство привязки. Это достигается путем вызова [ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) метод, передав идентификатор может быть привязано, для которого необходимо получить значение и затем преобразование результата в требуемый тип. `Set` Доступа следует устанавливать значение соответствующего свойства привязки. Это достигается путем вызова [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод, передавая идентификатор может быть привязано, для которого требуется задать значение и присваиваемое значение.

В следующем примере кода показаны методы доступа для `EventName` свойство, используемое:

```csharp
public string EventName {
  get { return (string)GetValue (EventNameProperty); }
  set { SetValue (EventNameProperty, value); }
}
```

### <a name="consuming-a-bindable-property"></a>Использование может быть привязано

После создания может быть привязано, его можно будет использовать из XAML или кода. В XAML это достигается путем объявления пространства имен с префиксом, с объявлением пространства имен, указывающее имя пространства имен CLR и при необходимости, имя сборки. Дополнительные сведения см. в разделе [пространства имен XAML](~/xamarin-forms/xaml/namespaces.md).

В следующем примере кода показано пространство имен XAML для пользовательского типа, который содержит свойство привязки, который определен в сборке, в качестве код приложения, который ссылается на пользовательский тип:

```xaml
<ContentPage ... xmlns:local="clr-namespace:EventToCommandBehavior" ...>
  ...
</ContentPage>
```

Объявление пространства имен используется при задании `EventName` свойство, используемое, как показано в следующем примере кода XAML:

```xaml
<ListView ...>
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" ... />
  </ListView.Behaviors>
</ListView>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var listView = new ListView ();
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  ...
});
```

<a name="advanced" />

## <a name="advanced-scenarios"></a>Расширенные сценарии

При создании [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляра, существует несколько необязательных параметров, которые могут устанавливаться для реализации сценариев дополнительного связываемые свойства. В данном разделе изучаются этих сценариев.

<a name="propertychanges" />

### <a name="detecting-property-changes"></a>Обнаружение событий изменения свойств

Объект `static` метод обратного вызова, изменения свойств может быть зарегистрирован может быть привязано, указав `propertyChanged` параметр для [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) метод. Указанный метод обратного вызова будет вызываться при изменении значения свойства привязки.

В следующем примере кода показано как `EventName` регистрирует свойство, используемое `OnEventNameChanged` метода как метода обратного вызова, изменения свойства:

```csharp
public static readonly BindableProperty EventNameProperty =
  BindableProperty.Create (
    "EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
...

static void OnEventNameChanged (BindableObject bindable, object oldValue, object newValue)
{
  // Property changed implementation goes here
}
```

В методе обратного вызова, изменения свойств [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) параметр используется для обозначения того, какой экземпляр класс-владелец обнаружил изменение, а также значения двух `object` параметры представляют старые и новые значения свойство, используемое.

<a name="validation" />

### <a name="validation-callbacks"></a>Обратные вызовы проверки

Объект `static` метод обратного вызова проверки может быть зарегистрирован может быть привязано, указав `validateValue` параметр для [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) метод. Указанный метод обратного вызова будет вызываться, если задано значение, связываемые свойства.

В следующем примере кода показано как `Angle` регистрирует свойство, используемое `IsValidValue` метода как метода обратного вызова проверки:

```csharp
public static readonly BindableProperty AngleProperty =
  BindableProperty.Create ("Angle", typeof(double), typeof(HomePage), 0.0, validateValue: IsValidValue);
...

static bool IsValidValue (BindableObject view, object value)
{
  double result;
  bool isDouble = double.TryParse (value.ToString (), out result);
  return (result >= 0 && result <= 360);
}
```

Обратные вызовы проверки предоставляются со значением и должен возвращать `true` Если значение является допустимым для свойства, в противном случае `false`. Будет порождено исключение, если обратный вызов проверки возвращает `false`, которого должно обрабатываться разработчиком. Типичное использование метода обратного вызова проверки ограничения значений целых чисел, если задано свойство, используемое. Например `IsValidValue` метод проверяет, что свойство имеет значение `double` в диапазоне от 0 до 360.

<a name="coerce" />

### <a name="coerce-value-callbacks"></a>Приведенных значений

Объект `static` привязкой к значению метод обратного вызова может быть зарегистрирован может быть привязано, указав `coerceValue` параметр для [ `BindableProperty.Create` ](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) метод. Указанный метод обратного вызова будет вызываться при изменении значения свойства привязки.

Приведение значения обратные вызовы используются для принудительного переоценки свойство, используемое при изменении значения свойства. Например чтобы убедиться, что значение одного свойства привязки не больше, чем значение другого привязываемые свойства можно использовать значение принудительного обратного вызова.

В следующем примере кода показано как `Angle` регистрирует свойство, используемое `CoerceAngle` метода как метода обратного вызова значение принудительного:

```csharp
public static readonly BindableProperty AngleProperty = BindableProperty.Create (
  "Angle", typeof(double), typeof(HomePage), 0.0, coerceValue: CoerceAngle);
public static readonly BindableProperty MaximumAngleProperty = BindableProperty.Create (
  "MaximumAngle", typeof(double), typeof(HomePage), 360.0);
...

static object CoerceAngle (BindableObject bindable, object value)
{
  var homePage = bindable as HomePage;
  double input = (double)value;

  if (input > homePage.MaximumAngle) {
    input = homePage.MaximumAngle;
  }

  return input;
}
```

`CoerceAngle` Метод проверяет значение параметра `MaximumAngle` свойство и если `Angle` он превышает значение свойства, он приводит значение к `MaximumAngle` значение свойства.

<a name="defaultfunc" />

### <a name="creating-a-default-value-with-a-func"></a>Создание значения по умолчанию с помощью Func

Объект `Func` может использоваться для инициализации значения по умолчанию привязываемые свойства, как показано в следующем примере кода:

```csharp
public static readonly BindableProperty SizeProperty =
  BindableProperty.Create ("Size", typeof(double), typeof(HomePage), 0.0,
  defaultValueCreator: bindable => Device.GetNamedSize (NamedSize.Large, (Label)bindable));
```

`defaultValueCreator` Параметр имеет значение `Func` , вызывающий [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) метод для возврата `double` , представляющий именованный размер шрифта, который используется на [ `Label` ](xref:Xamarin.Forms.Label) на собственной платформе.

## <a name="summary"></a>Сводка

В этой статье предоставляются общие сведения о привязываемые свойства, а также показано, как создавать и использовать их. Может быть привязано — это специальный тип свойства, где значение свойства отслеживается системой свойств Xamarin.Forms.


## <a name="related-links"></a>Связанные ссылки

- [Пространства имен языка XAML](~/xamarin-forms/xaml/namespaces.md)
- [Чтобы команда поведение события (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Обратный вызов проверки (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/validationcallback/)
- [Приведение значение обратного вызова (пример)](https://developer.xamarin.com/samples/xamarin-forms/xaml/coercevaluecallback/)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
