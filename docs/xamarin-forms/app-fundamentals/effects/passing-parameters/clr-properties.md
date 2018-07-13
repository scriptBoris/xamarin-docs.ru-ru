---
title: Передача параметров эффект как общие свойства среды выполнения языка
description: Свойства Common Language Runtime (CLR) можно использовать для определения влияния параметров, которые не реагируют на изменения свойств среды выполнения. В этой статье показано использование свойств CLR для передачи параметров эффекта.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995772"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Передача параметров эффект как общие свойства среды выполнения языка

_Свойства Common Language Runtime (CLR) можно использовать для определения влияния параметров, которые не реагируют на изменения свойств среды выполнения. В этой статье показано использование свойств CLR для передачи параметров эффекта._

Процесс создания силу параметры, которые не реагируют на изменения свойств времени выполнения выглядит следующим образом:

1. Создание `public` класса, который наследуется от класса [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) класса. `RoutingEffect` Класс представляет эффект независимо от платформы, который создает оболочку для внутреннего эффект, обычно от платформы.
1. Создайте конструктор, который вызывает конструктор базового класса, передавая объединение имени группы разрешения и уникальный идентификатор, который был указан на каждый класс эффект от платформы.
1. Добавьте в класс для каждого параметра передается значение свойства.

Параметры могут передаваться с эффектом, указав значения для каждого свойства при создании экземпляра эффект.

Пример приложения демонстрирует `ShadowEffect` , добавляющий тени к тексту, отображаемому элементом [ `Label` ](xref:Xamarin.Forms.Label) элемента управления. На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](clr-properties-images/shadow-effect.png "Обязанности проекта эффекта тени")

Объект [ `Label` ](xref:Xamarin.Forms.Label) управления `HomePage` настраивается путем `LabelShadowEffect` в каждом проекте под конкретную платформу. Параметры передаются к каждому `LabelShadowEffect` свойствах в `ShadowEffect` класса. Каждый `LabelShadowEffect` класс является производным от `PlatformEffect` класса для каждой платформы. Это приводит к тени, добавляемой к тексту, отображаемому элементом `Label` контролировать, как показано на следующем снимке экрана:

![](clr-properties-images/screenshots.png "Эффект тени на каждой платформе")

## <a name="creating-effect-parameters"></a>Создание силу параметров

Объект `public` класса, который наследуется от класса [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) класс должен будет создан для представления параметров эффект, как показано в следующем примере кода:

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

`ShadowEffect` Содержит четыре свойства, которые представляют параметры должны быть переданы каждой платформы `LabelShadowEffect`. Конструктор класса вызывает конструктор базового класса, передав в качестве параметра, состоящий из объединение имени группы разрешения и уникальный идентификатор, который был указан на каждый класс эффект от платформы. Таким образом, новый экземпляр `MyCompany.LabelShadowEffect` будут добавлены к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции при `ShadowEffect` создается.

## <a name="consuming-the-effect"></a>Использование эффекта

В следующем примере показан код XAML [ `Label` ](xref:Xamarin.Forms.Label) элемента управления, к которому `ShadowEffect` подключен:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

Эквивалент [ `Label` ](xref:Xamarin.Forms.Label) в C# показан в следующем примере кода:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

В обоих примерах кода, а экземпляр `ShadowEffect` со значениями указанными для каждого свойства перед добавлением элемента управления создается экземпляр класса [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции. Обратите внимание, что `ShadowEffect.Color` свойство использует значения цвета для конкретных платформ. Дополнительные сведения см. в разделе [класс устройств](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Создание эффекта на каждой платформе

В следующих разделах рассматривается реализация платформы `LabelShadowEffect` класса.

### <a name="ios-project"></a>Проект iOS

В следующем коде показано в примере `LabelShadowEffect` реализации для проекта iOS:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached` Метод извлекает `ShadowEffect` экземпляра и задает `Control.Layer` свойства для заданных значений свойств для создания тени. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

### <a name="android-project"></a>Проект Android

В следующем коде показано в примере `LabelShadowEffect` реализацию для проекта Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached` Метод извлекает `ShadowEffect` экземпляра и вызовы [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) метод для создания тени с помощью заданных значений свойств. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

### <a name="universal-windows-platform-project"></a>Проект платформы универсальной Windows

В следующем коде показано в примере `LabelShadowEffect` реализации для проекта универсальной платформы Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

Универсальная платформа Windows не дает эффекта тени и поэтому `LabelShadowEffect` реализации на обеих платформах имитирует путем добавления второй смещение [ `Label` ](xref:Xamarin.Forms.Label) первичной `Label`. `OnAttached` Метод извлекает `ShadowEffect` экземпляра, создает новый `Label`и задает некоторые свойства макета `Label`. Затем он создает тень, задав [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) свойства, управляющие цвет и расположение `Label`. `shadowLabel` Затем вставляется смещение от первичной `Label`. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

## <a name="summary"></a>Сводка

В этой статье показано использование свойств CLR для передачи параметров эффекта. Свойства CLR можно использовать для определения влияния параметров, которые не реагируют на изменения свойств среды выполнения.


## <a name="related-links"></a>Связанные ссылки

- [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Эффект](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Эффект тени (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
