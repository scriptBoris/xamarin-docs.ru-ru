---
title: Исходные представления в C#
description: Исходные представления из iOS, Android и UWP можно непосредственно ссылаться из страницы Xamarin.Forms, созданные с помощью C#. В этой статье показано, как добавить исходные представления в макете Xamarin.Forms, созданные с помощью C# и переопределение макет пользовательские представления, чтобы исправить их измерения использования API.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 8587cade1c5b4a6882f21603ee869f94f38fd04a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058838"
---
# <a name="native-views-in-c"></a>Исходные представления в C#

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)

_Исходные представления из iOS, Android и UWP можно непосредственно ссылаться из страницы Xamarin.Forms, созданные с помощью C#. В этой статье показано, как добавить исходные представления в макете Xamarin.Forms, созданные с помощью C# и переопределение макет пользовательские представления, чтобы исправить их измерения использования API._

## <a name="overview"></a>Обзор

Любой элемент управления Xamarin.Forms, который позволяет `Content` задаваемый, или с `Children` коллекции, можно добавить представления платформы. Например, iOS `UILabel` можно напрямую добавить [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) свойство, или к [ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children) коллекции. Тем не менее, обратите внимание, что эта функция требует использования `#if` определяет в решениях общий проект Xamarin.Forms и не доступна из решений Xamarin.Forms .NET Standard library.

Далее на снимках экрана демонстрируют платформой представления были добавлены в Xamarin.Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "Содержит представления платформы StackLayout")](code-images/screenshots.png#lightbox "StackLayout, содержащий представления платформы")

Возможность добавления представления с платформой Xamarin.Forms макет включен по два метода расширения на каждой платформе:

- `Add` — Добавляет представление платформы для [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) коллекции макета.
- `ToView` — принимает представления платформы и помещает его в качестве Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View) , можно задать в качестве `Content` свойства элемента управления.

С помощью этих методов в общий проект Xamarin.Forms требует импорта соответствующего пространства имен платформы Xamarin.Forms:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** — Xamarin.Forms.Platform.Android
- **Платформа универсальной Windows (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Добавление представлений платформы на каждой платформе

Ниже описаны процедуры добавления представления платформы в макет Xamarin.Forms на каждой платформе.

### <a name="ios"></a>iOS

В следующем примере кода показано, как добавить `UILabel` для [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) и [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

Предполагается, что `stackLayout` и `contentView` ранее были созданы экземпляры в XAML или C#.

### <a name="android"></a>Android

В следующем примере кода показано, как добавить `TextView` для [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) и [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

Предполагается, что `stackLayout` и `contentView` ранее были созданы экземпляры в XAML или C#.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В следующем примере кода показано, как добавить `TextBlock` для [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) и [ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

Предполагается, что `stackLayout` и `contentView` ранее были созданы экземпляры в XAML или C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Переопределение показателей платформы для пользовательских представлений

Пользовательские представления на каждой платформе часто только правильно реализовать измерения для макета сценария, для которого они были созданы. Например пользовательское представление может рассчитаны на занимать половины ширины элемента устройства. Однако после совместно с другими пользователями, пользовательское представление может потребоваться занимают все доступные ширину устройства. Таким образом может потребоваться переопределить реализацию измерения пользовательские представления, когда будут повторно использованы в макете Xamarin.Forms. По этой причине `Add` и `ToView` методы расширения предоставляют переопределений, которые позволяют указать делегаты измерения, которые можно переопределить макет пользовательского представления, при добавлении в макет Xamarin.Forms.

Ниже показано, как переопределять макет пользовательские представления, чтобы исправить их измерения использования API.

### <a name="ios"></a>iOS

В следующем коде показано в примере `CustomControl` класс, унаследованный от `UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Экземпляр этого представления добавляется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), как показано в следующем примере кода:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Тем не менее поскольку `CustomControl.SizeThatFits` переопределение всегда возвращает высоту 150, представление будет отображаться с пустым отступа сверху и снизу, как показано на следующем снимке экрана:

![](code-images/ios-bad-measurement.png "пользовательский элемент управления с реализацией SizeThatFits iOS")

Решение этой проблемы — предоставить `GetDesiredSizeDelegate` реализации, как показано в следующем примере кода:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Этот метод использует ширине, предоставляемой `CustomControl.SizeThatFits` метод, но заменяет высоту 150 для высоты 70. При `CustomControl` добавляется экземпляр [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), `FixSize` метод может быть указан как `GetDesiredSizeDelegate` для исправления поврежденных измерения, предоставляемые `CustomControl` класса:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

В результате пользовательское представление правильности отображения без пустого пространства, сверху и снизу, как показано на следующем снимке экрана:

![](code-images/ios-good-measurement.png "пользовательский элемент управления с переопределением GetDesiredSize iOS")

### <a name="android"></a>Android

В следующем коде показано в примере `CustomControl` класс, унаследованный от `TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Экземпляр этого представления добавляется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), как показано в следующем примере кода:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Тем не менее поскольку `CustomControl.OnMeasure` переопределение всегда возвращает половины ширины запрошенного, представление будет отображаться резервирует только половина доступную ширину устройства, как показано на следующем снимке экрана:

![](code-images/android-bad-measurement.png "Android пользовательский элемент управления с реализацией неправильный OnMeasure")

Решение этой проблемы — предоставить `GetDesiredSizeDelegate` реализации, как показано в следующем примере кода:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Этот метод использует ширине, предоставляемой `CustomControl.OnMeasure` метод, но умножает его на два. При `CustomControl` добавляется экземпляр [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), `FixSize` метод может быть указан как `GetDesiredSizeDelegate` для исправления поврежденных измерения, предоставляемые `CustomControl` класса:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

В результате, пользовательское представление отображается правильно, занимающий ширину устройства, как показано на следующем снимке экрана:

![](code-images/android-good-measurement.png "Android пользовательский элемент управления с помощью пользовательских GetDesiredSize делегата")

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В следующем коде показано в примере `CustomControl` класс, унаследованный от `Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Экземпляр этого представления добавляется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), как показано в следующем примере кода:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Тем не менее поскольку `CustomControl.ArrangeOverride` переопределение всегда возвращает половины ширины запрошенного, представление будет обрезано до половины ширины элемента устройства, как показано на следующем снимке экрана:

![](code-images/winrt-bad-measurement.png "Пользовательский элемент управления универсальной платформы Windows с реализацией неправильный ArrangeOverride")

Решение этой проблемы — предоставить `ArrangeOverrideDelegate` реализация, при добавлении представления [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), как показано в следующем примере кода:

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Этот метод использует ширине, предоставляемой `CustomControl.ArrangeOverride` метод, но умножает его на два. В результате, пользовательское представление отображается правильно, занимающий ширину устройства, как показано на следующем снимке экрана:

![](code-images/winrt-good-measurement.png "Пользовательский элемент управления универсальной платформы Windows с делегатом ArrangeOverride")

## <a name="summary"></a>Сводка

В этой статье описываются способы добавления собственного представления макета Xamarin.Forms, созданные с помощью C# и переопределение макет пользовательские представления, чтобы исправить их измерения использования API.


## <a name="related-links"></a>Связанные ссылки

- [NativeEmbedding (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Исходные формы](~/xamarin-forms/platform/native-forms.md)
