---
title: Вложенные реакции на событие
description: Вложенные реакции на событие, статические классы с один или несколько вложенных свойств. В этой статье показано, как создавать и использовать вложенные реакции на событие.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995350"
---
# <a name="attached-behaviors"></a>Вложенные реакции на событие

_Вложенные реакции на событие, статические классы с один или несколько вложенных свойств. В этой статье показано, как создавать и использовать вложенные реакции на событие._

## <a name="overview"></a>Обзор

Присоединенное свойство — это специальный тип может быть привязано. Они определены в одном классе, но присоединен к другим объектам, и они являются распознается в XAML как атрибуты, которые содержат класс и имя свойства, разделенные точкой.

Присоединенное свойство можно определить `propertyChanged` делегат, который будет выполняться при изменении значения свойства, например, если свойство имеет значение в элементе управления. Когда `propertyChanged` выполняет делегат, он передал ссылку на элемент управления, на котором он присоединяется и параметры, содержащие старое и новое значения для свойства. Этот делегат может использоваться для добавления новых функциональных возможностей в элемент управления, свойство, к которому присоединена, управляя ссылку, передается в, следующим образом:

1. `propertyChanged` Делегат приведение ссылки элемента управления, который получаются в виде [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), тип элемента управления, то поведение не разработаны для улучшения.
1. `propertyChanged` Делегат изменяет свойства элемента управления, вызывает методы элемента управления, или регистрирует обработчики событий для события, предоставляемые элементом управления, для реализации основных функциональных возможностей поведения.

Проблемы с вложенные реакции на событие — что они определены в `static` класса, с помощью `static` свойства и методы. Это затрудняет создание вложенные реакции на событие состоянием. Кроме того реакции на событие Xamarin.Forms заменили вложенные реакции на событие, став предпочитаемым подходом для создания поведения. Дополнительные сведения о реакции на событие Xamarin.Forms, см. в разделе [реакции на событие Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) и [поведения для повторного использования](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Создание присоединенного поведение

Пример приложения демонстрирует `NumericValidationBehavior`, который рассказывает, введенное пользователем в [ `Entry` ](xref:Xamarin.Forms.Entry) управления красным, если это не `double`. Поведение показано в следующем примере кода:

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

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
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` Класс содержит вложенное свойство с именем `AttachBehavior` с `static` Get и set, управляющий Добавление или удаление поведения в элемент управления, к которому будет присоединен. Это присоединенного свойства регистры `OnAttachBehaviorChanged` метод, который будет выполняться при изменении значения свойства. Этот метод регистрирует или отмены регистрирует обработчик событий для [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) события, в зависимости от значения `AttachBehavior` вложенного свойства зависимостей. Основные функциональные возможности поведения обеспечивается `OnEntryTextChanged` метод, который выполняет синтаксический анализ значение, введенное в [ `Entry` ](xref:Xamarin.Forms.Entry) по пользователям и наборы `TextColor` свойство на красный, если значение не `double`.

## <a name="consuming-an-attached-behavior"></a>Использование вложенных поведение

`NumericValidationBehavior` Класс можно использовать, добавив `AttachBehavior` вложенное свойство, чтобы [ `Entry` ](xref:Xamarin.Forms.Entry) управления, как показано в следующем примере кода XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

Эквивалент [ `Entry` ](xref:Xamarin.Forms.Entry) в C# показан в следующем примере кода:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Во время выполнения поведение будет отвечать на действия с элементом управления, в соответствии с реализация поведения. Далее на снимках экрана показано вложенное поведение, реагирование на недопустимые входные данные:

[![](attached-images/screenshots-sml.png "Пример приложения с помощью присоединенного поведение")](attached-images/screenshots.png#lightbox "пример приложения с помощью присоединенного поведение")

> [!NOTE]
> Вложенные реакции на событие написаны для типа элемента управления (или суперкласса, можно применить к многим элементам управления), и они должны добавляться только к элементу управления совместимы. При попытке подключить поведение к элементу управления несовместимые приведет к неизвестное поведение и зависит от реализации поведения.

### <a name="removing-an-attached-behavior-from-a-control"></a>Удаление вложенных поведение из элемента управления

`NumericValidationBehavior` Класс может быть удален из элемента управления, задав `AttachBehavior` вложенное свойство, чтобы `false`, как показано в следующем примере кода XAML:

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

Эквивалент [ `Entry` ](xref:Xamarin.Forms.Entry) в C# показан в следующем примере кода:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

Во время выполнения `OnAttachBehaviorChanged` метод будет выполнен, когда значение `AttachBehavior` присоединенное свойство имеет значение `false`. `OnAttachBehaviorChanged` Метод будет затем отмену регистрации обработчика событий для [ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged) событие. таким образом, поведение не выполняется, так как пользователь взаимодействует с элементом управления.

## <a name="summary"></a>Сводка

В этой статье было показано, как создавать и использовать вложенные реакции на событие. Вложенные реакции на событие, `static` классы с один или несколько вложенных свойств.


## <a name="related-links"></a>Связанные ссылки

- [Вложенные реакции на событие (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
