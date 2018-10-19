---
title: Добавление распознавателя жестов жестом сжатия
description: В этой статье объясняется, как использовать жест «щипок» для выполнения интерактивного масштабирования изображения в расположении жестом сжатия.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: f67cbb136c42a4bc476c1715ea6fd15255d71dc7
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998707"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Добавление распознавателя жестов жестом сжатия

_Жест «щипок» используется для выполнения интерактивного масштабирования и реализуется с помощью класса PinchGestureRecognizer. Распространенный сценарий для жест «щипок» — для выполнения интерактивного масштабирования изображения в расположении жестом сжатия. Это достигается путем масштабирования содержимого окна просмотра и демонстрируется в этой статье._

Чтобы сделать элемент пользовательского интерфейса, масштабируемый с жест «щипок», создать [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) экземпляра, обрабатывать [ `PinchUpdated` ](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) событий, и добавьте новый распознаватель жестов для [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) коллекции элемента пользовательского интерфейса. В следующем коде показано в примере `PinchGestureRecognizer` подключен к [ `Image` ](xref:Xamarin.Forms.Image) элемент:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Это можно также сделать в XAML, как показано в следующем примере кода:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Код для `OnPinchUpdated` обработчик событий добавляется в файл с выделенным кодом:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Создание контейнера PinchToZoom

Обработка жест «щипок», чтобы выполнить операцию масштабирования требуются некоторые расчеты для преобразования пользовательского интерфейса. Этот раздел содержит универсальный вспомогательный класс для выполнения математических операций, который может использоваться для интерактивного изменения масштаба любой элемент пользовательского интерфейса. Следующий пример кода демонстрирует класс `PinchToZoomContainer`:

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

Этот класс можно обернуть вокруг элемента пользовательского интерфейса таким образом, жест «щипок» будет масштаб элемент оболочку пользовательского интерфейса. В следующем примере показан код XAML `PinchToZoomContainer` упаковки [ `Image` ](xref:Xamarin.Forms.Image) элемент:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

В следующем примере кода показано как `PinchToZoomContainer` заключает в оболочку [ `Image` ](xref:Xamarin.Forms.Image) элемента на странице C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Когда [ `Image` ](xref:Xamarin.Forms.Image) элемент получает жест жестом сжатия, изображение, показываемое будет быть увеличена или уменьшить. Масштабирование выполняется путем `PinchZoomContainer.OnPinchUpdated` метод, который показан в следующем примере кода:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Этот метод обновляет уровень масштаба элементом оболочку пользовательского интерфейса, в соответствии с жестом сжатия жест пользователя. Это достигается с помощью значения [ `Scale` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [ `ScaleOrigin` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) и [ `Status` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) свойства [ `PinchGestureUpdatedEventArgs` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) экземпляра, для которого требуется вычислить коэффициент масштабирования, применяемый в начале координат жест «щипок». Элемент оболочку пользовательского затем развернуто в начале координат жест «щипок», задав его [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY), и [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) вычисляемые значения свойств.

## <a name="related-links"></a>Связанные ссылки

- [PinchGesture (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
