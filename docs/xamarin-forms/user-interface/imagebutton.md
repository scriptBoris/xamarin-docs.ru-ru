---
title: Xamarin.Forms ImageButton
description: ImageButton отображает изображение и реагирует на касание или щелчок, который направляет приложению выполнения определенной задачи.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 26f69f9ac2d315e1076cab6f7c1534cbdd39a9b3
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52173910"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

_ImageButton отображает изображение и реагирует на касание или щелчок, который направляет приложению выполнения определенной задачи._

`ImageButton` Просмотреть объединяет [ `Button` ](xref:Xamarin.Forms.Button) представление и [ `Image` ](xref:Xamarin.Forms.Image) представление для создания кнопки, содержимое которого является изображением. Пользователь нажимает клавишу `ImageButton` пальцем или щелкает его с помощью мыши, чтобы настроить приложение для выполнения определенной задачи. Однако в отличие от `Button` представлении `ImageButton` представление не поддерживает понятие текста и внешний вид текста.

> [!NOTE]
> Хотя [ `Button` ](xref:Xamarin.Forms.Button) определяет представление [ `Image` ](xref:Xamarin.Forms.Button.Image) свойство, которое позволяет отображать изображение на `Button`, это свойство предназначено для использования при отображении небольшой значок рядом с полем `Button` текста.

В примерах кода в этом руководстве, взяты из [FormsGallery пример](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/).

## <a name="setting-the-image-source"></a>Установка источника изображения

`ImageButton` Определяет `Source` свойство, которое должно быть присвоено изображение для отображения на кнопке, в качестве источника изображения файла, URI, ресурс или поток. Дополнительные сведения о загрузке изображений из различных источников, см. в разделе [изображений в Xamarin.Forms](images.md).

В следующем примере показано, как создать экземпляр `ImageButton` в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Source` Свойство задает изображение, отображаемое в `ImageButton`. В этом примере задается в локальный файл, которые будут загружены из каждого проекта платформы, что на следующих снимках экрана:

[![Основные ImageButton](imagebutton-images/BasicImageButton.png "основные ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "основные ImageButton")

По умолчанию `ImageButton` представляет собой прямоугольник, но можно дать углы округленное ИТ с помощью `CornerRadius` свойство. Дополнительные сведения о `ImageButton` внешний вид, см. в разделе [внешний вид ImageButton](#imagebutton-appearance).

В следующем примере показано, как создать страницу, которая является функциональным эквивалентом предыдущего примера XAML, но полностью в C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>Обработка ImageButton нажимает кнопку

`ImageButton` Определяет `Clicked` событие, возникающее, когда пользователь касается `ImageButton` с указателем мыши или палец. Событие при отжатии кнопки мыши или палец поверхности от `ImageButton`. `ImageButton` Должен иметь его `IsEnabled` свойству присвоено `true` реагировать на касания.

В следующем примере показано, как создать экземпляр `ImageButton` в XAML и обработки его `Clicked` событий:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Clicked` Событий присваивается обработчик событий с именем `OnImageButtonClicked` , расположенную в файл с выделенным кодом:

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

Когда `ImageButton` шифрованию, `OnImageButtonClicked` выполнения метода. `sender` Аргумент `ImageButton` инициатором этого события. Это можно использовать для доступа к `ImageButton` объекта, или чтобы различать несколько `ImageButton` объектов с одинаковым `Clicked` событий.

Данный конкретный `Clicked` обработчик увеличивает значение счетчика и выводит значение счетчика в [ `Label` ](xref:Xamarin.Forms.Label):

[![Основные щелчок ImageButton](imagebutton-images/ImageButton.png "основные щелчок ImageButton")](imagebutton-images/ImageButton-Large.png#lightbox "основные щелчок ImageButton")

В следующем примере показано, как создать страницу, которая является функциональным эквивалентом предыдущего примера XAML, но полностью в C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>Отключение ImageButton

Иногда приложение находится в определенном состоянии, там, где определенный `ImageButton` щелкните не является допустимой операцией. В таком случае `ImageButton` следует отключить, задав его `IsEnabled` свойства `false`.

## <a name="using-the-command-interface"></a>С помощью интерфейса командной

Существует возможность приложения реагировать на `ImageButton` касания без обработки `Clicked` событий. `ImageButton` Реализует механизм альтернативную вызывается _команда_ или _команды_ интерфейс. Он состоит из двух свойств:

- `Command` типа [ `ICommand` ](xref:System.Windows.Input.ICommand), интерфейс, определенный в [ `System.Windows.Input` ](xref:System.Windows.Input) пространства имен.
- `CommandParameter` свойство типа [ `Object` ](xref:System.Object).

Этот подход используется в связи с привязкой к данным и особенно в том случае, если реализация архитектуры Model-View-ViewModel (MVVM).

Дополнительные сведения об использовании интерфейса командной см. в разделе [с помощью интерфейса командной](button.md#using-the-command-interface) в [кнопку](button.md) руководства.

## <a name="pressing-and-releasing-the-imagebutton"></a>Нажатие и отпускание ImageButton

Помимо `Clicked` событий, `ImageButton` также определяет `Pressed` и `Released` события. `Pressed` Событие возникает при нажатии палец `ImageButton`, или нажатии кнопки мыши с помощью указателя за пределы `ImageButton`. `Released` Событие возникает при отпускании кнопки мыши или палец. Как правило `Clicked` событие также инициируется в то же время, как `Released` события, но если указатель мыши или палец слайды от поверхности `ImageButton` до освобождения, `Clicked` событие может не произойти.

Дополнительные сведения об этих событиях см. в разделе [нажатие и отпускание кнопки](button.md#pressing-and-releasing-the-button) в [кнопку](button.md) руководства.

## <a name="imagebutton-appearance"></a>Внешний вид ImageButton

Помимо свойств, `ImageButton` наследует от [ `View` ](xref:Xamarin.Forms.View) класса `ImageButton` также определяет несколько свойств, которые влияют на его внешний вид:

- `Aspect` — как изображение будет масштабироваться в соответствии с отображаемой области.
- `BorderColor` цвет области, окружающей `ImageButton`.
- `BorderWidth` представляет ширину границы.
- `CornerRadius` — Это радиус скругления углов `ImageButton`.

`Aspect` Свойство может устанавливаться на один из членов [ `Aspect` ](xref:Xamarin.Forms.Aspect) перечисления:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -растягивает изображение для заполнения полностью и точно `ImageButton`. Это может привести в образе искажен.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -обрезает изображение, чтобы он заполнял `ImageButton` при сохранении пропорций.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -горизонтальные рамки изображение (при необходимости), чтобы все изображение встраивается в `ImageButton`, с помощью пустого пространства, добавляемого в верх или низ или сторон в зависимости от того, является ли изображение шириной или высотой. Это значение по умолчанию [ `Aspect` ](xref:Xamarin.Forms.Aspect) перечисления.

> [!NOTE]
> `ImageButton` Класс также имеет [ `Margin` ](xref:Xamarin.Forms.View.Margin) и `Padding` свойства, которые управляют поведением макета `ImageButton`. Дополнительные сведения см. в разделе [поля и заполнение](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Визуальные состояния ImageButton

`ImageButton` имеет `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) , можно использовать для запуска Визуальное изменение в `ImageButton` при нажатии пользователем, в условии, что он включен.

В следующем примере XAML показано, как определить визуальное состояние для `Pressed` состояния:

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Указывает, что при `ImageButton` нажата, его [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) свойство изменяется со значения по умолчанию 1 к 0,8. `Normal` `VisualState` Указывает, что при `ImageButton` находится в обычном состоянии, его `Scale` будет установлено значение 1. Таким образом, общий эффект том, что при `ImageButton` является нажата, она масштабируется повторно немного меньше, и когда `ImageButton` является выпуска, она масштабируется повторно размер по умолчанию.

Дополнительные сведения о визуальных состояниях см. в разделе [Xamarin.Forms Диспетчер визуальных состояний](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Связанные ссылки

- [Пример FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
