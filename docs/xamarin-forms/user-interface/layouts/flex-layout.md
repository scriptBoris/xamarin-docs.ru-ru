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
ms.openlocfilehash: a6c1b0a4e0df1c25f595ca4eb53079c74b84972e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998587"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

_Используйте FlexLayout для размещения или упаковки коллекцию дочерних представлений._

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) возможности в Xamarin.Forms версии 3.0. Он основан на CSS [гибкий модуль макета рамок](http://www.w3.org/TR/css-flexbox-1/), известный как _гибкий макет_ или _flex-box_, поэтому вызов, поскольку в нем присутствует много гибкие варианты для упорядочивания дочерних элементов в макете.

`FlexLayout` аналогичен Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) в том, что его можно упорядочить его дочерние элементы по горизонтали и вертикали в виде стека. Тем не менее `FlexLayout` поддерживает упаковки его дочерних элементов, если их слишком много, чтобы поместиться в одну строку или столбец, а также существует множество вариантов для ориентации, выравнивание и адаптации к экранах различных размеров.

`FlexLayout` является производным от [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) и наследует [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) свойство типа `IList<View>`.

`FlexLayout` Определяет шесть общедоступного свойства связывания и пять вложенных привязываемые свойства, которые влияют на размер, ориентацию и выравнивание дочерних элементов. (Если вы не знакомы с присоединенного свойства связывания, см. в статье  **[присоединенных свойств](~/xamarin-forms/xaml/attached-properties.md)**.) Эти свойства подробно в следующих разделах описаны на **[привязываемые свойства подробно](#bindable-properties)** и  **[присоединенного привязываемые свойства подробно](#attached-properties)**. Однако в этой статье начинается с раздела, на некоторых **[распространенные сценарии использования](#common-scenarios)** из `FlexLayout` многие из этих свойств, описывающий неформально. В конце статьи, вы увидите, как объединить `FlexLayout` с [таблицы стилей CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Распространенные сценарии использования

**[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример программы содержит несколько страниц, объяснить некоторые наиболее распространенные способы применения `FlexLayout` и позволяет экспериментировать со своими свойствами.

### <a name="using-flexlayout-for-a-simple-stack"></a>С помощью FlexLayout для простого стека

**Простой стек** странице показано, как `FlexLayout` можно заменить на `StackLayout` , но с простой разметкой. Все, что в этом примере определяется в XAML-страницы. `FlexLayout` Содержит четыре дочерних элементов:

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

Вот эту страницу, под управлением iOS, Android и универсальной платформы Windows:

[![Простой стек страницы](flex-layout-images/SimpleStack.png "простой стек страницы")](flex-layout-images/SimpleStack-Large.png#lightbox)

Три свойства `FlexLayout` отображаются в **SimpleStackPage.xaml** файла:

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) Свойству присваивается значение [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) перечисления. Значение по умолчанию — `Row`. Свойству присвоено значение `Column` дочерние элементы из `FlexLayout` располагаются в одном столбце элементов.

    Когда элементы на `FlexLayout` упорядочены в столбце, `FlexLayout` считается имеет вертикальный _основной оси_ и горизонтального _ось_.

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) Свойство имеет тип [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) и указывает, как элементы выравниваются по перекрестной оси. `Center` Параметр заставляет каждый элемент по горизонтали по центру.

    Если вы использовали `StackLayout` вместо `FlexLayout` для выполнения этой задачи будет выровнять все элементы, назначив `HorizontalOptions` свойство каждого элемента в `Center`. `HorizontalOptions` Работает не для дочерних элементов `FlexLayout`, но при этом единичные `AlignItems` свойство выполняет той же цели. Если вам нужно, можно использовать `AlignSelf` присоединенного свойство, используемое для переопределения `AlignItems` свойства для отдельных элементов:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    После этого изменения, это `Label` расположен над левым краем `FlexLayout` при порядок чтения слева направо.

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) Свойство имеет тип [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)и указывает, как элементы располагаются на основной оси. `SpaceEvenly` Параметр размещает все оставшиеся вертикали поровну между всеми элементами и выше первого элемента и ниже последнего элемента.

    Если вы использовали `StackLayout`, необходимо назначить `VerticalOptions` свойство каждого элемента в `CenterAndExpand` для получения аналогичного результата. Но `CenterAndExpand` параметр выделит вдвое больше места между каждым элементом, чем перед первым элементом и после последнего элемента. Можно имитировать `CenterAndExpand` параметр `VerticalOptions` , задав `JustifyContent` свойство `FlexLayout` для `SpaceAround`.

Эти `FlexLayout` свойства рассматриваются более подробно в разделе **[привязываемые свойства подробно](#bindable-properties)** ниже.

### <a name="using-flexlayout-for-wrapping-items"></a>С помощью FlexLayout для упаковки элементов

**Упаковки Photo** странице **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** образце показано, как `FlexLayout` можно поместить свои дочерние элементы дополнительные строки или столбцы. Файл XAML создает экземпляр `FlexLayout` и назначает два свойства:

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

`Direction` Свойства данного объекта `FlexLayout` не установлено, поэтому он имеет значение по умолчанию `Row`, то есть дочерние элементы располагаются в строках, а основной оси по горизонтали.

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) Свойство относится к типу перечисления [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Если имеется слишком много элементов помещается на строку, значение этого свойства вызывает элементы, которые нужно переносить на следующую строку.

Обратите внимание, что `FlexLayout` является дочерним элементом `ScrollView`. При наличии слишком много строк, чтобы уместиться на странице, а затем `ScrollView` имеет значение по умолчанию `Orientation` свойство `Vertical` и позволяет вертикальная прокрутка.

`JustifyContent` Свойство выделяет оставшиеся места на основной оси (горизонтальной оси), чтобы каждый элемент заключается в такой же объем пустого пространства.

Файл с выделенным кодом обращается к Коллекция фотографий примера и добавляет их в `Children` коллекцию `FlexLayout`:

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

Ниже приведен программу на трех платформ, постепенно прокручивать сверху вниз.

[![Странице упаковки Photo](flex-layout-images/PhotoWrapping.png "странице упаковки фото")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Макет страницы с FlexLayout

В веб-дизайн вызывается имеется стандартный макет [ _grail священный_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) так как это формат макета, это весьма желательно, но часто трудно реализовать с помощью совершенство. Макет состоит из заголовка в верхней части страницы и нижний колонтитул внизу, расширение на всю ширину страницы. Резервирует центре страницы — это основное содержимое, но часто с по столбцам меню слева от данные содержимого и с дополнительными символами (иногда называется _выделить_ области) справа. [Разделе 5.4.1 спецификации CSS гибкий макет поле](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) описывает, как можно реализовать макет grail священный с окном flex.

**Макета Grail Священный** странице **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример простой реализации этого макета, с помощью одного `FlexLayout` вложенным в другой. Так как эта страница предназначена для телефоне в книжной ориентации, области слева и справа от области содержимого, только 50 пикселей в ширину:

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

Здесь он работает под управлением трех платформ:

[![Страница макета Grail Священный](flex-layout-images/HolyGrailLayout.png "Grail Священный макета страницы")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Области навигации и "отдельно от" подготавливаются к просмотру с `BoxView` слева и справа.

Первый `FlexLayout` в XAML файл имеет основной вертикальной оси и содержит три дочерних элементов, расположенных в столбце. Это заголовок, текст страницы и нижний колонтитул. Вложенный `FlexLayout` имеет основной горизонтальной оси с тремя дочерними элементами, упорядочены в строку.

В этой программе демонстрируются три вложенных привязываемые свойства:

- `Order` Задать присоединенное свойство, используемое на первом `BoxView`. Это свойство представляет собой целое число со значением по умолчанию 0. Это свойство можно использовать для изменения порядка макета. Обычно разработчики предпочитают содержимое страницы, отображаются в разметке до элементов навигации и выделить элементов. Установка `Order` свойство на первом `BoxView` значение меньше, чем другие одноуровневых приводит к отображаются как первый элемент в строке. Аналогичным образом, убедитесь, что элемент отображается последний, задав `Order` значение больше, чем одноуровневых элементов.

- `Basis` Присоединенное свойство, используемое имеет значение по двум `BoxView` элементы, чтобы предоставить им 50 пикселей в ширину. Это свойство имеет тип `FlexBasis`, структура, которая определяет статическое свойство типа `FlexBasis` с именем `Auto`, который используется по умолчанию. Можно использовать `Basis` чтобы указать размер в пикселях или процентах, показывающее, сколько места занимает элемент, на основной оси. Он называется _основы_ так как оно задает размер элемента, который является основой всех последующих макета.

- `Grow` Свойству на вложенный `Layout` и на `Label` дочерних, представляющий содержимое. Это свойство имеет тип `float` и имеет значение по умолчанию 0. Если задано положительное значение, все оставшееся пространство на основной оси выделяется для этого элемента и того же уровня с положительным значениям `Grow`. Пространство распределяется пропорционально значениям, напоминающей спецификации типа «звезда» в `Grid`.

    Первый `Grow` вложенное свойство задано на вложенный `FlexLayout`, указывая, что этот `FlexLayout` будет занимать все неиспользуемые вертикальное пространство внутри внешнего `FlexLayout`. Второй `Grow` вложенное свойство задано на `Label` представляющий содержимое, в том, что это содержимое не занимают все неиспользуемое пространство по горизонтали в пределах внутреннего `FlexLayout`.

    Имеется также аналогичное `Shrink` присоединенного привязываемые свойства, можно использовать, если размер дочерних элементов превышает размер `FlexLayout` , но не требуется перенос.

### <a name="catalog-items-with-flexlayout"></a>Элементы каталога с FlexLayout

**Элементов каталога** странице в **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример аналогичен [пример 1 в разделе 1.1 спецификации поле макета CSS-Flex](http://www.w3.org/TR/css-flexbox-1/#overview)за исключением того, что отображается ряд изображения, поддерживающим горизонтальную прокрутку и описания три кодеры:

[![Каталог элементов страницы](flex-layout-images/CatalogItems.png "каталога элементов страницы")](flex-layout-images/CatalogItems-Large.png#lightbox)

Каждый из трех кодеры является `FlexLayout` содержащихся в `Frame` , имеющем точные значения высоты и ширины символов, и который также является потомком большего `FlexLayout`. В этом файле XAML, большая часть свойств `FlexLayout` указанных дочерних элементов в стилях, все, кроме одного из которых имеет неявный стиль:

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

Неявный стиль для `Image` включает параметры из двух вложенных привязываемые свойства `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order` Параметр &ndash;1 приводит `Image` элемента для отображения в каждом из вложенного сначала `FlexLayout` представления независимо от ее положения в коллекции дочерних элементов. `AlignSelf` Свойство `Center` вызывает `Image` по центру `FlexLayout`. Это значение переопределяет значение `AlignItems` свойство, которое имеет значение по умолчанию из `Stretch`, то есть, `Label` и `Button` дочерние элементы растягивается на всю ширину `FlexLayout`.

В каждой из трех `FlexLayout` просматривает, пустое `Label` предшествует `Button`, но у него есть `Grow` параметр 1. Это означает, что все лишнее вертикальное пространство выделяется в это поле пустым `Label`, который фактически передает `Button` вниз.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Привязываемые свойства подробно

Теперь, когда вы узнали некоторые типичные способы применения `FlexLayout`, свойства `FlexLayout` можно изучать более подробно. 
`FlexLayout` Определяет шесть привязываемые свойства, установленные для `FlexLayout` сама, либо в коде или XAML, для управления orientatin и выравнивание. (Одно из этих свойств [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), в этой статье не рассматривается.)

Вы можете поэкспериментировать с пятью оставшихся привязываемые свойства с помощью **поэкспериментировать** странице **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** образца. Эта страница позволяет добавить или удалить дочерние объекты из `FlexLayout` и задать сочетания пять связываемые свойства. Все дочерние `FlexLayout` являются `Label` представления разных цветов и размеров, с помощью `Text` свойство присвоено значение, соответствующее положению в `Children` коллекции.

При запуске программы до пяти `Picker` представления отображают значения по умолчанию для этих пяти `FlexLayout` свойства. `FlexLayout` Внизу экрана содержит три дочерних элементов:

[![На странице эксперимента: По умолчанию](flex-layout-images/ExperimentDefault.png "по умолчанию на странице эксперимента:")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Каждый из `Label` представления имеет серый фон, показывающий пространства, выделенного для, `Label` в `FlexLayout`. Фон `FlexLayout` сам по себе является Бледно-голубой. За исключением немного полей в левого и правого занимает весь нижней части страницы.

<a name="direction" />

### <a name="the-direction-property"></a>Свойство Direction

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) Свойство имеет тип [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), перечисление с четырьмя элементами:

- `Column`
- `ColumnReverse` (или «столбец reverse» в XAML)
- `Row`, значение по умолчанию
- `RowReverse` (или «строки reverse» в XAML)

В XAML можно указать значение этого свойства, используя имена членов перечисления в нижнем регистре, верхнем регистре, или смешанный регистр, или же можно использовать две дополнительные строки, показывается в скобках, совпадающие с индикаторами CSS. (Строки «столбец reverse» и «строки reverse» определены в [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) класс используется средством синтаксического анализа XAML.)

Вот **эксперимента** (слева направо), страница `Row` направление, `Column` направлении, и `ColumnReverse` направление:

[![На странице эксперимента: Направление](flex-layout-images/ExperimentDirection.png "на странице эксперимента: направление")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Обратите внимание, что для `Reverse` параметров, запустить элементы по правому или нижнему.

<a name="wrap" />

### <a name="the-wrap-property"></a>Свойство переноса по словам

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) Свойство имеет тип [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), перечисление с тремя элементами:

- `NoWrap`, значение по умолчанию
- `Wrap`
- `Reverse` (или «wrap-reverse» в XAML)

Слева направо, Показать эти экраны `NoWrap`, `Wrap` и `Reverse` параметры для 12 дочерних элементов:

[![На странице эксперимента: Перенос](flex-layout-images/ExperimentWrap.png "перенос на странице эксперимента:")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Когда `Wrap` свойству `NoWrap` основной оси ограничен (как в этой программе) и основной оси не ширину или высоту в соответствии со всех дочерних элементов, `FlexLayout` пытается уменьшить размер элементов, как на снимке экрана iOS Демонстрирует. Вы можете управлять shrinkness элементов с [ `Shrink` ](#shrink) присоединенного связываемые свойства.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>Свойство JustifyContent

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) Свойство имеет тип [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), перечисление с шесть членов:

- `Start` (или «flex-start» в XAML), значение по умолчанию
- `Center`
- `End` (или «flex-end» в XAML)
- `SpaceBetween` (или «пространство между» в XAML)
- `SpaceAround` (или «пространство каток» в XAML)
- `SpaceEvenly`

Это свойство указывает, как элементы располагаются на основной оси, которое является горизонтальной осью в этом примере:

[![На странице эксперимента: Выравнивание по ширине содержимого](flex-layout-images/ExperimentJustifyContent.png "на странице эксперимента: выравнивание содержимого")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

На снимках экрана всех трех `Wrap` свойству `Wrap`. `Start` По умолчанию отображается в предыдущем снимке экрана Android. На iOS снимке экрана ниже показаны `Center` параметр: все элементы перемещаются в центр. Три других параметров начиная со слова `Space` выделить дополнительное пространство, не занятую элементы. `SpaceBetween` Выделяет пространство между элементами; `SpaceAround` помещает равно пространства вокруг каждого элемента во время `SpaceEvenly` помещает равно пространства между каждым элементом и перед первым элементом и после последнего элемента в строке.

<a name="align-items" />

### <a name="the-alignitems-property"></a>Свойство AlignItems

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) Свойство имеет тип [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), перечисление с четырьмя элементами:

- `Stretch`, значение по умолчанию
- `Center`
- `Start` (или «flex-start» в XAML)
- `End` (или «flex-end» в XAML)

Это один из двух свойств (другие, [ `AlignContent` ](#align-content)), указывающее, как дочерние элементы выравниваются по перекрестной оси. В каждой строке дочерние элементы растягиваются (как показано на предыдущем снимке экрана) или выровнены по start, по центру или по конец каждого элемента, как показано на следующих снимках экрана три:

[![На странице эксперимента: Выравнивания элементов](flex-layout-images/ExperimentAlignItems.png "на странице эксперимента: выравнивания элементов")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

На снимке экрана iOS верхние края все дочерние элементы выравниваются. На снимках экрана Android элементы являются вертикально по центру зависимости от самого высокого дочернего. На снимке экрана универсальной платформы Windows выравниваются нижние границы всех элементов.

Для любого отдельного элемента `AlignItems` параметр может быть переопределено с помощью [ `AlignSelf` ](#align-self) присоединенного связываемые свойства.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>Свойство AlignContent

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) Свойство имеет тип [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), перечисление с семь элементов:

- `Stretch`, значение по умолчанию
- `Center`
- `Start` (или «flex-start» в XAML)
- `End` (или «flex-end» в XAML)
- `SpaceBetween` (или «пространство между» в XAML)
- `SpaceAround` (или «пространство каток» в XAML)
- `SpaceEvenly`

Как и `AlignItems`, `AlignContent` свойство также выравнивает дочерние элементы в перекрестной оси, но также влияет на все строки или столбцы:

[![На странице эксперимента: Выравнивание содержимого](flex-layout-images/ExperimentAlignContent.png "на странице эксперимента: выравнивание содержимого")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

В iOS screnshot обе строки, в верхней части; на снимке экрана Android они находятся в центре; и на снимке экрана универсальной платформы Windows они внизу. Строки также могут быть заданы различными способами:

[![На странице эксперимента: Выравнивание содержимого 2](flex-layout-images/ExperimentAlignContent2.png "на странице эксперимента: выравнивание содержимого 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent` Не действует, если имеется только одну строку или столбец.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Вложенные свойства привязки, подробно

`FlexLayout` Определяет пять вложенных связываемые свойства. Эти свойства устанавливаются на дочерних элементов `FlexLayout` и действуют только для этого конкретного дочернего.

<a name="align-self" />

### <a name="the-alignself-property"></a>Свойство AlignSelf

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) Присоединенное свойство, используемое имеет тип [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), перечисление с пять элементов:

- `Auto`, значение по умолчанию
- `Stretch`
- `Center`
- `Start` (или «flex-start» в XAML)
- `End` (или «flex-end» в XAML)

Для любого отдельного дочернего элемента `FlexLayout`, это свойство при задании переопределений [ `AlignItems` ](#align-items) свойство установлено `FlexLayout` сам. Значение по умолчанию `Auto` означает использование `AlignItems` параметр.

Для `Label` элемент с именем `label` (или примере), можно задать `AlignSelf` свойства в коде следующим образом:

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

Обратите внимание, что отсутствует ссылка на `FlexLayout` предком `Label`. В XAML свойство следующим образом:

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Свойство Order

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) Свойство имеет тип `int`. Значение по умолчанию — 0.

`Order` Свойство позволяет изменить порядок, дочерние элементы `FlexLayout` упорядочены. Как правило, дочерние элементы `FlexLayout` упорядочены, они отображаются в порядке `Children` коллекции. Этот порядок можно переопределить, задав `Order` присоединенное свойство привязки ненулевое целочисленное значение на один или несколько дочерних элементов. `FlexLayout` Затем упорядочивает его дочерние элементы, на основе параметра из `Order` свойство для каждого дочернего, но дочерние элементы с тем же `Order` параметр располагаются в порядке их появления в `Children` коллекции.

### <a name="the-basis-property"></a>Свойство основы

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) Присоединенное свойство, используемое указывает объем пространства, выделенного для дочернего элемента `FlexLayout` на основной оси. Указанная команда размер по `Basis` свойство — это размер родительского элемента на основной оси `FlexLayout`. Таким образом `Basis` указывает ширину дочерний элемент, если дочерние элементы расположены в строках или высота, если дочерние элементы размещаются в столбцах.

`Basis` Свойство имеет тип [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), структуру. Можно указать размер, в любом аппаратно независимых единицах или в процентах от размера `FlexLayout`. Значение по умолчанию `Basis` свойством является свойство `FlexBasis.Auto`, это означает, что дочернего элемента запрошено используется ширины или высоты.

В коде, можно задать `Basis` свойство для `Label` с именем `label` к 40 аппаратно независимых единицах, следующим образом:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Второй аргумент `FlexBasis` конструктор называется `isRelative` и указывает, является ли размер относительный (`true`) или абсолютный (`false`). Аргумент имеет значение по умолчанию `false`, поэтому можно также использовать следующий код:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Неявное преобразование из `float` для `FlexBasis` определен, поэтому вы можете упростить еще больше:

```csharp
FlexLayout.SetBasis(label, 40);
```

Можно задать размер 25% от `FlexLayout` родительского следующим образом:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Это дробное значение должно быть в диапазоне от 0 до 1.

В XAML можно использовать ряд с размером в аппаратно независимые единицы:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Или можно указать значение в диапазоне от 0% до 100%.

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**Поэкспериментировать основы** странице **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример позволяет экспериментировать с `Basis` свойство. На странице отображается перенесенного столбца до пяти `Label` элементы с чередованием цвета фона и переднего плана. Два `Slider` элементы позволяют указать `Basis` значения для второй и четвертый `Label`:

[![Основы поэкспериментировать страницы](flex-layout-images/BasisExperiment.png "основы поэкспериментировать страницы")](flex-layout-images/BasisExperiment-Large.png#lightbox)

На снимке экрана iOS слева показаны два `Label` элементов, получает значения высоты в аппаратно независимых единицах. Android экран показывает, как их, получает высота небольшую часть общей высоты `FlexLayout`. Если `Basis` имеет значение 100%, то дочернее действие высоту `FlexLayout`и переносятся на следующий столбец и занимают всей высоте этого столбца, как показано на снимке экрана универсальной платформы Windows: он отображается так, как если пять дочерних элементов упорядочиваются в строку , но фактически идут в пять столбцов.

### <a name="the-grow-property"></a>Увеличение свойства

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) Присоединенное свойство, используемое имеет тип `int`. Значение по умолчанию — 0, а значение должно быть больше или равно 0.

`Grow` Свойство играет роль при `Wrap` свойству `NoWrap` и имеет ряд дочерних элементов, общая ширина меньше, чем ширина `FlexLayout`, или столбец из дочерних элементов имеет высоту более короткие чем `FlexLayout`. `Grow` Свойство указывает, как распределение оставшиеся пространство среди дочерних элементов.

В **расти эксперимента** странице пять `Label` элементы чередующихся цвета упорядочены в столбец, а также два `Slider` элементы позволяют настроить `Grow` свойство второй и четвертый `Label`. На снимке экрана iOS слева показано значение по умолчанию `Grow` свойства 0:

[![На странице эксперимента Grow](flex-layout-images/GrowExperiment.png "странице эксперимента Grow")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Если один дочерние задано положительное `Grow` значение, что дочерние занимает все оставшееся пространство, как показано на снимке экрана Android. Эту область также может быть распределена между двумя или более дочерних элементов. На снимке экрана UWP `Grow` свойство второго `Label` имеет значение 0,5, хотя `Grow` свойство четвертого `Label` — 1.5, которая предоставляет четвертый `Label` три раза больше пространства оставшиеся как второй `Label`.

Как дочернее представление использует пространство зависит от конкретного типа дочернего элемента. Для `Label`, текст может располагаться в общее пространство, используемое `Label` с помощью свойств `HorizontalTextAlignment` и `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>Свойство сжатия

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) Присоединенное свойство, используемое имеет тип `int`. Значение по умолчанию — 1, а значение должно быть больше или равно 0.

`Shrink` Свойство играет роль при `Wrap` свойству `NoWrap` и агрегатные ширину строки дочерних элементов больше, чем ширина `FlexLayout`, или агрегатные высота дочерних элементов одного столбца больше, чем Высота `FlexLayout`. Обычно `FlexLayout` будет отображать эти дочерние объекты, constricting их размеры. `Shrink` Свойство можно определить, какие дочерние элементы имеют приоритет в отображении в их полный размер.

**Сжать эксперимента** страница создает `FlexLayout` с одной строкой до пяти `Label` дочерние элементы, которые требуют больше места, чем `FlexLayout` ширины. На снимке экрана iOS слева показано все `Label` элементы со значениями по умолчанию 1:

[![Сжатия поэкспериментировать страницы](flex-layout-images/ShrinkExperiment.png "сжатия поэкспериментировать страницы")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

На снимке экрана Android `Shrink` значение для второго `Label` имеет значение 0, а, `Label` отображается в его ширина. Кроме того, четвертый `Label` присваивается `Shrink` значение больше единицы, а ее сжатия. Показано, как на снимке экрана UWP `Label` элементов, получает `Shrink` значение 0, чтобы они могли отображаться в их полного размера, если это возможно.

Можно установить оба `Grow` и `Shrink` значения на случай, где размеры агрегатные дочерних иногда может быть меньше или иногда больше, чем размер `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Дизайн CSS с FlexLayout

Можно использовать [Дизайн CSS](~/xamarin-forms/user-interface/styles/css/index.md) функция, представленная с Xamarin.Forms 3.0 в связи с `FlexLayout`. **Элементов каталога CSS** странице **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** пример дублирует макет **элементов каталога** странице, но с CSS таблицы стилей для множества стилей:

[![Страница элементов каталога CSS](flex-layout-images/CssCatalogItems.png "страница элементов каталога CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Исходный **CatalogItemsPage.xaml** файл имеет пять `Style` определений в его `Resources` раздел с 15 `Setter` объектов. В **CssCatalogItemsPage.xaml** файл, который разбивается на два `Style` определения с только что четырьмя `Setter` объектов. Эти стили дополнить стилей CSS для свойств, которые в настоящее время не поддерживает функцию стилей Xamarin.Forms CSS:

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

Таблица стилей CSS указывается в первой строке `Resources` раздел:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Обратите внимание, что два элемента в каждом из трех элементов включают `StyleClass` параметры:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Они ссылаются на селекторы в **CatalogItemsStyles.css** стилей:

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

Несколько `FlexLayout` Здесь указываются присоединенного связываемые свойства. В `label.empty` селектор, вы увидите `flex-grow` атрибут, который задает стиль пустой `Label` для предоставления пустых строк выше `Button`. `image` Селектор содержит `order` атрибут и `align-self` атрибутов, которые соответствуют `FlexLayout` присоединенного свойства для привязки.

Вы уже видели, что можно задать свойства непосредственно на `FlexLayout` и подключенных привязываемые свойства можно задать с дочерними объектами `FlexLayout`. Или можно задать эти свойства, косвенно с помощью традиционных стили, основанные на XAML или стили CSS. Важно узнать и понять эти свойства. Эти свойства, которые делают `FlexLayout` по-настоящему гибкие. 

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout с Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms 3.0 гибкий макет, по [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
