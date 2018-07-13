---
title: Создание особенностей платформы
description: В этой статье показано, как предоставлять эффект через конкретную платформу.
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: 1d9f07a089eabedf07bef49c9815fe7e93128f09
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997260"
---
# <a name="creating-platform-specifics"></a>Создание особенностей платформы

_Поставщики могут создавать свои собственные особенности платформы с эффектами. Эффект предоставляет определенных функциональных возможностей, которые затем передаются через конкретную платформу. Результатом является эффект, можно было более легко через XAML и текучего кода API. В этой статье показано, как предоставлять эффект через конкретную платформу._

## <a name="overview"></a>Обзор

Процесс создания конкретной платформы выглядит следующим образом:

1. Реализуйте определенные функции, как эффект. Дополнительные сведения см. в разделе [Создание эффекта](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Создание класса платформы, который будет предоставить эффект. Дополнительные сведения см. в разделе [создание класса платформы](#creating).
1. В классе платформы реализации присоединенного свойства, чтобы разрешить конкретную платформу быть востребован XAML. Дополнительные сведения см. в разделе [Добавление присоединенного свойства](#attached_property).
1. В классе платформы реализации методов расширения, чтобы разрешить конкретную платформу для использования через API текучего кода. Дополнительные сведения см. в разделе [добавления методов расширения](#extension_methods).
1. Измените реализацию эффект, эффект применяется только в том случае, если был вызван с платформой на той же платформе, в результате. Дополнительные сведения см. в разделе [Создание силу](#creating_the_effect).

Предоставление эффект как от платформы образом, результат можно было более легко через XAML и текучего кода API.

> [!NOTE]
> Он является предусмотрено, что этот способ будет использоваться поставщиков для создания собственных особенностей платформы, для упрощения потребления пользователями. Хотя пользователи могут начать создавать свои собственные особенности платформы, следует отметить, что он требует больше кода, чем создание и использование эффекта.

Пример приложения демонстрирует `Shadow` платформы, добавляющий тени к тексту, отображаемому элементом [ `Label` ](xref:Xamarin.Forms.Label) управления:

![](creating-images/screenshots.png "Тени от платформы")

В образце приложения реализуется `Shadow` платформы на каждой платформе, для простоты понимания. Тем не менее помимо Каждая реализация эффект платформой, реализация класса теневой копии мало чем отличается для каждой платформы. Таким образом это руководство посвящено реализации класса теневой копии и связанные влияет на единую платформу.

Дополнительные сведения об эффектах см. в разделе [Настройка элементов управления с эффектами](~/xamarin-forms/app-fundamentals/effects/index.md).

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>Создание класса платформы

Конкретную платформу создается как `public static` класса:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

В следующих разделах рассматривается реализация `Shadow` эффект от платформы и связанные с ней.

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>Добавление вложенного свойства

Необходимо добавить вложенное свойство `Shadow` платформы — разрешить использование через XAML:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed` Присоединенное свойство используется для добавления `MyCompany.LabelShadowEffect` эффект и удалите его из элемента управления, `Shadow` класс присоединяется к. Это присоединенного свойства регистры `OnIsShadowedPropertyChanged` метод, который будет выполняться при изменении значения свойства. В свою очередь, вызывает этот метод `AttachEffect` или `DetachEffect` метод для добавления или удаления эффект на основе значения из `IsShadowed` вложенного свойства зависимостей. Эффект, добавлении или удалении из элемента управления путем изменения элемента управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции.

> [!NOTE]
> Обратите внимание, что эффект разрешается, указав значение, которое представляет собой объединение имени группы разрешения и уникальный идентификатор, который указан в реализации эффект. Дополнительные сведения см. в разделе [Создание эффекта](~/xamarin-forms/app-fundamentals/effects/creating.md).

Дополнительные сведения о вложенных свойствах см. в разделе [присоединенного свойства](~/xamarin-forms/xaml/attached-properties.md).

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>Добавления методов расширения

Методы расширения должны добавляться к `Shadow` платформы — разрешить использование через API текучего кода:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed` И `SetIsShadowed` методы расширения вызова get и set для `IsShadowed` вложенное свойство зависимостей, соответственно. Каждый метод расширения работает `IPlatformElementConfiguration<iOS, FormsElement>` тип, который указывает, что специфические для платформы может быть вызвана [ `Label` ](xref:Xamarin.Forms.Label) экземпляров из iOS.

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>Создание эффекта

`Shadow` Добавляет платформы `MyCompany.LabelShadowEffect` для [ `Label` ](xref:Xamarin.Forms.Label), а затем удаляет его. В следующем коде показано в примере `LabelShadowEffect` реализации для проекта iOS:

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`UpdateShadow` Метода задает `Control.Layer` свойства для создания тени, при условии, что `IsShadowed` присоединенное свойство имеет значение `true`и при условии, что `Shadow` платформы был вызван на одной платформе, Для реализации эффект. Эта проверка выполняется с помощью `OnThisPlatform` метод.

Если `Shadow.IsShadowed` присоединенного изменения значений свойств во время выполнения потребностей эффект реагировать, удалив тени. Таким образом, переопределения `OnElementPropertyChanged` метод используется в ответ на изменение может быть привязано, вызвав `UpdateShadow` метод.

Дополнительные сведения о создании эффекта см. в разделе [Создание эффекта](~/xamarin-forms/app-fundamentals/effects/creating.md) и [передача параметров эффект как вложенные свойства](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

## <a name="consuming-a-platform-specific"></a>Использование конкретной платформы

`Shadow` Платформы потребляется в XAML, задав `Shadow.IsShadowed` вложенное свойство, чтобы `boolean` значение:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

Дополнительные сведения о использование особенностей платформы, см. в разделе [особенностей платформы в использование](~/xamarin-forms/platform/platform-specifics/consuming/index.md).

## <a name="summary"></a>Сводка

В этой статье было показано, как предоставлять эффект через конкретную платформу. Результатом является эффект, можно было более легко через XAML и текучего кода API.


## <a name="related-links"></a>Связанные ссылки

- [ShadowPlatformSpecific (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Настройка элементов управления с эффектами](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Вложенные свойства](~/xamarin-forms/xaml/attached-properties.md)
