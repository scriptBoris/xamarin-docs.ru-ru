---
title: Ориентации устройства
description: В этой статье объясняется, как приложения Xamarin.Forms макета, прекрасно выглядят в книжной и альбомной ориентации.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: 762d4752eb3882398f212e634c66305f74d5a459
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052667"
---
# <a name="device-orientation"></a>Ориентации устройства

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)

Это важно учитывать, как приложение будет использоваться и как альбомной ориентации могут быть включены для повышения удобства работы пользователей. Отдельные макеты, которые могут быть спроектированы для размещения нескольких ориентаций экрана и лучше всего использовать доступное пространство. На уровне приложения можно отключать или включать поворота.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Управление ориентации

Если вы используете Xamarin.Forms, поддерживаемый метод управления ориентации устройства — использовать параметры для каждого отдельного проекта.

### <a name="ios"></a>iOS

В iOS, ориентации устройства настроена для приложений, использующих **Info.plist** файла. Этот файл будет содержать параметры ориентации для iPhone и iPod, а также параметры для iPad, если приложение включает его в качестве целевого объекта. Ниже приведены инструкции, относящиеся к интегрированной среды разработки. Используйте параметры интегрированной среды разработки в верхней части этого документа, чтобы выбрать, какие инструкции, вы бы хотели см. в разделе:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

В Visual Studio откройте проект iOS и откройте **Info.plist**. Файл откроется в панель конфигурации, начиная с вкладкой сведения о развертывании iPhone:

![сведения о развертывании в Visual Studio iPhone](device-orientation-images/orientation-vs-iphone.png)

Чтобы настроить ориентацию iPad, выберите **сведения о развертывании iPad** вкладки в верхней левой части панели, затем выберите из доступных ориентация:

![Поддерживаемые ориентации устройства в Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

В Visual Studio для Mac, откройте проект iOS и откройте **Info.plist**. В разделе **приложения** вкладке разделы будут доступны для Задание ориентации:

![сведения о развертывании в Visual Studio для Mac iPhone](device-orientation-images/orientation-xam-ui.png)

Если вы хотите изменить значения, с помощью интерфейса редактора ключ значение, выберите **источника**> вкладку в нижней части экрана:

![Поддерживаемые ориентации устройства в Visual Studio для Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Чтобы управлять ориентацией на Android, откройте **MainActivity.cs** и установите ориентацию, с помощью атрибута Декорирование `MainActivity` класса:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin.Android поддерживает несколько параметров для указания ориентации:

- **Альбомная** &ndash; заставляет приложение ориентации на альбомную, независимо от того, данные датчиков.
- **Книжная** &ndash; заставляет приложение ориентации на книжной ориентации, независимо от того, данные датчиков.
- **Пользователь** &ndash; приводит к быть представлены с помощью пользователя предпочтительная ориентация приложения.
- **За** &ndash; приводит к ориентации приложения должен совпадать с ориентацию [действия](https://developer.xamarin.com/api/type/Android.App.Activity/) за ней.
- **Датчик** &ndash; вызывает ориентация приложения будет определяться датчика, в том случае, даже если пользователь отключил автоматического поворота.
- **SensorLandscape** &ndash; предписывает приложению использовать альбомной ориентации, при использовании данных датчиков, чтобы изменить направление экрана направлена (благодаря чему экрана не будет считаться сверху вниз).
- **SensorPortrait** &ndash; предписывает приложению использовать книжной ориентации, при использовании данных датчиков, чтобы изменить направление экрана направлена (благодаря чему экрана не будет считаться сверху вниз).
- **ReverseLandscape** &ndash; предписывает приложению использовать альбомной ориентации, с которыми сталкиваются противоположном направлении от обычного, таким образом, чтобы отображаться «сверху вниз.»
- **ReversePortrait** &ndash; предписывает приложению использовать книжной ориентации, с которыми сталкиваются противоположном направлении от обычного, таким образом, чтобы отображаться «сверху вниз.»
- **FullSensor** &ndash; заставляет приложение полагаться на данные датчиков, чтобы выбрать правильную ориентацию (из возможных 4).
- **FullUser** &ndash; предписывает приложению использовать параметры ориентации пользователя. Если включен автоматический поворот, можно использовать все 4 ориентации.
- **UserLandscape** &ndash; _\[не поддерживается\]_ предписывает приложению использовать альбомной ориентации, если у пользователя нет автоматического поворота включен, в этом случае он будет использовать датчик для определения ориентации. Этот параметр приведет к разрыву компиляции.
- **UserPortrait** &ndash; _\[не поддерживается\]_ предписывает приложению использовать книжной ориентации, если у пользователя нет автоматического поворота включен, в этом случае будет использоваться датчик для определения ориентации. Этот параметр приведет к разрыву компиляции.
- **Блокировки** &ndash; _\[не поддерживается\]_ предписывает приложению использовать ориентацию экрана, то при запуске без ответа на изменения в устройстве физический Ориентация. Этот параметр приведет к разрыву компиляции.

Обратите внимание, что собственных API Android предоставляют массу контроль над управление ориентации, включая параметры, которые явно указана другая пользователя выражен предпочтения.

### <a name="universal-windows-platform"></a>Универсальная платформа Windows

В универсальной платформы Windows (UWP), поддерживаемые ориентации задаются в **Package.appxmanifest** файла. Открытия манифеста откроет панель конфигурации, где можно выбрать поддерживаемые ориентации.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Реагирование на изменения в ориентации

Xamarin.Forms не предлагает все собственные события для уведомления приложения ориентации изменений в общем коде. Тем не менее `SizeChanged` событие `Page` применяется, когда доля ширины или высоты `Page` изменения. Если ширина `Page` больше, чем высота, устройство находится в альбомной ориентации. Дополнительные сведения см. в разделе [отобразить изображение, в зависимости от ориентации экрана](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

> [!NOTE]
> Нет существующих, бесплатный пакет NuGet для получения уведомлений об изменениях ориентации в общем коде. См. в разделе [репозиторий GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) Дополнительные сведения.

Кроме того, можно переопределить [ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*) метод `Page`, вставка любой макет изменения логики существует. `OnSizeAllocated` Метод вызывается всякий раз, когда `Page` выделяется новый размер, что происходит при повороте устройства. Обратите внимание, что базовая реализация `OnSizeAllocated` выполняет функции важные макета, поэтому очень важно вызвать базовую реализацию в переопределении:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Невозможность выполнения этот шаг приведет к сбою страницы.

Обратите внимание, что `OnSizeAllocated` метод может вызываться многократно при повороте устройства. Изменение макета каждый раз слишком затратно ресурсов и может привести к мерцание. Рассмотрите возможность использования переменной экземпляра на странице для отслеживания, является ли в альбомной или книжной ориентации и перерисовывать только при наличии изменений:

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

После обнаружения изменений в ориентации устройства, может потребоваться добавить или удалить дополнительные представления с пользовательского интерфейса реагировать на изменения в доступное пространство. Например рассмотрим встроенные калькулятора на каждой платформе в книжной ориентации:

![](device-orientation-images/calculator-portrait.png "Приложение калькулятора в книжной ориентации")

и в альбомной ориентации.

![](device-orientation-images/calculator-landscape.png "Приложение калькулятора в альбомной ориентации")

Обратите внимание на то, что приложениям использовать преимущества доступное пространство, добавив дополнительные функциональные возможности в альбомной ориентации.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Гибкий макет

Существует возможность разработки интерфейсов, с помощью встроенных макетов, таким образом, чтобы они корректно переход при повороте устройства. Во время проектирования интерфейсов, которые будут продолжать привлекательными при изменении ориентации учитывайте следующие общие правила:

- **Обратите внимание на соотношение** &ndash; изменения ориентации может вызвать проблемы, при некоторых предположений относительно коэффициенты. Например представление, которое будет иметь достаточно места в 1/3 расстояние по вертикали в книжной ориентации экрана может не поместиться в 1/3 по вертикали в альбомной ориентации.
- **Будьте внимательны с абсолютными значениями** &ndash; абсолютные (области в пикселях) значения, которые имеют смысл в книжной ориентации не имеет смысла в альбомной ориентации. При необходимости абсолютные значения использования вложенным макетам во избежание их влияния. Например, было бы разумным использовать абсолютные значения в `TableView` `ItemTemplate` при шаблона элемента имеет гарантированной универсальный высоту.

Выше правила также применяются в том случае, если реализация интерфейсов нескольких размеров экрана и являются обычно считается лучшим. Остальная часть в этом руководстве объясняется, конкретные примеры быстрые макетов с помощью каждого из первичного макетов в Xamarin.Forms.

> [!NOTE]
> Для ясности в следующих разделах демонстрируются способы реализации быстро реагирующих макеты, используя лишь один из типов `Layout` за раз. На практике часто бывает проще смешивать `Layout`s для достижения нужного макета, с помощью простых и интуитивно `Layout` для каждого компонента.

### <a name="stacklayout"></a>StackLayout

Рассмотрим следующее приложение, отображается в книжной ориентации.

![](device-orientation-images/photo-stack-portrait.png "Приложение фото в книжной ориентации")

и в альбомной ориентации.

![](device-orientation-images/photo-stack-landscape.png "Приложение фотографий в альбомной ориентации")

Выполняется с помощью следующих XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

C# используется для изменения ориентации `outerStack` зависимости от ориентации устройства:

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Обратите внимание на следующее условия:

- `outerStack` настраивается для представления изображений и элементов управления в виде горизонтальной или вертикальной стека в зависимости от ориентации, чтобы лучше всего воспользоваться преимуществами доступное пространство.


### <a name="absolutelayout"></a>AbsoluteLayout

Рассмотрим следующее приложение, отображается в книжной ориентации.

![](device-orientation-images/photo-abs-portrait.png "Приложение фото в книжной ориентации")

и в альбомной ориентации.

![](device-orientation-images/photo-abs-landscape.png "Приложение фотографий в альбомной ориентации")

Выполняется с помощью следующих XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Обратите внимание на следующее условия:

- Из-за того, в который странице макетирования нет необходимости для процедурного кода представить скорость реагирования.
- `ScrollView` , Используется для разрешить метки отображаться, даже если высота экрана не меньше, чем сумма предопределенной высоту кнопки и изображения.


### <a name="relativelayout"></a>RelativeLayout

Рассмотрим следующее приложение, отображается в книжной ориентации.

![](device-orientation-images/photo-rel-portrait.png "Приложение фото в книжной ориентации")

и в альбомной ориентации.

![](device-orientation-images/photo-rel-landscape.png "Приложение фотографий в альбомной ориентации")

Выполняется с помощью следующих XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Обратите внимание на следующее условия:

- Из-за того, в который странице макетирования нет необходимости для процедурного кода представить скорость реагирования.
- `ScrollView` , Используется для разрешить метки отображаться, даже если высота экрана не меньше, чем сумма предопределенной высоту кнопки и изображения.

### <a name="grid"></a>Grid

Рассмотрим следующее приложение, отображается в книжной ориентации.

![](device-orientation-images/photo-grid-portrait.png "Приложение фото в книжной ориентации")

и в альбомной ориентации.

![](device-orientation-images/photo-grid-landscape.png "Приложение фотографий в альбомной ориентации")

Выполняется с помощью следующих XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

А также следующие процедурный код для обработки изменений поворота:

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Обратите внимание на следующее условия:

- Из-за того, в который макетирования страницы имеется метод для изменения положения элементов управления сетки.


## <a name="related-links"></a>Связанные ссылки

- [Макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Пример BusinessTumble (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [Гибкий макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [Вывод изображения в зависимости от ориентации экрана](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
