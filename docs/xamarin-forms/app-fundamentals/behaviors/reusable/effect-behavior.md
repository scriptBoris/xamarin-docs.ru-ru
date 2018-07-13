---
title: Повторно используемые EffectBehavior
description: Поведения представляют собой удобный подход для добавления эффекта к элементу управления, удаление эффекта стереотипного кода из файлов кода программной обработки. В этой статье показано, с помощью поведения Xamarin.Forms, чтобы добавить эффект к элементу управления.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995785"
---
# <a name="reusable-effectbehavior"></a>Повторно используемые EffectBehavior

_Поведения представляют собой удобный подход для добавления эффекта к элементу управления, удаление эффекта стереотипного кода из файлов кода программной обработки. В этой статье показано, с помощью поведения Xamarin.Forms, чтобы добавить эффект к элементу управления._

## <a name="overview"></a>Обзор

`EffectBehavior` Класс является многократно используемых пользовательское поведение Xamarin.Forms, который добавляет [ `Effect` ](xref:Xamarin.Forms.Effect) экземпляра к элементу управления, когда поведение, присоединенных к элементу управления и удаляет `Effect` экземпляра при поведение отсоединить от элемента управления.

Следующие свойства поведение должно быть задано в соответствии с поведением:

- **Группа** – значение [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) атрибута для класса эффект.
- **Имя** – значение [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) атрибута для класса эффект.

Дополнительные сведения об эффектах см. в разделе [эффекты](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Создание поведение

`EffectBehavior` Класс является производным от [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса, где `T` — [ `View` ](xref:Xamarin.Forms.View). Это означает, что `EffectBehavior` класса можно подключить к любому элементу управления Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Реализация привязываемые свойства

`EffectBehavior` Класс определяет два [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляров, которые можно использовать для добавления [ `Effect` ](xref:Xamarin.Forms.Effect) к элементу управления, при присоединении поведения к элементу управления. Эти свойства отображаются в следующем примере кода:

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

Когда `EffectBehavior` исчерпания `Group` свойство должно быть присвоено значение [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) атрибут для эффекта. Кроме того `Name` свойство должно быть присвоено значение [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) атрибута для класса эффект.

### <a name="implementing-the-overrides"></a>Реализация переопределения

`EffectBehavior` Класса переопределения [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) и [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) методы [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса, как показано в следующем коде Пример:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) Метод выполняет программу установки с помощью вызова `AddEffect` метод, передавая присоединенного элемента управления как параметр. [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) Метод выполняет очистку, вызвав `RemoveEffect` метод, передавая присоединенного элемента управления как параметр.

### <a name="implementing-the-behavior-functionality"></a>Реализация функциональных возможностей поведения

Поведение служит для добавления [ `Effect` ](xref:Xamarin.Forms.Effect) определенные в `Group` и `Name` свойства в элемент управления, когда поведение, присоединенных к элементу управления и удалите `Effect` при поведение отсоединить от элемента управления. В следующем примере кода показано поведение основные функциональные возможности:

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

`AddEffect` Метод выполняется в ответ на `EffectBehavior` присоединяемый к элементу управления и он получает присоединенного элемента управления как параметр. Затем этот метод добавляет полученный результат в элемент управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции. `RemoveEffect` Метод выполняется в ответ на `EffectBehavior` после отключения от элемента управления и он получает присоединенного элемента управления как параметр. Метод затем удаляет эффект из элемента управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции.

`GetEffect` Использует метод [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) метод для извлечения [ `Effect` ](xref:Xamarin.Forms.Effect). Результат находится через объединение `Group` и `Name` значения свойств. Если платформа не предоставляет эффектом `Effect.Resolve` метод будет возвращать значение отличное от`null` значение.

## <a name="consuming-the-behavior"></a>Использование поведения

`EffectBehavior` Класса можно подключить к [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) коллекцию элементов управления, как показано в следующем примере кода XAML:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

`Group` И `Name` свойства поведения присваиваются значения [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) и [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) атрибуты для класса эффект в каждой конкретной платформы проект.

Во время выполнения, при присоединении поведения к [ `Label` ](xref:Xamarin.Forms.Label) управления `Xamarin.LabelShadowEffect` будут добавлены к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции. Это приводит к тени, добавляемой к тексту, отображаемому элементом `Label` контролировать, как показано на следующем снимке экрана:

![](effect-behavior-images/screenshots.png "Пример приложения с помощью EffectsBehavior")

Преимуществом использования этого поведения для добавления и удаления эффекты от элементов управления является удаления эффект обработки стереотипного кода из файлов кода.

## <a name="summary"></a>Сводка

В этой статье показано, с помощью поведения, чтобы добавить эффект к элементу управления. `EffectBehavior` Класс является многократно используемых пользовательское поведение Xamarin.Forms, который добавляет [ `Effect` ](xref:Xamarin.Forms.Effect) экземпляра к элементу управления, когда поведение, присоединенных к элементу управления и удаляет `Effect` экземпляра при поведение отсоединить от элемента управления.


## <a name="related-links"></a>Связанные ссылки

- [Эффекты](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Поведение эффект (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Поведение](xref:Xamarin.Forms.Behavior)
- [Поведение<T>](xref:Xamarin.Forms.Behavior`1)
