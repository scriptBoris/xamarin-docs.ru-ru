---
title: Добавление распознавателя жестов касания
description: В этой статье объясняется, как использовать жест касания для обнаружения tap в приложении Xamarin.Forms. Обнаружение TAP реализуется с помощью класса TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: e602ae1f140640d9a895b65d78feab3d0a3b7861
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994858"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Добавление распознавателя жестов касания

_Жест касания используется для обнаружения tap и реализуется с помощью класса TapGestureRecognizer._

## <a name="overview"></a>Обзор

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

Приложения, использующие шаблон Mvvm, обычно используют `ICommand` вместо привязки обработчиков событий напрямую. [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) Можно легко поддерживать `ICommand` либо задав привязку в коде:

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

## <a name="summary"></a>Сводка

Используется для обнаружения tap жеста касания и реализуется с помощью [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) класса. Можно указать числом ответвлений для распознавания дважды щелкните (или троекратного нажатия или более касается) поведение.


## <a name="related-links"></a>Связанные ссылки

- [TapGesture (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
