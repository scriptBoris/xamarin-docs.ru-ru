---
title: Исходные представления в XAML
description: Исходные представления из iOS, Android и универсальной платформы Windows можно непосредственно ссылаться из файлов XAML Xamarin.Forms. На исходные представления можно задать свойства и обработчики событий, и они могут взаимодействовать с представления Xamarin.Forms. В этой статье показано, как использовать исходные представления из файлов XAML Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: b7ea75c13d84cf9fe74d7a606f6127aaa6bbe3b2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996338"
---
# <a name="native-views-in-xaml"></a>Исходные представления в XAML

_Исходные представления из iOS, Android и универсальной платформы Windows можно непосредственно ссылаться из файлов XAML Xamarin.Forms. На исходные представления можно задать свойства и обработчики событий, и они могут взаимодействовать с представления Xamarin.Forms. В этой статье показано, как использовать исходные представления из файлов XAML Xamarin.Forms._

В этой статье рассматриваются следующие темы:

- [Использование собственного представления](#consuming) — процесс для использования собственного представления из XAML.
- [Использование собственного привязок](#native_bindings) — привязка к и из свойств собственного представления данных.
- [Передачи аргументов в исходные представления](#passing_arguments) — передачи аргументов в конструкторы собственного представления и вызов собственного представления методов фабрики.
- [Ссылки на исходные представления из кода](#native_view_code) — извлечение собственного представления экземпляров объявленных в файле XAML, из соответствующего файла кода.
- [Создание подкласса исходные представления](#subclassing) — Создание подкласса исходные представления для определения API для XAML.  

<a name="overview" />

## <a name="overview"></a>Обзор

Внедрение нативных представлений в файл XAML Xamarin.Forms:

1. Добавление `xmlns` объявление пространства имен в файле XAML для пространства имен, которое содержит собственного представления.
1. Создайте экземпляр собственного представления в файле XAML.

> [!NOTE]
> XAMLC должен быть отключен для всех страниц XAML, использующие собственные представления.

Чтобы сослаться на собственного представления из файла кода, необходимо использовать общий ресурс проекта (SAP) и поместите код конкретной платформы с помощью директив условной компиляции. Дополнительные сведения см. в разделе [ссылки на исходные представления из кода](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Использование собственного представления

В следующем примере кода демонстрируется использование собственного представления для каждой платформы в Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

А также указание `clr-namespace` и `assembly` для собственного представления пространства имен, `targetPlatform` также должен быть указан. Это должно быть присвоено одно из значений [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) перечисления и обычно устанавливается в `iOS`, `Android`, или `Windows`. Во время выполнения, средство синтаксического анализа XAML будет игнорировать любые префиксы пространства имен XML, которые имеют `targetPlatform` не совпадает с платформой, на котором выполняется приложение.

Каждое объявление пространства имен можно использовать для ссылки любого класса или структуры из указанного пространства имен. Например `ios` объявление пространства имен можно использовать для ссылки на любого класса или структуры в магазине iOS `UIKit` пространства имен. Можно задать свойства собственного представления с помощью XAML, но типы свойств и объектов должны совпадать. Например `UILabel.TextColor` свойству `UIColor.Red` с помощью `x:Static` расширения разметки и `ios` пространства имен.

Привязываемые свойства и присоединенного привязываемые свойства также можно задать на исходные представления с помощью `Class.BindableProperty="value"` синтаксис. Каждое представление собственного упаковывается в конкретной платформы `NativeViewWrapper` экземпляр, который является производным от [ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View) класса. Установка может быть привязано или присоединенное свойство, используемое для собственного представления передает значение свойства оболочки. Например, выровненные по центру по горизонтали макета можно указать, задав `View.HorizontalOptions="Center"` собственного представления.

> [!NOTE]
> Обратите внимание на то, что стили нельзя использовать с представлениями собственного, так как стили можно предназначены только для свойств, связанных с `BindableProperty` объектов.

Конструкторы Android мини-приложения обычно требуют Android `Context` как аргумент и это можно сделать доступными через статическое свойство класса в `MainActivity` класса. Таким образом, при создании мини-приложение Android в XAML, `Context` объект обычно должен быть передан в конструктор мини-приложения с помощью `x:Arguments` атрибут `x:Static` расширение разметки. Дополнительные сведения см. в разделе [передачи аргументов для собственного представления](#passing_arguments).

> [!NOTE]
> Обратите внимание на то, собственного представления с помощью этого именования `x:Name` в проекте библиотеки .NET Standard или общего ресурса проекта (SAP) не поддерживается. Это приведет к созданию переменной собственный тип, который приведет к ошибке компиляции. Тем не менее, исходные представления может быть заключен в `ContentView` экземпляров и получить в файле кода, при условии, что используется SAP. Дополнительные сведения см. в разделе [ссылается на собственный представления из кода](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Собственные привязки

Привязка данных используется для синхронизации пользовательского интерфейса со своим источником данных и упрощает как приложения Xamarin.Forms отображает и взаимодействует с данными. Условии, что объект источника реализует `INotifyPropertyChanged` интерфейс, изменения в *источника* автоматически передаются в *целевой* объект платформе привязки, а также изменения в *целевой* объекта при необходимости могут быть переданы в *источника* объекта.

Исходные представления свойств также можно использовать привязку данных. В следующем примере кода демонстрируется привязка данных, с помощью свойств класса исходные представления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

На этой странице содержатся [ `Entry` ](xref:Xamarin.Forms.Entry) которого [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) свойство привязывается к `NativeSwitchPageViewModel.IsSwitchOn` свойство. [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) Страницы присваивается новый экземпляр класса `NativeSwitchPageViewModel` класс в файле кода с ViewModel класса, реализующего `INotifyPropertyChanged` интерфейс.

Страница также содержит встроенного коммутатора для каждой платформы. Использует каждого встроенного коммутатора [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay) привязки, чтобы обновить значение `NativeSwitchPageViewModel.IsSwitchOn` свойства. Таким образом, если параметр отключен, `Entry` отключена, и когда переключатель включен `Entry` включен. На следующих снимках экрана показано эту функцию на каждой платформе:

![](xaml-images/native-switch-disabled.png "Отключено встроенного коммутатора")
![](xaml-images/native-switch-enabled.png "встроенного коммутатора включена")

Условии, что реализует встроенного свойства автоматически поддерживаются двусторонней привязки `INotifyPropertyChanged`, поддерживает наблюдение за ключ-значение (KVO) на устройстве iOS или является `DependencyProperty` на UWP. Тем не менее многие собственного представления не поддерживают уведомления об изменении свойства. Для этих представлений, можно указать [ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName) значение свойства как часть выражения привязки. Это свойство должно быть присвоено имя события в собственном представлении, сигнализирующее о при изменении целевого свойства. Затем, когда значение встроенного коммутатора изменяется, `Binding` класс получает уведомление, что пользователь изменил значение переключателя и `NativeSwitchPageViewModel.IsSwitchOn` обновляется значение свойства.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Передача аргументов исходные представления

Можно передать аргументы конструктора собственного представления, используя `x:Arguments` атрибут `x:Static` расширение разметки. Кроме того, собственного представления фабричные методы (`public static` методы, возвращающие объекты или значения совпадает с типом класса или структуры, который определяет методы) может быть вызвана путем указания метода имя, используя `x:FactoryMethod` атрибут и его аргументов с помощью `x:Arguments` атрибута.

В следующем примере кода показаны оба способа:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

[ `UIFont.FromName` ](https://developer.xamarin.com/api/member/UIKit.UIFont.FromName/) Фабричный метод используется для задания [ `UILabel.Font` ](https://developer.xamarin.com/api/property/UIKit.UILabel.Font/) свойство в новый [ `UIFont` ](https://developer.xamarin.com/api/type/UIKit.UIFont/) в iOS. `UIFont` Аргументами метода, которые являются дочерними элементами указываются имя и размер `x:Arguments` атрибута.

[ `Typeface.Create` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/p/System.String/Android.Graphics.TypefaceStyle/) Фабричный метод используется для задания [ `TextView.Typeface` ](https://developer.xamarin.com/api/property/Android.Widget.TextView.Typeface/) свойство в новый [ `Typeface` ](https://developer.xamarin.com/api/type/Android.Graphics.Typeface/) в Android. `Typeface` Имя семейства и стиля задаются с помощью аргументов метода, которые являются дочерними элементами `x:Arguments` атрибута.

[ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) Конструктор используется для задания [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) свойство в новый `FontFamily` на универсальной платформе Windows (UWP). `FontFamily` Имя указано в параметре аргумента метода, который является дочерним элементом `x:Arguments` атрибута.

> [!NOTE]
> Аргументы должны совпадать с типами, необходимый для метода, конструктора или фабрики.

На следующих снимках экрана показано результатом указания аргументов метода и конструктора фабрики для задания шрифта на различные собственного представления:

![](xaml-images/passing-arguments.png "Настройка шрифтов на исходные представления")

Дополнительные сведения о передаче аргументов в XAML, см. в разделе [передачи аргументов в XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Ссылки на исходные представления из кода

Несмотря на то, что невозможно присвоить имя собственного представления с `x:Name` атрибут, его можно получить экземпляр собственного представления, объявленных в файле XAML из соответствующего файла кода в проекте общего доступа, при условии, что собственного представления является дочерним элементом [ `ContentView` ](xref:Xamarin.Forms.ContentView) , указывающий `x:Name` значение атрибута. Затем внутри директив условной компиляции в файл с выделенным кодом необходимо сделать следующее.

1. Получить [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) свойство значение и выполнить его приведение к конкретной платформы `NativeViewWrapper` типа.
1. Получить `NativeViewWrapper.NativeElement` свойство и приведите его к типу собственного представления.

Машинный интерфейс API может быть вызвана собственного представления для выполнения требуемой операции. Этот подход также обеспечивает преимущество что несколько представлений собственного XAML для различных платформ может быть дочерних элементов того же [ `ContentView` ](xref:Xamarin.Forms.ContentView). В следующем примере кода демонстрируется этот метод:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

В приведенном выше примере, собственного представления для каждой платформы являются дочерними элементами [ `ContentView` ](xref:Xamarin.Forms.ContentView) элементов управления, с `x:Name` значение атрибута, используемого для получения `ContentView` в код программной части:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) Обращении к свойству для получения упакованного собственного представления с указанием платформы `NativeViewWrapper` экземпляра. `NativeViewWrapper.NativeElement` Свойство затем будет обращаться для получения собственного представлении в качестве его собственного типа. API собственного представления вызывается, чтобы выполнить требуемые операции.

IOS и Android native кнопки одного и того же `OnButtonTap` обработчик событий, поскольку каждая кнопка собственного потребляет `EventHandler` делегировать в ответ на событие сенсорного ввода. Тем не менее, универсальной платформы Windows (UWP) использует отдельный `RoutedEventHandler`, который в свою очередь использует `OnButtonTap` обработчик событий в этом примере. Таким образом, при нажатии кнопки собственного `OnButtonTap` выполнения обработчика событий, который масштабирует и поворачивает собственный элемент управления, содержащихся в [ `ContentView` ](xref:Xamarin.Forms.ContentView) с именем `contentViewTextParent`. Далее на снимках экрана показаны такого развития событий на каждой платформе.

![](xaml-images/contentview.png "ContentView, содержащий собственный элемент управления")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Создание подкласса исходные представления

Многие iOS и Android исходные представления не подходят для создания экземпляров в XAML, поскольку они используют методы, а не свойства, для настройки элемента управления. Решением этой проблемы является подкласс исходные представления в оболочках, которые определяют дополнительные API XAML с поддержкой, использует свойства для настройки элемента управления, и события независимо от платформы. Упакованное исходные представления можно поместить в общий ресурс проекта (SAP) и окружено директив условной компиляции или помещены в проекты под конкретные платформы и ссылка из XAML в проекте библиотеки .NET Standard.

В следующем примере кода показано, что страницы Xamarin.Forms, которое использует подкласса исходные представления:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

На этой странице содержатся [ `Label` ](xref:Xamarin.Forms.Label) , отображающий фруктов, выбранного пользователем из собственного элемента управления. `Label` Привязывается к `SubclassedNativeControlsPageViewModel.SelectedFruit` свойство. [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) Страницы присваивается новый экземпляр класса `SubclassedNativeControlsPageViewModel` класс в файле кода с ViewModel класса, реализующего `INotifyPropertyChanged` интерфейс.

Страница также содержит собственного средства выбора представления для каждой платформы. Каждого собственного представления отображается коллекция фруктов, привязывая его `ItemSource` свойства `SubclassedNativeControlsPageViewModel.Fruits` коллекции. Это позволяет пользователю выбрать фруктов, как показано на следующем снимке экрана:

![](xaml-images/sub-classed.png "Подкласса исходные представления")

В iOS и Android native выбора использовать методы для настройки элементов управления. Таким образом эти средства выбора должен быть подклассом предоставить доступ к свойствам, чтобы сделать их XAML с поддержкой. В универсальной платформы Windows (UWP), `ComboBox` уже XAML с поддержкой и поэтому не требует создания подклассов.

### <a name="ios"></a>iOS

Подклассы реализации iOS [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) представление и предоставляет свойства и события, можно было легко из XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

`MyUIPickerView` Предоставляет `ItemsSource` и `SelectedItem` свойства и `SelectedItemChanged` событий. Объект [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) требует базового [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) модель данных, к которому получает доступ `MyUIPickerView` свойства и события. `UIPickerViewModel` Модель данных обеспечивается `PickerModel` класса:

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

`PickerModel` Класс предоставляет базовое хранилище `MyUIPickerView` класса, с помощью `Items` свойство. Каждый раз, когда выбранный элемент в `MyUIPickerView` изменения, [ `Selected` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.Selected/) выполняется метод, который обновляет выбранный индекс и активируется `ItemChanged` событий. Это гарантирует, что `SelectedItem` свойство всегда возвращает последний элемент, выбраны пользователем. Кроме того `PickerModel` класс переопределяет методы, которые используются для установки `MyUIPickerView` экземпляра.

### <a name="android"></a>Android

Подклассы Android реализации [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) представление и предоставляет свойства и события, можно было легко из XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

`MySpinner` Предоставляет `ItemsSource` и `SelectedObject` свойства и `ItemSelected` событий. Элементов, отображаемых на `MySpinner` предоставленные класс [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.Adapter/) связанный с представлением, а элементы заполняются в `Adapter` при `ItemsSource` изначально устанавливается свойство. Каждый раз, когда выбранный элемент в `MySpinner` класса изменения, `OnBindableSpinnerItemSelected` обновлениями обработчик событий `SelectedObject` свойство.

## <a name="summary"></a>Сводка

В этой статье было показано, как использовать исходные представления из файлов XAML Xamarin.Forms. На исходные представления можно задать свойства и обработчики событий, и они могут взаимодействовать с представления Xamarin.Forms.


## <a name="related-links"></a>Связанные ссылки

- [NativeSwitch (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)
- [Forms2Native (пример)](https://developer.xamarin.com/samples/xamarin-forms/Forms2Native/)
- [NativeViewInsideContentView (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeViewInsideContentView/)
- [SubclassedNativeControls (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/SubclassedNativeControls/)
- [Исходные формы](~/xamarin-forms/platform/native-forms.md)
- [Передача аргументов в XAML](~/xamarin-forms/xaml/passing-arguments.md)
