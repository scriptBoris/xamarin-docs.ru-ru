---
title: Xamarin.Forms FlexLayout
description: Используйте FlexLayout для размещения или упаковки коллекцию дочерних представлений.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 7585138cd6c33c2a5dc537ba28101a84e1c4b7ae
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921840"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

_Используйте FlexLayout для размещения или упаковки коллекцию дочерних представлений._

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) новые возможности в Xamarin.Forms версии 3.0. Он основан на CSS [гибкие поле Макет модуля](http://www.w3.org/TR/css-flexbox-1/), которая часто называется _гибкий макет_ или _flex поле_, так называемые, поскольку в нем содержится много гибкие средства для упорядочивания дочерних элементов в макете.

`FlexLayout` Аналогично Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) в том, что его можно упорядочить свои дочерние элементы по горизонтали и вертикали в виде стека. Однако `FlexLayout` способен также упаковки его дочерних элементов, если имеется слишком много, чтобы уместиться в одной строке или столбце, а также существует множество вариантов ориентацию, выравнивание и адаптации к экранах различных размеров.

`FlexLayout` является производным от [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) и наследует [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) свойство типа `IList<View>`.

`FlexLayout` Определяет шесть открытые свойства связывания и пять вложенного привязываемые свойства, которые влияют на размер, ориентация и выравнивание дочерних элементов. (Если вы не знакомы с вложенные свойства привязки, см. в статье  **[вложенные свойства](~/xamarin-forms/xaml/attached-properties.md)**.) Эти свойства описаны в следующих разделах подробно на **[привязываемые свойства подробно](#bindable-properties)** и  **[вложенные привязываемые свойства подробно](#attached-properties)**. Однако в этой статье начинается с помощью раздела на некоторых **[распространенные сценарии использования](#common-scenarios)** из `FlexLayout` многие из этих свойств, описывающий более быстро. В конце статьи, вы увидите, как объединить `FlexLayout` с [таблицы стилей CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Распространенные сценарии использования

**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** демонстрационная программа содержит несколько страниц, demonstate некоторые наиболее распространенные способы применения `FlexLayout` и позволяет экспериментировать с его свойствами.

### <a name="using-flexlayout-for-a-simple-stack"></a>С помощью FlexLayout для простого стека

**Простой стек** страниц отображает как `FlexLayout` можно заменить на `StackLayout` , но с более простой разметки. Все, что в этом примере определяется в XAML-страницы. `FlexLayout` Содержит четыре дочерних элементов:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

Вот этой страницы, под управлением iOS, Android и универсальной платформы Windows.

[![Простой стека страницы](flex-layout-images/SimpleStack.png "простой стека страницы")](flex-layout-images/SimpleStack-Large.png#lightbox)

Три свойства `FlexLayout` отображаются в **SimpleStackPage.xaml** файла:

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) Свойству присвоено значение [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) перечисления. Значение по умолчанию — `Row`. Свойства `Column` дочерние элементы из `FlexLayout` располагаются в одном столбце элементов.

    Когда элементы в `FlexLayout` упорядочены в столбце, `FlexLayout` говорят вертикальной _основной оси_ и горизонтальной _ось_.

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) Свойство относится к типу [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) и указывает способ выравнивания элементов в перекрестной оси. `Center` Вынуждает пунктом горизонтально по центру.

    При использовании `StackLayout` вместо `FlexLayout` для этой задачи будет выровнять все элементы путем назначения `HorizontalOptions` свойство каждого элемента в `Center`. `HorizontalOptions` Свойство не работает для дочерних элементов `FlexLayout`, но один `AlignItems` свойство достичь той же цели. Если необходимо, можно использовать `AlignSelf` присоединенного свойства привязки для переопределения `AlignItems` свойства для отдельных элементов:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    С данным изменением, это один `Label` расположено на левой границе `FlexLayout` при порядок чтения справа налево.

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) Свойство относится к типу [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)и указывает, как элементы располагаются на основной оси. `SpaceEvenly` Параметр размещает все оставшиеся интервал по вертикали между элементами и выше первого элемента и ниже последнего элемента.

    При использовании `StackLayout`, необходимо назначить `VerticalOptions` свойство для каждого элемента `CenterAndExpand` для достижения аналогичного эффекта. Но `CenterAndExpand` параметр будет выделить вдвое больше места между каждым элементом, чем до первого элемента и после последнего элемента. Можно имитировать `CenterAndExpand` параметр `VerticalOptions` , установив `JustifyContent` свойство `FlexLayout` для `SpaceAround`.

Эти `FlexLayout` свойства рассматриваются более подробно в разделе **[привязываемые свойства подробно](#bindable-properties)** ниже.

### <a name="using-flexlayout-for-wrapping-items"></a>С помощью FlexLayout для упаковки элементов

**Упаковки фото** страница **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** образце показано, как `FlexLayout` можно заключить свои дочерние элементы дополнительные строки или столбцы. Создает файл XAML `FlexLayout` и назначает два свойства:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

`Direction` Этого `FlexLayout` не задано, поэтому он содержит значение по умолчанию `Row`, то есть дочерние элементы располагаются в строках, а горизонтальная основной оси.

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) Свойство имеет тип перечисления [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Если имеется слишком много элементов помещается на строки, значение этого свойства вызывает элементы перенос на следующую строку.

Обратите внимание, что `FlexLayout` является дочерним элементом `ScrollView`. При наличии слишком много строк, чтобы уместиться на странице, то `ScrollView` имеет значение по умолчанию `Orientation` свойство `Vertical` и позволяет вертикальную прокрутку.

`JustifyContent` Свойство распределяет оставшееся место на основной оси (горизонтальная ось), чтобы каждый элемент будет окружена такого же объема пустое место.

Файл кода обращается к Коллекция фотографий образца и добавляет их в `Children` коллекцию `FlexLayout`:

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        int imageDimension = Device.RuntimePlatform == Device.iOS ||
                             Device.RuntimePlatform == Device.Android ? 240 : 120;

        string urlSuffix = String.Format("?width={0}&height={0}&mode=max", imageDimension);

        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("http://docs.xamarin.com/demo/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath + urlSuffix))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

Вот программу на трех платформах, постепенно прокручивать сверху вниз.

[![Страница упаковки фото](flex-layout-images/PhotoWrapping.png "странице упаковки фото")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Макет страницы с FlexLayout

В веб-дизайн вызывается имеется стандартный макет [ _grail Папский_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) , так как формат макета, весьма желательно, но часто трудно реализовать с меренгой. Макет состоит из заголовка в верхней части страницы и нижний колонтитул внизу расширение до полной ширины страницы. Занимающих центре страницы является основного содержимого, но часто с меню в один столбец слева от содержимого и Дополнительные сведения (иногда называется _выделить_ область) в правом. [Раздел 5.4.1 спецификации CSS гибкий макет поле](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) описывает, как можно реализовать grail Папский макета, в котором flex.

**Макета Grail Папский** страница **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** приведен пример простой реализации этот макет с помощью одного `FlexLayout` вложенным в другой. Поскольку данная страница предназначена для phone в книжной ориентации, области слева и справа от области содержимого являются только 50 пикселей в ширину:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

Здесь выполняется на трех платформах:

[![Страница макета Grail Папский](flex-layout-images/HolyGrailLayout.png "Grail Папский макета страницы")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Области навигации и отдельно от заполняются `BoxView` слева и справа.

Первый `FlexLayout` в XAML файл имеет основной вертикальной оси и содержит три дочерних элементов, расположенных в столбце. Это заголовок, тело страницы и нижний колонтитул. Вложенный `FlexLayout` имеет основной горизонтальной оси с тремя дочерними элементами, упорядочены в строку.

В этой программе показаны три вложенных привязываемые свойства:

- `Order` Задать присоединенное свойство, связываемое с первого `BoxView`. Это свойство имеет тип integer и значение по умолчанию 0. Это свойство можно использовать для изменения порядка макета. Обычно разработчики предпочитают содержимое страницы для отображения в разметке до элементов навигации и выделить элементов. Установка `Order` свойство на первом `BoxView` значение меньше, чем другие одноуровневых приводит к отображаются как первый элемент в строке. Аналогичным образом можно гарантировать, что элемент отображается последнее, задав `Order` значение больше, чем одноуровневых узлов.

- `Basis` Вложенное свойство привязываемых установлено по двум `BoxView` элементы, чтобы дать им шириной 50 пикселей. Это свойство имеет тип `FlexBasis`, структура, которая определяет статическое свойство типа `FlexBasis` с именем `Auto`, которое используется по умолчанию. Можно использовать `Basis` чтобы указать размер в пикселях или процент, указывающий, сколько места занимает элемент на основной оси. Он вызывается _основы_ , так как он указывает размер элемента, который является основой для всех последующих макета.

- `Grow` Свойству nested на `Layout` и на `Label` дочерних, представляющий содержимое. Это свойство имеет тип `float` и имеет значение по умолчанию 0. Если задано положительное значение, все оставшееся место на основной оси выделяется для этого элемента и одноуровневых элементов с положительными значениями из `Grow`. Память выделяется пропорционально значениям, напоминающей спецификации типа «звезда» в `Grid`.

    Первый `Grow` вложенному свойству задано на вложенный `FlexLayout`, означающее этим `FlexLayout` является занимают все неиспользуемые пространстве по вертикали в пределах внешнего `FlexLayout`. Второй `Grow` вложенное свойство установлено на `Label` представляющий содержимое, указывающее, что это содержимое занимают все неиспользуемые пространстве по горизонтали в рамках внутреннего `FlexLayout`.

    Имеется также аналогичное `Shrink` присоединенного привязываемые свойства, можно использовать, когда размер дочерних элементов превышает размер `FlexLayout` , но не требуется перенос.

### <a name="catalog-items-with-flexlayout"></a>Элементы с FlexLayout каталога

**Элементы каталога** страницы в **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример аналогичен [пример 1 в разделе 1.1 спецификации CSS гибкий макет поле](http://www.w3.org/TR/css-flexbox-1/#overview)за исключением того, что он отображает ряд изображений, поддерживающим горизонтальную прокрутку и описания три monkeys:

[![Каталог элементы страницы](flex-layout-images/CatalogItems.png "каталога элементы страницы")](flex-layout-images/CatalogItems-Large.png#lightbox)

Каждый из трех monkeys `FlexLayout` содержащихся в `Frame` , определяет точные значения высоты и ширины, и который также является потомком большего `FlexLayout`. В этом файле XAML, большая часть свойств `FlexLayout` дочерние элементы указываются в стилях только один из которых имеет неявный стиль:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Неявный стиль для `Image` содержит две вложенные привязываемые свойства параметры `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order` Параметра &ndash;значении 1 `Image` элемент сначала отображаемый в каждом из вложенного `FlexLayout` представления независимо от его положение в коллекции дочерних элементов. `AlignSelf` Свойство `Center` вызывает `Image` по центру `FlexLayout`. Это переопределяет настройки `AlignItems` свойства, которое имеет значение по умолчанию из `Stretch`, то есть, `Label` и `Button` дочерние элементы растягивается на всю ширину `FlexLayout`.

Внутри каждой из трех `FlexLayout` представления пустое `Label` предшествует `Button`, но у него есть `Grow` параметр 1. Это означает, что все лишнее вертикальное пространство выделяется в этом пустые `Label`, фактически помещает `Button` вниз.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Привязываемые свойства подробно

Теперь, когда вы познакомились с некоторыми распространенными приложениями `FlexLayout`, свойства `FlexLayout` можно анализировать более подробно. 
`FlexLayout` Определяет шесть привязываемые свойства, установленные для `FlexLayout` себя, либо в коде или в XAML для элемента управления orientatin и выравнивания. (Одно из этих свойств [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), не описанные в данной статье.)

Можно поэкспериментировать с пятью оставшихся привязываемые свойства с помощью **поэкспериментировать** страница **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** образца. Эта страница позволяет добавить или удалить дочерние объекты из `FlexLayout` и задать сочетание пять привязываемые свойства. Все дочерние элементы `FlexLayout` , `Label` представления различных цветов и размеров, с `Text` свойству присвоено значение, соответствующее его положение в `Children` коллекции.

При запуске программы до пяти `Picker` представления отображают значения по умолчанию для этих пяти `FlexLayout` свойства. `FlexLayout` В нижней части экрана содержит три дочерних элементов:

[![Страница эксперимента: По умолчанию](flex-layout-images/ExperimentDefault.png "страница эксперимента - по умолчанию")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Каждый из `Label` представления используется серый фон, показывающий объем, выделяемый тому, который `Label` в `FlexLayout`. Фон `FlexLayout` сам по себе является Анна синий. Он занимает весь нижней части страницы за исключением мало границы в слева и справа.

<a name="direction" />

### <a name="the-direction-property"></a>Свойство Direction

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) Свойство относится к типу [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), перечисление с четырьмя элементами:

- `Column`
- `ColumnReverse` (или «столбец возвратов» в языке XAML)
- `Row`, значение по умолчанию
- `RowReverse` (или «строка возвратов» в языке XAML)

В языке XAML можно указать значение этого свойства, используя имена членов перечисления в нижнем регистре, верхнем регистре, или смешанный регистр, или же можно использовать две дополнительные строки, показывается в скобках, которые являются одинаковыми как индикаторы CSS. (Строки «возвратов столбца» и «строки обратное» определяются в [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) класс, используемый средством синтаксического анализа XAML.)

Вот **эксперимента** (слева направо), страница `Row` направление, `Column` направление, и `ColumnReverse` направление:

[![Страница эксперимента: Направление](flex-layout-images/ExperimentDirection.png "страница эксперимента - направление")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Обратите внимание, что для `Reverse` варианты, элементы с загрузкой по правому или нижнему.

<a name="wrap" />

### <a name="the-wrap-property"></a>Свойство Wrap

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) Свойство относится к типу [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), перечисление с три члена:

- `NoWrap`, значение по умолчанию
- `Wrap`
- `Reverse` (или «wrap возвратов» в языке XAML)

Слева направо, Показать эти экраны `NoWrap`, `Wrap` и `Reverse` параметры 12 дочерних элементов:

[![Страница эксперимента: Перенос](flex-layout-images/ExperimentWrap.png "страница эксперимента - Wrap")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

При `Wrap` свойству `NoWrap` основной оси ограничен (как при использовании этой программы) и основной оси не ширину или высоту, по размеру все дочерние элементы, `FlexLayout` пытается уменьшить размер элементов, как на снимке экрана iOS Демонстрирует. Можно управлять shrinkness элементов с [ `Shrink` ](#shrink) присоединенного привязываемые свойства.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>Свойство JustifyContent

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) Свойство относится к типу [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), перечисление с шесть членов:

- `Start` (или «flex-start», в языке XAML), значение по умолчанию
- `Center`
- `End` (или «flex-end» в XAML)
- `SpaceBetween` (или «место между» в языке XAML)
- `SpaceAround` (или «место решения» в языке XAML)
- `SpaceEvenly`

Это свойство указывает, как элементы, расположенные на основной оси, который является горизонтальной оси в этом примере:

[![Страница эксперимента: Выравнивание содержимого](flex-layout-images/ExperimentJustifyContent.png "страница эксперимента - Justify содержимого")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Все три снимках экрана `Wrap` свойству `Wrap`. `Start` По умолчанию отображается на предыдущем снимке экрана Android. Операций ввода-вывода ниже снимке экрана `Center` параметр: все элементы перемещаются к центру. Три остальных начиная со слова `Space` выделить дополнительное пространство, не занимаемое элементами. `SpaceBetween` Выделяет пространство между элементами; `SpaceAround` помещает равно пространства вокруг каждого элемента во время `SpaceEvenly` помещает равно пространство между элементами и перед первым элементом и после последнего элемента в строке.

<a name="align-items" />

### <a name="the-alignitems-property"></a>Свойство AlignItems

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) Свойство относится к типу [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), перечисление с четырьмя элементами:

- `Stretch`, значение по умолчанию
- `Center`
- `Start` (или «flex-start», в языке XAML)
- `End` (или «flex-end» в XAML)

Это один из двух свойств (другие, [ `AlignContent` ](#align-content)), указывающее, как дочерние элементы выравниваются по перекрестной оси. В каждой строке дочерние элементы являются растягивается (как показано на предыдущем снимке экрана) или выровнены по Пуск, по центру или по концу каждого элемента, как показано в следующих трех снимки экрана:

[![Страница эксперимента: Выравнивания элементов](flex-layout-images/ExperimentAlignItems.png "страница эксперимента - выравнивания элементов")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

На снимке экрана iOS выравниваются верхние края все дочерние элементы. В Android снимки экрана элементы по вертикали центрируется на основе для самого высокого дочернего элемента. На снимке экрана UWP выравниваются нижних границ для всех элементов.

Для любого конкретного элемента `AlignItems` параметр может быть заменено [ `AlignSelf` ](#align-self) присоединенного привязываемые свойства.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>Свойство AlignContent

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) Свойство относится к типу [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), перечисление с семи элементов:

- `Stretch`, значение по умолчанию
- `Center`
- `Start` (или «flex-start», в языке XAML)
- `End` (или «flex-end» в XAML)
- `SpaceBetween` (или «место между» в языке XAML)
- `SpaceAround` (или «место решения» в языке XAML)
- `SpaceEvenly`

Как `AlignItems`, `AlignContent` свойство также выравнивает дочерние элементы в перекрестной оси, но также влияет на все строки или столбцы:

[![Страница эксперимента: Выравнивание содержимого](flex-layout-images/ExperimentAlignContent.png "странице эксперимента — выравнивание содержимого")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

В screnshot операций ввода-вывода обе строки находятся в верхней; в Android экрана они в центре; и в UWP экрана они внизу. Строки также могут быть заданы различными способами:

[![Страница эксперимента: Выравнивание содержимого 2](flex-layout-images/ExperimentAlignContent2.png "странице эксперимента — выравнивание содержимого 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent` Имеет смысл, если имеется только одну строку или столбец.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Вложенные свойства привязываемых подробно

`FlexLayout` Определяет пять вложенные свойства привязки. Эти свойства устанавливаются на дочерних элементов `FlexLayout` и действуют только для этого конкретного дочернего.

<a name="align-self" />

### <a name="the-alignself-property"></a>Свойство AlignSelf

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) Вложенное свойство привязки имеет тип [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), перечисление с пять элементов:

- `Auto`, значение по умолчанию
- `Stretch`
- `Center`
- `Start` (или «flex-start», в языке XAML)
- `End` (или «flex-end» в XAML)

Для всех отдельных дочерних из `FlexLayout`, это свойство переопределяет [ `AlignItems` ](#align-items) свойство, заданное для `FlexLayout` сам. Значение по умолчанию `Auto` означает использование `AlignItems` параметр.

Для `Label` элемента с именем `label` (или примере), можно задать `AlignSelf` свойства в коде следующим образом:

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

Обратите внимание, что отсутствует ссылка на `FlexLayout` родительский `Label`. В языке XAML задать для свойства следующим образом:

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Свойства Order

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) Свойство относится к типу `int`. Значение по умолчанию — 0.

`Order` Позволяет изменить порядок, дочерние элементы `FlexLayout` упорядочены. Как правило, дочерние элементы `FlexLayout` упорядочиваются находится в том же порядке, в котором они появляются в `Children` коллекции. Этот порядок можно переопределить, задав `Order` присоединенного привязываемые свойства ненулевое целочисленное значение на один или несколько дочерних элементов. `FlexLayout` Затем упорядочивает свои дочерние элементы, в зависимости от настройки `Order` свойство для каждого дочернего, но дочерних элементов с одинаковым `Order` параметр организованы в порядке, в котором они появляются в `Children` коллекции.

### <a name="the-basis-property"></a>Свойство основы

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) Вложенное свойство привязки указывает объем пространства, выделенного для дочернего элемента `FlexLayout` на основной оси. Указанная команда размер по `Basis` свойство — это размер вдоль оси основного родительского `FlexLayout`. Таким образом `Basis` указывает ширину дочерний элемент, если дочерние элементы расположены в строках или высоту при дочерние элементы упорядочиваются в столбцы.

`Basis` Свойство относится к типу [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), структуру. Можно указать размер в аппаратно независимых единицах или в процентах от размера `FlexLayout`. Значение по умолчанию `Basis` свойство является статическое свойство `FlexBasis.Auto`, что означает наличие дочернего элемента запроса используется ширины или высоты.

В коде, можно задать `Basis` свойство `Label` с именем `label` на 40 аппаратно независимых единицах следующим образом:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Второй аргумент `FlexBasis` конструктор называется `isRelative` и указывает, является ли размер относительный (`true`) или абсолютный (`false`). Аргумент имеет значение по умолчанию `false`, поэтому можно также использовать следующий код:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Неявное преобразование из `float` для `FlexBasis` определен, поэтому его можно еще более упростить:

```csharp
FlexLayout.SetBasis(label, 40);
```

Можно задать размер до 25% от `FlexLayout` родительского следующим образом:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Это дробное значение должно быть в диапазоне от 0 до 1.

В языке XAML можно использовать номер для в аппаратно независимые единицы:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Или можно указать значение в процентах в диапазоне от 0 до 100%.

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**Основы поэкспериментировать** страница **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример позволяет экспериментировать с `Basis` свойство. На странице отображается упакованного столбца пяти `Label` элементов с чередованием цвета фона и переднего плана. Два `Slider` элементы позволяют указать `Basis` значения для вторая и четвертая `Label`:

[![Основы поэкспериментировать страницы](flex-layout-images/BasisExperiment.png "основы поэкспериментировать страницы")](flex-layout-images/BasisExperiment-Large.png#lightbox)

На снимке экрана операций ввода-вывода, слева показано два `Label` элементы, которые получают высота в аппаратно независимых единицах. Android экране показаны данные об их, получает высота долю общей высоты `FlexLayout`. Если `Basis` имеет значение 100%, то дочернее действие высоту `FlexLayout`, переносятся на следующий столбец и занимают всю высоту столбца, как показано на снимке экрана UWP: он отображается, как если бы пять дочерних элементов организованы в строки , но фактически идут в пять столбцов.

### <a name="the-grow-property"></a>Увеличение свойство

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) Вложенное свойство привязки имеет тип `int`. Значение по умолчанию — 0, а значение должно быть больше или равно 0.

`Grow` Свойство играет роль при `Wrap` свойству `NoWrap` , строки дочерних элементов общая ширина меньше, чем ширина `FlexLayout`, или столбец дочерних элементов имеет короткий высоту, чем `FlexLayout`. `Grow` Свойство указывает, как распределять оставшееся пространство среди дочерних элементов.

В **расти эксперимента** страницы пять `Label` элементы чередующиеся цвета упорядочены в столбец, а два `Slider` элементы позволяют настроить `Grow` свойство вторая и четвертая `Label`. На снимке экрана iOS слева показано значение по умолчанию `Grow` свойства 0:

[![Страница эксперимента рост](flex-layout-images/GrowExperiment.png "страницы расширения эксперимента")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Если задано положительное любой один дочерний `Grow` значение, что дочерние занимает оставшееся пространство как показывает Android экрана. Это пространство, также могут быть распределены между двумя или более дочерних элементов. На снимке экрана UWP `Grow` свойство второго `Label` имеет значение 0,5, во время `Grow` свойство четвертого `Label` — 1,5, которая предоставляет четвертый `Label` втрое больше оставшееся место вторым `Label`.

Как дочернее представление использует это пространство зависит от конкретного типа дочернего элемента. Для `Label`, текст может располагаться в общее пространство `Label` с помощью свойств `HorizontalTextAlignment` и `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>Свойство сжатия

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) Вложенное свойство привязки имеет тип `int`. Значение по умолчанию — 1, а значение должно быть больше или равно 0.

`Shrink` Свойство играет роль при `Wrap` свойству `NoWrap` и статистические ширину строки дочерних элементов больше, чем ширина `FlexLayout`, или агрегатные высота дочерних элементов одного столбца больше, чем Высота `FlexLayout`. Обычно `FlexLayout` будет отображать эти дочерние элементы с constricting их размеров. `Shrink` Свойства можно указать, какие дочерние элементы, получают приоритет в отображении в их полный размер.

**Сжать эксперимента** страница создает `FlexLayout` содержит по одной строке из пяти `Label` дочерних элементов, которые требуют больше места, чем `FlexLayout` ширины. На снимке экрана iOS слева отображаются все `Label` элементы со значениями по умолчанию 1:

[![Параметр сжатия поэкспериментировать страницы](flex-layout-images/ShrinkExperiment.png "параметр сжатия поэкспериментировать страницы")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

На снимке экрана Android `Shrink` значение для второго `Label` имеет значение 0 и что `Label` отображается в его ширина. Кроме того, четвертый `Label` получает `Shrink` значения больше единицы, и он сжат. Снимок экрана UWP показано, как `Label` , получает элементы `Shrink` значение 0, чтобы они могли отображаться в полного размера, при этом возможна.

Можно задать и оба `Grow` и `Shrink` значения для размещения в ситуациях, где размеры дочерних статистические иногда может быть меньше или иногда больше, чем размер `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Дизайн CSS с FlexLayout

Можно использовать [Дизайн CSS](~/xamarin-forms/user-interface/styles/css/index.md) функция, добавленная с версии 3.0 в связи с Xamarin.Forms `FlexLayout`. **Элементы каталога CSS** страница **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** образец дублирует макет **элементы каталога** страницы, но с CSS Таблица стилей для множества стилей:

[![Страница элементы каталога CSS](flex-layout-images/CssCatalogItems.png "страница элементы каталога CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Исходный **CatalogItemsPage.xaml** файл имеет пять `Style` определений в его `Resources` раздел с 15 `Setter` объектов. В **CssCatalogItemsPage.xaml** файла, которое было уменьшено до двух `Style` определения имеет только четыре `Setter` объектов. Эти стили дополнить стиля CSS для свойств, которые в настоящее время не поддерживает функцию стилей Xamarin.Forms CSS:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Таблицы стилей CSS упоминается в первой строке `Resources` раздела:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Обратите внимание, что включены два элемента в каждом из трех элементов `StyleClass` параметры:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Они ссылаются на селекторы в **CatalogItemsStyles.css** таблицы стилей:

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

Несколько `FlexLayout` упоминаемые вложенные свойства привязки. В `label.empty` селектора, вы увидите `flex-grow` атрибут, который стили пустой `Label` для предоставления пустых строк выше `Button`. `image` Селектор содержит `order` атрибута и `align-self` атрибутов, которые соответствуют `FlexLayout` присоединенного свойства для привязки.

Вы уже видели, что можно задать свойства непосредственно в `FlexLayout` и вложенные привязываемые свойства можно задать с дочерними объектами `FlexLayout`. Или можно задать эти свойства, косвенно с помощью традиционных стили XAML-приложения или стили CSS. Важно знать и понимать эти свойства. Эти свойства, которые делают `FlexLayout` настоящему гибкие. 

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout с Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 гибкий макет, по [университета Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
