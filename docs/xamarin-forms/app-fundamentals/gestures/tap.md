---
title: Добавление распознавателя жестов касания
description: В этой статье объясняется, как использовать жест касания для обнаружения tap в приложении Xamarin.Forms. Обнаружение TAP реализуется с помощью класса TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: a28afb30770f15861aef06643e7f51070199ea9b
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "38994858"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Добавление распознавателя жестов касания

_Жест касания используется для обнаружения tap и реализуется с помощью класса TapGestureRecognizer._

Чтобы сделать активным с помощью жеста касания элемента пользовательского интерфейса, создать [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) экземпляра, обрабатывать [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) событий и добавьте новый распознаватель жестов для [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) коллекции элемента пользовательского интерфейса. В следующем коде показано в примере `TapGestureRecognizer` подключен к [ `Image` ](xref:Xamarin.Forms.Image) элемент:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

По умолчанию образ будет отвечать на одного касания. Задайте [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) свойство дважды щелкните (или дополнительные касания при необходимости).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Когда [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) имеет значение выше, обработчик событий будет выполняться только если касания происходят в пределах заданного периода времени (это период не настраивается). Если второй (или последующих) касания не происходят в течение этого периода эффективно игнорируются и перезапускает «tap count».

<a name="Using_Xaml" />

## <a name="using-xaml"></a>С помощью Xaml

Распознаватель жестов можно добавить к элементу управления в Xaml, использование присоединенных свойств. Синтаксис для добавления [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) изображения, показано ниже (в данном случае определение *двойного касания* событий):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

Код для обработчика событий (в данном) увеличивает значение счетчика и изображения изменяется от черного &amp; белый.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>С помощью ICommand

Приложения, использующие шаблон Model-View-ViewModel (MVVM), обычно используют `ICommand` вместо привязки обработчиков событий напрямую. [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) Можно легко поддерживать `ICommand` либо задав привязку в коде:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

или с помощью Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

Полный код для этой модели представления можно найти в образце. Соответствующие `Command` детали реализации, показаны ниже:

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```


## <a name="related-links"></a>Связанные ссылки

- [TapGesture (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
