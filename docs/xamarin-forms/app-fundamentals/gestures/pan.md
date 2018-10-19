---
title: Добавление распознавателя жестов pan
description: В этой статье объясняется, как использовать жест pan горизонтально и вертикально Сдвиг изображения, таким образом, чтобы все содержимое образа можно просмотреть, когда оно отображается в окне просмотра меньше, чем размеры изображения.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 59e9f4c61bda86faa5a55d70ef91411adb14da6d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "38996810"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Добавление распознавателя жестов pan

_Жест pan используется для обнаружения движения пальца по экрану и применения к содержимому, перемещение и реализуется с помощью `PanGestureRecognizer` класса. Распространенный сценарий для жест pan является по горизонтали и вертикали Сдвиг изображения, таким образом, чтобы все содержимое образа можно просмотреть, когда оно отображается в окне просмотра меньше, чем размеры изображения. Это достигается путем перемещения изображения в области просмотра и демонстрируется в этой статье._

Чтобы сделать элемент пользовательского интерфейса, перемещаемые с жестом pan, создать [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) экземпляра, обрабатывать [ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) событий, и добавьте новый распознаватель жестов для [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) коллекции элемента пользовательского интерфейса. В следующем коде показано в примере `PanGestureRecognizer` подключен к [ `Image` ](xref:Xamarin.Forms.Image) элемент:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Это можно также сделать в XAML, как показано в следующем примере кода:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

Код для `OnPanUpdated` обработчик событий добавляется в файл с выделенным кодом:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Правильный панорамирования в Android требует [пакет NuGet Xamarin.Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) как минимум.

## <a name="creating-a-pan-container"></a>Создание контейнера pan

Этот раздел содержит универсальный вспомогательный класс, который выполняет прокрутку свободной формы, которой обычно подходит для перехода в пределах изображения или карты. Обработка жест pan для выполнения этой операции требуется некоторые расчеты для преобразования пользовательского интерфейса. Это математические используется для сдвига только в пределах границ элемента оболочку пользовательского интерфейса. Следующий пример кода демонстрирует класс `PanContainer`:

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Этот класс можно обернуть вокруг элемента пользовательского интерфейса таким образом, жест будет сдвинуть элемент оболочку пользовательского интерфейса. В следующем примере показан код XAML `PanContainer` упаковки [ `Image` ](xref:Xamarin.Forms.Image) элемент:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

В следующем примере кода показано как `PanContainer` заключает в оболочку [ `Image` ](xref:Xamarin.Forms.Image) элемента на странице C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

В обоих примерах [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) и [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) свойствам присваивается значения ширины и высоты изображения.

Когда [ `Image` ](xref:Xamarin.Forms.Image) элемент получает жест pan, изображение, показываемое, на которые будет панорамировании окна. Панорамирование выполняется по `PanContainer.OnPanUpdated` метод, который показан в следующем примере кода:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Этот метод обновляет содержимое для просмотра элементом оболочку пользовательского интерфейса, в зависимости от пользователя pan жест. Это достигается с помощью значения [ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) и [ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) свойства [ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs) экземпляр для вычисления направления и расстояние сдвига. `App.ScreenWidth` И `App.ScreenHeight` свойства укажите высоту и ширину окна просмотра и заданы ширина экрана и значения высоты экрана устройства, соответствующие проекты под конкретные платформы. Элемент оболочку пользовательского затем панорамировании окна, задав его [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) вычисляемые значения для свойств.

Когда прокрутку содержимого в элемент, который не занимает весь экран, высоту и ширину окна просмотра можно получить из этого элемента [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) и [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) свойства.

> [!NOTE]
> Отображение изображения с высоким разрешением, может значительно увеличивать объем памяти приложения. Таким образом они должны быть созданы только при необходимости и как только приложение больше не требуется, их необходимо освободить. Дополнительные сведения см. в разделе [Оптимизация графических ресурсов](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="related-links"></a>Связанные ссылки

- [PanGesture (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
