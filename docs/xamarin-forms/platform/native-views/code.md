---
title: Собственные представления в C#
description: Собственные представления из iOS, Android и UWP могут существовать прямые ссылки с Xamarin.Forms страниц, созданных с помощью C#. В этой статье демонстрирует добавлять собственные представления в Xamarin.Forms макета, созданные с помощью C# и переопределить макета настраиваемые представления, чтобы исправить их измерения использование API.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: c3a79947b02e0f877fd4ea1b0ddb72486c222719
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050056"
---
# <a name="native-views-in-c"></a>Собственные представления в C#

_Собственные представления из iOS, Android и UWP могут существовать прямые ссылки с Xamarin.Forms страниц, созданных с помощью C#. В этой статье демонстрирует добавлять собственные представления в Xamarin.Forms макета, созданные с помощью C# и переопределить макета настраиваемые представления, чтобы исправить их измерения использование API._

## <a name="overview"></a>Обзор

Любой элемент управления Xamarin.Forms, который позволяет `Content` Чтобы задать, а также `Children` коллекции, можно добавить представления конкретную платформу. Например, iOS `UILabel` могут напрямую добавляться [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) свойства, или к [ `StackLayout.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) коллекции. Однако обратите внимание, что эта функциональность требует использования `#if` определяет в решениях Xamarin.Forms общий проект и не доступна из решения Xamarin.Forms .NET стандартные библиотеки.

Следующих снимках экрана показано платформой представления были добавлены в Xamarin.Forms [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/):

[![](code-images/screenshots-sml.png "StackLayout, содержащий представления платформой")](code-images/screenshots.png#lightbox "StackLayout, содержащий представления платформой")

Возможность добавления представления платформой в макете Xamarin.Forms включено по два метода расширения на каждой платформе:

- `Add` — Добавляет представление конкретную платформу, чтобы [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) коллекции макета.
- `ToView` — принимает вид конкретную платформу и помещает его в качестве Xamarin.Forms [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) , можно задать в качестве `Content` свойство элемента управления.

С помощью этих методов в общем проекте Xamarin.Forms требует импорта соответствующего пространства имен Xamarin.Forms платформ:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** — Xamarin.Forms.Platform.Android
- **Универсальная платформа Windows (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Добавление представлений платформой на каждой платформе

Ниже описаны процедуры добавления представления платформой в Xamarin.Forms макет на каждой платформе.

### <a name="ios"></a>iOS

В следующем примере кода показано, как добавить `UILabel` для [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) и [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

Предполагается, что `stackLayout` и `contentView` экземпляры ранее были созданы в XAML и C#.

### <a name="android"></a>Android

В следующем примере кода показано, как добавить `TextView` для [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) и [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

Предполагается, что `stackLayout` и `contentView` экземпляры ранее были созданы в XAML и C#.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В следующем примере кода показано, как добавить `TextBlock` для [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) и [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

Предполагается, что `stackLayout` и `contentView` экземпляры ранее были созданы в XAML и C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Переопределения измерений платформы пользовательских представлений

Настраиваемые представления на каждой платформе часто только правильной реализации измерения для макета сценария, для которой они были спроектированы. Например пользовательское представление может были разработаны для занимать половины ширины элемента устройства. Тем не менее после которой совместно с другими пользователями, настраиваемое представление может потребоваться занимая полной ширины элемента устройства. Таким образом может потребоваться переопределить реализацию настраиваемых представлений измерения при, повторно используемые в макете Xamarin.Forms. По этой причине `Add` и `ToView` методы расширения предоставляют переопределений, которые позволяют указать делегаты измерения, которые можно переопределить пользовательское представление макета при ее добавлении в макет Xamarin.Forms.

Приведенные ниже показано, как переопределить макета настраиваемые представления, чтобы исправить их измерения использование API.

### <a name="ios"></a>iOS

В следующем примере кода показан `CustomControl` класс, унаследованный от класса `UILabel`:

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

Добавляемый экземпляр этого представления [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), как показано в следующем примере кода:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

Тем не менее поскольку `CustomControl.SizeThatFits` переопределения всегда возвращает высоту 150, представление отображается с пустым пространством выше и ниже его, как показано на следующем снимке экрана:

![](code-images/ios-bad-measurement.png "пользовательский элемент управления с неправильной реализацией SizeThatFits iOS")

Решением этой проблемы является предоставление `GetDesiredSizeDelegate` реализацию, как показано в следующем примере кода:

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

Этот метод использует ширине, предоставляемой `CustomControl.SizeThatFits` метода, но замещает высоту 150 для высоты 70. Когда `CustomControl` добавляется экземпляр [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), `FixSize` метод может быть указан как `GetDesiredSizeDelegate` для исправления поврежденных измерения, предоставленные `CustomControl` класса:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Это приводит к в представление правильности отображения без пустое пространство выше и ниже его, как показано на следующем снимке экрана:

![](code-images/ios-good-measurement.png "пользовательский элемент управления с переопределением GetDesiredSize iOS")

### <a name="android"></a>Android

В следующем примере кода показан `CustomControl` класс, унаследованный от класса `TextView`:

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

Добавляемый экземпляр этого представления [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), как показано в следующем примере кода:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

Тем не менее поскольку `CustomControl.OnMeasure` переопределения всегда возвращает половины ширины запрошенного, будет отображено представление занимающих только половину ширины элемента устройства, как показано на следующем снимке экрана:

![](code-images/android-bad-measurement.png "Android пользовательский элемент управления с реализацией неправильный OnMeasure")

Решением этой проблемы является предоставление `GetDesiredSizeDelegate` реализацию, как показано в следующем примере кода:

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

Этот метод использует ширине, предоставляемой `CustomControl.OnMeasure` метода, но умножает его на два. Когда `CustomControl` добавляется экземпляр [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), `FixSize` метод может быть указан как `GetDesiredSizeDelegate` для исправления поврежденных измерения, предоставленные `CustomControl` класса:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Это приводит к тем, что пользовательское представление отображается неправильно, занимающих ширину устройства, как показано на следующем снимке экрана:

![](code-images/android-good-measurement.png "Android пользовательский элемент управления с делегатом пользовательские GetDesiredSize")

### <a name="universal-windows-platform"></a>Универсальная платформа Windows 

В следующем примере кода показан `CustomControl` класс, унаследованный от класса `Panel`:

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

Добавляемый экземпляр этого представления [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), как показано в следующем примере кода:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

Тем не менее поскольку `CustomControl.ArrangeOverride` переопределения всегда возвращает половины ширины запрошенного, представление будет обрезано до половины ширины элемента устройства, как показано на следующем снимке экрана:

![](code-images/winrt-bad-measurement.png "Пользовательский элемент управления UWP с реализацией неправильный ArrangeOverride")

Решением этой проблемы является предоставление `ArrangeOverrideDelegate` реализация, при добавлении представления [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), как показано в следующем примере кода:

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

Этот метод использует ширине, предоставляемой `CustomControl.ArrangeOverride` метода, но умножает его на два. Это приводит к тем, что пользовательское представление отображается неправильно, занимающих ширину устройства, как показано на следующем снимке экрана:

![](code-images/winrt-good-measurement.png "Пользовательский элемент управления UWP с делегатом ArrangeOverride")

## <a name="summary"></a>Сводка

В этой статье описано, как добавить собственные представления в Xamarin.Forms макета, созданные с помощью C# и переопределение макета настраиваемые представления, чтобы исправить их измерения использование API.


## <a name="related-links"></a>Связанные ссылки

- [NativeEmbedding (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Исходные формы](~/xamarin-forms/platform/native-forms.md)
