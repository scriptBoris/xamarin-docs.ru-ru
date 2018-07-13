---
title: Передача параметров эффект как вложенные свойства
description: Вложенные свойства можно использовать для определения параметров эффект, реагирующие на изменения свойств среды выполнения. В этой статье демонстрируется использование присоединенных свойств для передачи параметров эффект, а также изменение параметра во время выполнения.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996455"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Передача параметров эффект как вложенные свойства

_Вложенные свойства можно использовать для определения параметров эффект, реагирующие на изменения свойств среды выполнения. В этой статье демонстрируется использование присоединенных свойств для передачи параметров эффект, а также изменение параметра во время выполнения._

Процесс для создания эффекта параметров, которые реагируют на изменения свойств времени выполнения выглядит следующим образом:

1. Создание `static` класс, который содержит вложенное свойство для каждого параметра должны быть переданы эффект.
1. Добавьте дополнительные присоединенное свойство к классу, который будет использоваться для управления, добавление или удаление эффекта элемент управления, который будет подключен к классу. Убедитесь, что это присоединенного свойства регистры `propertyChanged` делегат, который будет выполняться при изменении значения свойства.
1. Создание `static` методы получения и задания для каждого вложенного свойства зависимостей.
1. Реализовать логику в `propertyChanged` делегат для добавления и удаления эффект.
1. Реализовать вложенный класс внутри `static` класс с именем после эффекта, который подклассы `RoutingEffect` класса. Для конструктора вызовите конструктор базового класса, передавая объединение имени группы разрешения и уникальный идентификатор, который был указан на каждый класс эффект от платформы.

Параметры могут передаваться с эффектом, добавив вложенные свойства и значения свойств, соответствующий элемент управления. Кроме того параметры могут быть изменены во время выполнения, указав новое значение вложенного свойства.

> [!NOTE]
> Присоединенное свойство — это специальная привязываемые свойства, определенные в одном классе, но присоединенного к другим объектам и распознается в XAML как атрибуты, которые содержат класс и имя свойства, разделенные точкой. Дополнительные сведения см. в разделе [присоединенного свойства](~/xamarin-forms/xaml/attached-properties.md).

Пример приложения демонстрирует `ShadowEffect` , добавляющий тени к тексту, отображаемому элементом [ `Label` ](xref:Xamarin.Forms.Label) элемента управления. Кроме того во время выполнения можно изменить цвет тени. На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](attached-properties-images/shadow-effect.png "Обязанности проекта эффекта тени")

Объект [ `Label` ](xref:Xamarin.Forms.Label) управления `HomePage` настраивается путем `LabelShadowEffect` в каждом проекте под конкретную платформу. Параметры передаются к каждому `LabelShadowEffect` через присоединенные свойства в `ShadowEffect` класса. Каждый `LabelShadowEffect` класс является производным от `PlatformEffect` класса для каждой платформы. Это приводит к тени, добавляемой к тексту, отображаемому элементом `Label` контролировать, как показано на следующем снимке экрана:

![](attached-properties-images/screenshots.png "Эффект тени на каждой платформе")

## <a name="creating-effect-parameters"></a>Создание силу параметров

Объект `static` класс должен будет создан для представления параметров эффект, как показано в следующем примере кода:

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

`ShadowEffect` Содержит пять присоединенных свойств, с `static` методы получения и задания для каждого вложенного свойства зависимостей. Четыре эти свойства представляют параметры должны быть переданы каждой платформы `LabelShadowEffect`. `ShadowEffect` Класс также определяет `HasShadow` присоединенное свойство, которое используется для управления, добавление или удаление эффекта к элементу управления, `ShadowEffect` класс присоединяется к. Это присоединенного свойства регистры `OnHasShadowChanged` метод, который будет выполняться при изменении значения свойства. Этот метод добавляет или удаляет зависимости от значения `HasShadow` вложенного свойства зависимостей.

Вложенный `LabelShadowEffect` класса, который подклассы [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) класса поддерживает эффект добавления и удаления. `RoutingEffect` Класс представляет эффект независимо от платформы, который создает оболочку для внутреннего эффект, обычно от платформы. Это упрощает процесс удаления эффекта, так как нет сведений о типе доступа во время компиляции для создания эффекта от платформы. `LabelShadowEffect` Конструктор вызывает конструктор базового класса, передав в качестве параметра, состоящий из объединение имени группы разрешения и уникальный идентификатор, который был указан на каждый класс эффект от платформы. Это позволяет эффект добавления и удаления в `OnHasShadowChanged` метода, как показано ниже:

- **В силу сложения** — новый экземпляр класса `LabelShadowEffect` добавляется к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции. Этот параметр заменяет использование [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) метод, чтобы добавить эффект.
- **В силу удаления** — первый экземпляр `LabelShadowEffect` в элементе управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции извлекается и удалены.

## <a name="consuming-the-effect"></a>Использование эффекта

Каждой платформы `LabelShadowEffect` путем добавления вложенного свойства, которые могут использоваться [ `Label` ](xref:Xamarin.Forms.Label) управления, как показано в следующем примере кода XAML:

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Установка `ShadowEffect.HasShadow` вложенное свойство, чтобы `true` выполняет `ShadowEffect.OnHasShadowChanged` метод, который добавляет или удаляет `LabelShadowEffect` для [ `Label` ](xref:Xamarin.Forms.Label) элемента управления. В обоих примерах кода `ShadowEffect.Color` присоединенное свойство предоставляет специфические для платформы цветовых значений. Дополнительные сведения см. в разделе [класс устройств](~/xamarin-forms/platform/device.md).

Кроме того [ `Button` ](xref:Xamarin.Forms.Button) позволяет цвет тени, должен быть изменен во время выполнения. Когда `Button` нажатии следующий код изменяет цвет тени, задав `ShadowEffect.Color` вложенного свойства зависимостей:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Использование эффекта со стилем

Эффекты, которые могут быть использованы, добавив вложенные свойства в элемент управления также могут использоваться стиль. В следующем примере показан код XAML *явные* стиля для эффекта тени, который может быть применен к [ `Label` ](xref:Xamarin.Forms.Label) элементов управления:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style) Могут применяться к [ `Label` ](xref:Xamarin.Forms.Label) , задав его [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства `Style` экземпляра с помощью `StaticResource`расширения разметки, как показано в следующем примере кода:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Дополнительные сведения о стилях см. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md).

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

`OnAttached` Метод вызывает методы, которые получают значения присоединенного свойства, с помощью `ShadowEffect` методы получения и который задать `Control.Layer` свойства к значениям свойств для создания тени. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

#### <a name="responding-to-property-changes"></a>Реагирование на изменения свойств

Если какие-либо `ShadowEffect` присоединенного изменение значения свойства во время выполнения, на силу должно отвечать путем отображения изменений. Переопределенная версия `OnElementPropertyChanged` метод в классе эффект от платформы — место для реагирования на изменения может быть привязано, как показано в следующем примере кода:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` Метод обновляет radius, цвет и смещение тени, при условии, что соответствующий `ShadowEffect` изменилось значение вложенного свойства. Проверяет наличие измененного свойства должны быть выполнены, так как это переопределение может вызываться многократно.

### <a name="android-project"></a>Проект Android

В следующем коде показано в примере `LabelShadowEffect` реализацию для проекта Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

`OnAttached` Метод вызывает методы, которые получают значения присоединенного свойства, с помощью `ShadowEffect` методы получения и вызывает метод, который вызывает [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) метод для создания тени, используя значения свойств. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

#### <a name="responding-to-property-changes"></a>Реагирование на изменения свойств

Если какие-либо `ShadowEffect` присоединенного изменение значения свойства во время выполнения, на силу должно отвечать путем отображения изменений. Переопределенная версия `OnElementPropertyChanged` метод в классе эффект от платформы — место для реагирования на изменения может быть привязано, как показано в следующем примере кода:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` Метод обновляет radius, цвет и смещение тени, при условии, что соответствующий `ShadowEffect` изменилось значение вложенного свойства. Проверяет наличие измененного свойства должны быть выполнены, так как это переопределение может вызываться многократно.

### <a name="universal-windows-platform-project"></a>Проект платформы универсальной Windows

В следующем коде показано в примере `LabelShadowEffect` реализации для проекта универсальной платформы Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

Универсальная платформа Windows не дает эффекта тени и поэтому `LabelShadowEffect` реализации на обеих платформах имитирует путем добавления второй смещение [ `Label` ](xref:Xamarin.Forms.Label) первичной `Label`. `OnAttached` Метод создает новый `Label` и задает некоторые свойства макета `Label`. Затем он вызывает методы, которые получают значения присоединенного свойства, с помощью `ShadowEffect` методы получения и создает тень, задав [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) свойства, управляющие цвет и расположение `Label`. `shadowLabel` Затем вставляется смещение от первичной `Label`. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, к которой подключен эффект не `Control.Layer` свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

#### <a name="responding-to-property-changes"></a>Реагирование на изменения свойств

Если какие-либо `ShadowEffect` присоединенного изменение значения свойства во время выполнения, на силу должно отвечать путем отображения изменений. Переопределенная версия `OnElementPropertyChanged` метод в классе эффект от платформы — место для реагирования на изменения может быть привязано, как показано в следующем примере кода:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` Метод обновляет цвет или смещение тени, при условии, что соответствующий `ShadowEffect` изменилось значение вложенного свойства. Проверяет наличие измененного свойства должны быть выполнены, так как это переопределение может вызываться многократно.

## <a name="summary"></a>Сводка

В этой статье показано использование присоединенных свойств для передачи параметров эффект, а также изменение параметра во время выполнения. Вложенные свойства можно использовать для определения параметров эффект, реагирующие на изменения свойств среды выполнения.


## <a name="related-links"></a>Связанные ссылки

- [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Эффект](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Эффект тени (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
