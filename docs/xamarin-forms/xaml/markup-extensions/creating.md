---
title: Создание расширений разметки XAML
description: В этой статье объясняется, как определить собственные пользовательские расширения разметки XAML Xamarin.Forms. Расширение разметки XAML — это класс, который реализует интерфейс IMarkupExtension IMarkupExtension.
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b928c55f447d68b8adfedaa031fd85750ee71267
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563710"
---
# <a name="creating-xaml-markup-extensions"></a>Создание расширений разметки XAML

На уровне программного расширения разметки XAML — это класс, реализующий [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) или [ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) интерфейс. Можно просматривать исходный код расширений стандартной разметки, описано ниже в [ **расширений MarkupExtension** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) репозитория Xamarin.Forms GitHub.

Это также можно определить собственные пользовательские расширения разметки XAML путем наследования от `IMarkupExtension` или `IMarkupExtension<T>`. Используйте универсальную форму, если расширение разметки получает значение определенного типа. Это происходит с несколькими из расширения разметки Xamarin.Forms:

- `TypeExtension` является производным от `IMarkupExtension<Type>`
- `ArrayExtension` является производным от `IMarkupExtension<Array>`
- `DynamicResourceExtension` является производным от `IMarkupExtension<DynamicResource>`
- `BindingExtension` является производным от `IMarkupExtension<BindingBase>`
- `ConstraintExpression` является производным от `IMarkupExtension<Constraint>`

Два `IMarkupExtension` интерфейсы определяют только один метод, с именем `ProvideValue`:

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Так как `IMarkupExtension<T>` является производным от `IMarkupExtension` и включает в себя `new` ключевое слово на `ProvideValue`, одновременно содержит `ProvideValue` методы.

Очень часто расширения разметки XAML определяют свойства, влияющие к возвращаемому значению. (Является исключением, очевидно, `NullExtension`, в котором `ProvideValue` просто возвращает `null`.) `ProvideValue` Метод имеет один аргумент типа `IServiceProvider` , будет рассказано далее в этой статье.

## <a name="a-markup-extension-for-specifying-color"></a>Расширение разметки для указания цвета

Следующее расширение разметки XAML позволяет создавать `Color` с использованием компонентов тона, насыщенности и яркости. Он определяет четыре свойства для четырех компонентов цвета, включая альфа-компонент, который устанавливается равным 1. Класс является производным от `IMarkupExtension<Color>` для указания `Color` возвращаемое значение:

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Так как `IMarkupExtension<T>` является производным от `IMarkupExtension`, класс должен содержать два `ProvideValue` метода, который возвращает `Color` и еще одно, которое возвращает `object`, но второй метод может просто вызвать первый метод.

**Демонстрация цвета HSL** странице отображаются различные виды `HslColorExtension` может находиться в файле XAML для указания цвета для `BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Обратите внимание, что при `HslColorExtension` является XML-тег, четыре свойства заданы как атрибуты, но при его появлении расположены между фигурными скобками четыре свойства разделяются запятыми без кавычек. Для значений по умолчанию `H`, `S`, и `L` 0 и значение по умолчанию `A` -1, поэтому эти свойства можно опустить, если их значения по умолчанию. Последний пример показывает пример, где яркость равно 0, что обычно приводит к черный, но альфа-канал — 0,5, поэтому половина прозрачен и отображается серым белом фоне страницы:

[![Демонстрация цвета HSL](creating-images/hslcolordemo-small.png "Демонстрация цвета HSL")](creating-images/hslcolordemo-large.png#lightbox "Демонстрация цвета HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Расширение разметки для доступа к точечных рисунков

Аргумент `ProvideValue` — это объект, реализующий [ `IServiceProvider` ](xref:System.IServiceProvider) интерфейс, который определен в .NET `System` пространства имен. Этот интерфейс содержит один член, метод с именем `GetService` с `Type` аргумент.

`ImageResourceExtension` Класс, показанный ниже показан один из возможных способов использования `IServiceProvider` и `GetService` для получения `IXmlLineInfoProvider` объект, способный предоставлять строку и символ, данные для которых была обнаружена конкретной ошибки. В этом случае возникает исключение при `Source` не было задано свойство:

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` полезно, когда файл XAML требуется доступ к файл изображения, хранящиеся в виде внедренного ресурса в проект библиотеки .NET Standard. Она использует `Source` свойство для вызова статического `ImageSource.FromResource` метод. Этот метод требует ресурсов полное доменное имя, которое состоит из имени сборки, имя папки и имя файла, разделенных точками. `ImageResourceExtension` Не требуется имя сборки, часть, так как он получает имя сборки, с помощью отражения и добавляет его в `Source` свойство. В любом случае `ImageSource.FromResource` должна вызываться из сборки, содержащей точечного рисунка, который означает, что это расширение ресурсов XAML не может быть частью внешней библиотеки, если не являются также в этой библиотеке. (См. в разделе [ **внедренные изображения** ](~/xamarin-forms/user-interface/images.md#embedded-images) содержатся дополнительные сведения о доступе к растровые изображения, хранящиеся в виде внедренных ресурсов.)

Несмотря на то что `ImageResourceExtension` требует `Source` свойство для установки, `Source` свойство указывается в атрибуте как свойство содержимого класса. Это означает, что `Source=` можно опустить часть выражения в фигурных скобках. В **Демонстрация ресурса образа** странице `Image` элементы получить два изображения, используя имя папки и имя файла, разделенных точками:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Вот ее запуск на всех трех платформах:

[![Изображение ресурсов Demo](creating-images/imageresourcedemo-small.png "изображения ресурсов Demo")](creating-images/imageresourcedemo-large.png#lightbox "изображения Демонстрация ресурсов")

## <a name="service-providers"></a>Поставщики услуг

С помощью `IServiceProvider` аргумент `ProvideValue`, расширения разметки XAML могут получить доступ к полезную информацию о файле XAML, в котором они используются. Но использование `IServiceProvider` аргумент успешно, необходимо знать, какие службы доступны в определенных контекстов. Лучший способ понять, эта функция является путем отслеживания влияния исходный код существующего расширения разметки XAML в [ **расширений MarkupExtension** папку](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) в Xamarin.Forms репозитория в GitHub. Имейте в виду, что некоторые типы служб являются внутренними для Xamarin.Forms.

В некоторых расширений разметки XAML могут пригодиться эта служба.

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget` Интерфейс определяет два свойства `TargetObject` и `TargetProperty`. Если эти сведения будут получены в `ImageResourceExtension` класс, `TargetObject` — `Image` и `TargetProperty` — `BindableProperty` для объекта `Source` свойство `Image`. Это свойство, на котором установлено расширение разметки XAML.

`GetService` Вызов с аргументом `typeof(IProvideValueTarget)` фактически возвращает объект типа `SimpleValueTargetProvider`, который определен в `Xamarin.Forms.Xaml.Internals` пространства имен. Если нужно привести возвращаемое значение `GetService` к этому типу, также доступны `ParentObjects` свойство, которое представляет собой массив, содержащий `Image` элемент, `Grid` родительский элемент и `ImageResourceDemoPage` родительский `Grid`.

## <a name="conclusion"></a>Заключение

Расширения разметки XAML играют важную роль в XAML, расширив возможность устанавливать атрибуты из различных источников. Кроме того Если не указать существующие расширения разметки XAML, то, что вам нужно, можно также написать собственные.


## <a name="related-links"></a>Связанные ссылки

- [Расширения разметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Глава расширения разметки XAML из Xamarin.Forms (книга)](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
