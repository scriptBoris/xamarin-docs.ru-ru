---
title: Для повторного использования EffectBehavior
description: Поведения — полезна для добавления эффекта в элемент управления, удаление эффекта шаблон формы, обработки кода из файлов кода. В этой статье показано, с помощью поведения Xamarin.Forms, чтобы добавить эффект к элементу управления.
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: a1612d1e87f0e05c859babd93fd03ac9a5736b47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785059"
---
# <a name="reusable-effectbehavior"></a>Для повторного использования EffectBehavior

_Поведения — полезна для добавления эффекта в элемент управления, удаление эффекта шаблон формы, обработки кода из файлов кода. В этой статье показано, с помощью поведения Xamarin.Forms, чтобы добавить эффект к элементу управления._

## <a name="overview"></a>Обзор

`EffectBehavior` Класс является многократно используемых пользовательское поведение Xamarin.Forms, который добавляет [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) экземпляр для элемента управления, когда поведение, присоединенных к элементу управления и удаляет `Effect` экземпляра при поведение отсоединяется от элемента управления.

Чтобы использовать поведение должно быть задано поведение следующие свойства:

- **Группа** – значение [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) атрибутом для класса эффект.
- **Имя** – значение [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) атрибутом для класса эффект.

Дополнительные сведения об эффектах см. в разделе [эффекты](~/xamarin-forms/app-fundamentals/effects/index.md).

## <a name="creating-the-behavior"></a>Создание поведения

`EffectBehavior` Класс является производным от [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) класса, где `T` — [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/). Это означает, что `EffectBehavior` класса можно подключить к любому элементу управления Xamarin.Forms.

### <a name="implementing-bindable-properties"></a>Реализация свойства для привязки

`EffectBehavior` Класс определяет два [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) экземпляров, которые используются для добавления [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) к элементу управления, когда применяется поведение элемента управления. В следующем примере кода показаны следующие свойства:

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

Когда `EffectBehavior` потребляется, `Group` свойство должно быть присвоено значение [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) атрибута применительно к эффекту. Кроме того `Name` свойство должно быть присвоено значение [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) атрибутом для класса эффект.

### <a name="implementing-the-overrides"></a>Реализации переопределений

`EffectBehavior` Класса переопределения [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) и [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) методы [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) класса, как показано в следующем коде Пример:

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

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/) Метод выполняет программу установки с помощью вызова `AddEffect` метод, передавая присоединенного элемента управления как параметр. [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/) Метод выполняет очистку, вызвав `RemoveEffect` метод, передавая присоединенного элемента управления как параметр.

### <a name="implementing-the-behavior-functionality"></a>Реализация функциональных возможностей поведения

Поведение служит для добавления [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) определенные в `Group` и `Name` свойства для элемента управления, когда поведение, присоединенных к элементу управления и удалите `Effect` при поведение отсоединяется от элемента управления. В следующем примере кода показано поведение основные функциональные возможности:

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

`AddEffect` Метод выполняется в ответ на `EffectBehavior` , присоединяемый к элементу управления и он получает присоединенного элемента управления как параметр. Затем этот метод добавляет полученные эффект к элементу управления [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) коллекции. `RemoveEffect` Метод выполняется в ответ на `EffectBehavior` отключаемой от элемента управления и он получает присоединенного элемента управления как параметр. Метод затем удаляет эффект из элемента управления [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) коллекции.

`GetEffect` Использует метод [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) метод для извлечения [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/). Результат находится посредством объединения `Group` и `Name` значения свойств. Если платформа не дает эффекта, `Effect.Resolve` метод возвращает значение, отличное от`null` значение.

## <a name="consuming-the-behavior"></a>Использование поведения

`EffectBehavior` Класса можно подключить к [ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) коллекцию элементов управления, как показано в следующем примере кода XAML:

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

`Group` И `Name` свойствам поведения присваиваются значения [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) и [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) атрибуты для класса эффект в каждой платформы проект.

Во время выполнения, когда применяется поведение [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) управления `Xamarin.LabelShadowEffect` будут добавлены к элементу управления [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) коллекции. Это приведет к тень, добавляемый текст, отображаемый элементом `Label` устанавливаются, как показано на следующем снимке экрана:

![](effect-behavior-images/screenshots.png "Пример приложения с EffectsBehavior")

Преимущество использования этого поведения для добавления и удаления эффекты от элементов управления — что кода эффект обработки шаблона формы могут быть удалены из файлов кода.

## <a name="summary"></a>Сводка

В этой статье показано, с помощью поведения, чтобы добавить эффект к элементу управления. `EffectBehavior` Класс является многократно используемых пользовательское поведение Xamarin.Forms, который добавляет [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) экземпляр для элемента управления, когда поведение, присоединенных к элементу управления и удаляет `Effect` экземпляра при поведение отсоединяется от элемента управления.


## <a name="related-links"></a>Связанные ссылки

- [Эффекты](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Поведение эффект (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [Behavior<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
