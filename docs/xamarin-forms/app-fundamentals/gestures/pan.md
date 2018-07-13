---
title: Добавление распознавателя жестов Pan
description: В этой статье объясняется, как жест сдвиг по горизонтали и по вертикали перетащите изображение, чтобы все содержимое образа можно просмотреть, когда оно отображается в окне просмотра меньше, чем размеры изображения.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 45c0a1452916f193236e5ba741f8e8e19b6691aa
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996810"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Добавление распознавателя жестов Pan

_Жест pan используется для обнаружения перетаскивания и реализуется с помощью класса PanGestureRecognizer. Распространенный сценарий для жест pan — по горизонтали и вертикали перетащить изображение, таким образом, чтобы все содержимое образа можно просмотреть, когда оно отображается в окне просмотра меньше, чем размеры изображения. Это достигается путем перемещения изображения в области просмотра и демонстрируется в этой статье._

## <a name="overview"></a>Обзор

Чтобы сделать элемент пользовательского интерфейса, перетаскиваемые с жестом pan, создать [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) экземпляра, обрабатывать [ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) событий, и добавьте новый распознаватель жестов для [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) коллекции элемента пользовательского интерфейса. В следующем коде показано в примере `PanGestureRecognizer` подключен к [ `Image` ](xref:Xamarin.Forms.Image) элемент:

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

## <a name="creating-a-pan-container"></a>Создание контейнера Pan

Этот раздел содержит универсальный вспомогательный класс, который выполняет прокрутку свободной формы, которой обычно подходит для перехода в пределах изображения или карты. Обработка жест pan для выполнения операции перетаскивания требуется некоторые расчеты для преобразования пользовательского интерфейса. Это математические позволяет перетаскивать только в пределах границ элемента оболочку пользовательского интерфейса. Следующий пример кода демонстрирует класс `PanContainer`:

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

Этот класс можно обернуть вокруг элемента пользовательского интерфейса таким образом, жест pan перетащите элемент оболочку пользовательского интерфейса. В следующем примере показан код XAML `PanContainer` упаковки [ `Image` ](xref:Xamarin.Forms.Image) элемент:

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

Когда [ `Image` ](xref:Xamarin.Forms.Image) элемент получает жест pan, будет перетащить изображение, показываемое. Перетаскивание выполняется путем `PanContainer.OnPanUpdated` метод, который показан в следующем примере кода:

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

Этот метод обновляет содержимое для просмотра элементом оболочку пользовательского интерфейса, в зависимости от пользователя pan жест. Это достигается с помощью значения [ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) и [ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) свойства [ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs) экземпляр для вычисления направления и расстояние сдвига. `App.ScreenWidth` И `App.ScreenHeight` свойства укажите высоту и ширину окна просмотра и заданы ширина экрана и значения высоты экрана устройства, соответствующие проекты под конкретные платформы. Элемент оболочку пользовательского затем перетащить, задав его [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) и [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) вычисляемые значения для свойств.

Когда прокрутку содержимого в элемент, который не занимает весь экран, высоту и ширину окна просмотра можно получить из этого элемента [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) и [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) свойства.

> [!NOTE]
> Отображение изображения с высоким разрешением, может значительно увеличивать объем памяти приложения. Таким образом они должны быть созданы только при необходимости и как только приложение больше не требуется, их необходимо освободить. Дополнительные сведения см. в разделе [Оптимизация графических ресурсов](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="summary"></a>Сводка

Жест pan используется для выявления перетаскивания и реализуется с помощью [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) класса.



## <a name="related-links"></a>Связанные ссылки

- [PanGesture (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
