---
title: Реакции на событие Xamarin.Forms
description: Реакции на событие Xamarin.Forms создаются путем наследования от поведение или поведение<T> класса. В этой статье показано, как создавать и использовать реакции на событие Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998899"
---
# <a name="xamarinforms-behaviors"></a>Реакции на событие Xamarin.Forms

_Реакции на событие Xamarin.Forms создаются путем наследования от поведение или поведение<T> класса. В этой статье показано, как создавать и использовать реакции на событие Xamarin.Forms._

## <a name="overview"></a>Обзор

Процесс создания Xamarin.Forms поведение выглядит следующим образом:

1. Создайте класс, наследуемый от [ `Behavior` ](xref:Xamarin.Forms.Behavior) или [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса, где `T` является тип элемента управления, к которому следует применить поведение.
1. Переопределить [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) метод для выполнения дополнительных настроек.
1. Переопределить [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) метод, чтобы выполнить необходимую очистку.
1. Реализации основных функциональных возможностей поведения.

Это приводит к структуре, показано в следующем примере кода:

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) Метод запускается сразу после прикреплено поведение к элементу управления. Этот метод получает ссылку на элемент управления, к которому он подключен и может использоваться для регистрации обработчиков событий или выполните другие настройки, необходимые для поддержки функциональных возможностей поведения. Например может подписаться на событие элемента управления. Функциональных возможностей поведения, то должен быть реализован в обработчике событий для события.

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) Метод создается в том случае, когда поведение удаляется из элемента управления. Этот метод получает ссылку на элемент управления, к которому он подключен и используется для выполнения всех действий по очистке. Например вы может отменить подписку на событие элемента управления для предотвращения утечек памяти.

Поведение принимаются, подключив его к [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) коллекцию соответствующий элемент управления.

## <a name="creating-a-xamarinforms-behavior"></a>Создание поведение Xamarin.Forms

Пример приложения демонстрирует `NumericValidationBehavior`, который рассказывает, введенное пользователем в [ `Entry` ](xref:Xamarin.Forms.Entry) управления красным, если это не `double`. Поведение показано в следующем примере кода:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` Является производным от [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса, где `T` — [ `Entry` ](xref:Xamarin.Forms.Entry). [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) Метод регистрирует обработчик событий для [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) событий, с помощью [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) метод при отмене регистрации `TextChanged`событие памяти во избежание утечки. Основные функциональные возможности поведения обеспечивается `OnEntryTextChanged` метод, который выполняет синтаксический анализ значение, введенное пользователем в `Entry`и задает [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) свойство на красный, если значение не `double`.

> [!NOTE]
> Xamarin.Forms не устанавливает `BindingContext` поведения, так как поведения можно совместно использовать и применить к нескольким элементам управления через стили.

## <a name="consuming-a-xamarinforms-behavior"></a>Использование поведения Xamarin.Forms

Каждому элементу управления Xamarin.Forms [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) коллекции, к которому можно добавить один или несколько поведений, как показано в следующем примере кода XAML:

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

Эквивалент [ `Entry` ](xref:Xamarin.Forms.Entry) в C# показан в следующем примере кода:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

Во время выполнения поведение будет отвечать на действия с элементом управления, в соответствии с реализация поведения. Далее на снимках экрана показано поведение, реагирование на недопустимые входные данные:

[![](creating-images/screenshots-sml.png "Пример приложения с поведением Xamarin.Forms")](creating-images/screenshots.png#lightbox "пример приложения с поведением Xamarin.Forms")

> [!NOTE]
> Поведения написаны для типа элемента управления (или суперкласса, можно применить к многим элементам управления), и они должны добавляться только к элементу управления совместимы. Попытка подключить поведение к элементу управления несовместимые приведет к возникновению исключения.

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>Использование Xamarin.Forms поведения в стиле

Поведение также могут использоваться один из явных или неявных стилей. Однако создание стиль, который задает [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) свойства элемента управления не поддерживается, так как свойство только для чтения. Решение — добавить присоединенное свойство в класс поведение, управляющий добавления и удаления поведения. Процесс выглядит следующим образом:

1. Добавьте вложенное свойство в класс поведение, который будет использоваться для управления, добавление или удаление поведения, элементом управления, к которому будет поведение. Убедитесь, что присоединенное свойство регистрирует `propertyChanged` делегат, который будет выполняться при изменении значения свойства.
1. Создание `static` Get и set для присоединенного свойства.
1. Реализовать логику в `propertyChanged` делегат для добавления и удаления поведения.

В следующем примере кода показано вложенного свойства, элементы управления, добавляя и удаляя `NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

`NumericValidationBehavior` Класс содержит вложенное свойство с именем `AttachBehavior` с `static` Get и set, управляющий Добавление или удаление поведения в элемент управления, к которому будет присоединен. Это присоединенного свойства регистры `OnAttachBehaviorChanged` метод, который будет выполняться при изменении значения свойства. Этот метод добавляет или удаляет поведение к элементу управления, в зависимости от значения `AttachBehavior` вложенного свойства зависимостей.

В следующем коде показано в примере *явные* стиль для `NumericValidationBehavior` , использующий `AttachBehavior` подключенное свойство, и которые могут применяться к [ `Entry` ](xref:Xamarin.Forms.Entry) элементов управления:

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style) Могут применяться к [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления, задав его [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства `Style` экземпляра с помощью `StaticResource` расширения разметки, как показано в следующем примере кода:

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

Дополнительные сведения о стилях см. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md).

> [!NOTE]
> Хотя вы можете добавить состоянием привязываемые свойства на поведение, которое задается или запросить в XAML, если вы создаете поведения, они не могут совместно использоваться между элементами управления в `Style` в `ResourceDictionary`.

### <a name="removing-a-behavior-from-a-control"></a>Удаление поведения из элемента управления

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) Метод создается в том случае, когда поведение будет удален из элемента управления и используется для выполнения всех действий по очистке например отмены подписки на событие, чтобы предотвратить утечку памяти. Тем не менее, поведение не удаляются неявно из элементов управления Если элемента управления [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) коллекция изменяется с `Remove` или `Clear` метод. В следующем примере кода демонстрируется удаление особое поведение элемента управления `Behaviors` коллекции:

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

Кроме того, элемента управления [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) очистить коллекцию, как показано в следующем примере кода:

```csharp
entry.Behaviors.Clear();
```

Кроме того Обратите внимание на то, что варианты поведения, не удаляются неявно из элементов управления при страниц извлекаются из стека навигации. Вместо этого они должны быть явно удалены до страницы, окажется вне области действия.

## <a name="summary"></a>Сводка

В этой статье было показано, как создавать и использовать реакции на событие Xamarin.Forms. Реакции на событие Xamarin.Forms создаются путем наследования от [ `Behavior` ](xref:Xamarin.Forms.Behavior) или [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса.


## <a name="related-links"></a>Связанные ссылки

- [Поведение Xamarin.Forms (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Поведение Xamarin.Forms применен стиль (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [Поведение](xref:Xamarin.Forms.Behavior)
- [Поведение<T>](xref:Xamarin.Forms.Behavior`1)
