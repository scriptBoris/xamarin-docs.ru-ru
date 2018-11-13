---
title: Задание стиля приложений Xamarin.Forms с помощью каскадных таблиц стилей (CSS)
description: Xamarin.Forms поддерживает стили визуальных элементов с помощью каскадных таблиц стилей (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2018
ms.openlocfilehash: bcc18672257338e74e7a0c7114876129ae661acb
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527421"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Задание стиля приложений Xamarin.Forms с помощью каскадных таблиц стилей (CSS)

_Xamarin.Forms поддерживает стили визуальных элементов с помощью каскадных таблиц стилей (CSS)._

Приложения Xamarin.Forms можно применить различные стили с помощью CSS. Таблица стилей состоит из списка правил, с каждым правилом, состоящий из одного или нескольких селекторов и блок объявления. Блок объявления состоит из списка объявлений в фигурные скобки, в каждое объявление, состоящий из свойства, двоеточие и значение. Если существует несколько объявлений в блоке, точки с запятой вставляется в качестве разделителя. В следующем примере кода показан код CSS с Xamarin.Forms:

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

В Xamarin.Forms анализируются и вычисляется во время выполнения, а не время компиляции таблицы стилей CSS и повторного анализа, при использовании таблицы стилей.

> [!NOTE]
> В настоящее время все стили, возможна с помощью стилей XAML не может выполняться с CSS. Тем не менее стили XAML можно использовать в дополнение к CSS для свойств, которые в настоящее время не поддерживаются в Xamarin.Forms. Дополнительные сведения о стилях XAML см. в разделе [Задание стиля приложений Xamarin.Forms с помощью стилей XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

[MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/) пример демонстрирует использование CSS для стиля простое приложение и показан на снимках экрана ниже:

[![Страница MonkeyApp Main с Дизайн CSS](css-images/MonkeyAppMainPage.png "MonkeyApp главной страницы с Дизайн CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp главной страницы с Дизайн CSS")

[![Страница сведений MonkeyApp с Дизайн CSS](css-images/MonkeyAppDetailPage.png "страницу сведений о MonkeyApp с Дизайн CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "страницу сведений о MonkeyApp с Дизайн CSS")

## <a name="consuming-a-style-sheet"></a>Использование стилей

Процесс добавления стилей к решению выглядит следующим образом:

1. Добавьте пустой CSS-файл в проект библиотеки .NET Standard.
1. Задайте действие сборки файла CSS **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Загрузка таблицы стилей

Существует ряд подходов, которые могут использоваться для загрузки таблицы стилей.

### <a name="xaml"></a>XAML

Таблицу стилей можно загружать и синтаксический анализ при помощи [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) класса перед добавлением [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) Свойство задает стилей как URI относительно расположения внешнего файла XAML или относительно корневого каталога проекта, если URI начинается с `/`.

> [!WARNING]
> CSS-файл не удастся загрузить, если действие построения не присвоено **EmbeddedResource**.

Кроме того, можно загружать и анализировать с помощью стилей [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) класса перед добавлением [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), по встраивания его в `CDATA` разделе:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Дополнительные сведения о словарях ресурсов, см. в разделе [словари ресурсов](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C#

В C#, таблицу стилей можно загрузить как внедренный ресурс и добавить к [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

Первый аргумент `StyleSheet.FromAssemblyResource` метода сборки, содержащий таблицу стилей, а второй аргумент — `string` , представляющим собой идентификатор ресурса. Идентификатор ресурса можно получить из **свойства** окно при выборе файла CSS.

Кроме того, можно загрузить таблицу стилей из `StringReader` и добавляемый [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

Аргумент `StyleSheet.FromReader` метод `TextReader` который считан стилей.

## <a name="selecting-elements-and-applying-properties"></a>Выбор элементов и применение свойств

CSS использует селекторы для определения элементов, которые целевых. Стили с совпадающими селекторы применяются последовательно, в порядке определения. Стили, определенные на конкретном элементе всегда применяются последнего. Дополнительные сведения о поддерживаемых селекторы, см. в разделе [ссылку селектор](#selector-reference).

CSS использует свойства для применения стиля к выбранному элементу. Каждое свойство имеет набор возможных значений, и некоторые свойства могут повлиять на любого типа элемента, а другие относятся только к группам элементов. Дополнительные сведения о поддерживаемых свойствах см. в разделе [ссылку на свойство](#property-reference).

### <a name="selecting-elements-by-type"></a>Выбор элементов по типу

Можно выбрать элементы в визуальном дереве типом, без учета регистра с `element` селектор:

```css
stacklayout {
    margin: 20;
}
```

Этот выбор определяет любой [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) элементов на страницах, которые используют таблицу стилей и устанавливает их поля толщина 20.

> [!NOTE]
> `element` Селектор не определяет вложенные классы указанного типа.

### <a name="selecting-elements-by-base-class"></a>Выбор элементов в базовом классе

Можно выбрать элементы в визуальном дереве базовым классом, без учета регистра с `^base` селектор:

```css
^contentpage {
    background-color: lightgray;
}
```

Этот выбор определяет любой [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) элементы, которые используют таблицу стилей и задает фоновый цвет для `lightgray`.

> [!NOTE]
> `^base` Селектор относится к Xamarin.Forms и не является частью спецификации CSS.

### <a name="selecting-an-element-by-name"></a>Выбор элемента по имени

С учетом регистра можно выбрать отдельные элементы в визуальном дереве `#id` селектор:

```css
#listView {
    background-color: lightgray;
}
```

Этот выбор определяет элемент которого [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) свойству `listView`. Тем не менее если `StyleId` свойство не задано, средство выбора будет переключиться на использование `x:Name` элемента. Таким образом, в следующем примере XAML `#listView` селектор определит [ `ListView` ](xref:Xamarin.Forms.ListView) которого `x:Name` атрибуту присваивается `listView`и назначит цвет фона `lightgray`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Выбор элементов с помощью определенного класса атрибута

Можно выбрать элементы с атрибутом определенного класса с учетом регистра `.class` селектор:

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Класс CSS могут быть назначены элемент XAML, задав [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) свойство элемента, требуется имя класса CSS. Таким образом, в следующем примере XAML, стили определяются `.detailPageTitle` класс назначаются первому [ `Label` ](xref:Xamarin.Forms.Label), тогда как стили, определенные с `.detailPageSubtitle` класс назначаются второй `Label`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Выбрав дочерние элементы

Можно выбрать дочерних элементов в визуальном дереве с без учета регистра `element element` селектор:

```css
listview image {
    height: 60;
    width: 60;
}
```

Этот выбор определяет любой [ `Image` ](xref:Xamarin.Forms.Image) элементы, являющиеся дочерними для [ `ListView` ](xref:Xamarin.Forms.ListView) элементов и устанавливает их высоту и ширину до 60. Таким образом, в следующем примере XAML `listview image` селектор определит [ `Image` ](xref:Xamarin.Forms.Image) , является дочерним элементом [ `ListView` ](xref:Xamarin.Forms.ListView)и задает его высоту и ширину до 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> `element element` Селектор не требуется дочерний элемент, чтобы быть _прямой_ дочерний элемент родителя — дочерний элемент может иметь другого родителя. Выбор выполняется при условии, что первый элемент является предком.

### <a name="selecting-direct-child-elements"></a>Выбор дочерних элементов

Прямой дочерних элементов в визуальном дереве можно выбрать с без учета регистра `element>element` селектор:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Этот выбор определяет любой [ `Image` ](xref:Xamarin.Forms.Image) элементы, которые являются прямыми потомками [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) элементов и устанавливает их высоту и ширину 200. Таким образом, в следующем примере XAML `stacklayout>image` селектор определит [ `Image` ](xref:Xamarin.Forms.Image) это является прямым потомком [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)и присваивает его высоту и ширину 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> `element>element` Селектор требует, что дочерний элемент является _прямой_ дочерний элемент родителя.

## <a name="selector-reference"></a>Выбор ссылки

Xamarin.Forms поддерживает следующие селекторы CSS:

|Селектор|Пример|Описание|
|---|---|---|
|`.class`|`.header`|Выбирает все элементы с `StyleClass` свойство, содержащее «заголовок». Обратите внимание на то, что этот селектор чувствительно к регистру.|
|`#id`|`#email`|Выбирает все элементы с `StyleId` присвоено `email`. Если `StyleId` не указан, возврат к `x:Name`. При использовании XAML, `x:Name` предпочтительнее, чем `StyleId`. Обратите внимание на то, что этот селектор чувствительно к регистру.|
|`*`|`*`|Выбирает все элементы.|
|`element`|`label`|Выбирает все элементы типа `Label`, но не вложенные классы. Обратите внимание, что этот селектор без учета регистра.|
|`^base`|`^contentpage`|Выбирает все элементы с `ContentPage` в качестве базового класса, включая `ContentPage` сам. Обратите внимание, что этот селектор не учитывает регистр и не является частью спецификации CSS.|
|`element,element`|`label,button`|Выбирает все `Button` элементы и все `Label` элементы. Обратите внимание, что этот селектор без учета регистра.|
|`element element`|`stacklayout label`|Выбирает все `Label` элементов внутри `StackLayout`. Обратите внимание, что этот селектор без учета регистра.|
|`element>element`|`stacklayout>label`|Выбирает все `Label` элементов при помощи `StackLayout` как непосредственным родительским. Обратите внимание, что этот селектор без учета регистра.|
|`element+element`|`label+entry`|Выбирает все `Entry` элементы, непосредственно следующие за `Label`. Обратите внимание, что этот селектор без учета регистра.|
|`element~element`|`label~entry`|Выбирает все `Entry` элементов предшествует `Label`. Обратите внимание, что этот селектор без учета регистра.|

Стили с совпадающими селекторы применяются последовательно, в порядке определения. Стили, определенные на конкретном элементе всегда применяются последнего.

> [!TIP]
> Селекторы можно объединить без ограничений, таких как `StackLayout>ContentView>label.email`.

В настоящее время поддерживаются следующие селекторы:

- `[attribute]`
- `@media` и `@supports`
- `:` и `::`

> [!NOTE]
> Специфичность и переопределений специфичность не поддерживаются.

## <a name="property-reference"></a>Справочник по свойствам

Следующие свойства CSS, поддерживаемых Xamarin.Forms (в **значения** столбцов, типы являются _курсивом_, тогда как строковые литералы являются `gray`):

|Свойство.|Применение|Значения|Пример|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_Цвет_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Строка_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_Цвет_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`|_Double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`|_Double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_Цвет_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_Double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_число с плавающей запятой_ \| `auto` \| `initial`. Кроме того, можно указать значение в диапазоне 0 – 100% параметром `%` входа.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_число с плавающей запятой_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_число с плавающей запятой_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Строка_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_двойные_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`, `Span`|_Double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_Толщина_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Толщина_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Толщина_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Толщина_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Толщина_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_Double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Layout`, `Page`|_Толщина_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_Double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _Double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _Double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _Double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _Double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` и `right` следует избегать в средах справа налево.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _двойные_, _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_Double_ \| `initial`|`min-width: 320;`|

Также поддерживаются следующие свойства CSS Xamarin.Forms для конкретных (в **значения** столбцов, типы являются _курсивом_, тогда как строковые литералы являются `gray`):

|Свойство.|Применение|Значения|Пример|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_заключенный в кавычки текст_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_Цвет_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`, `Editor`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_Цвет_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_Цвет_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` поддерживается только на `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_Цвет_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_Цвет_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_Цвет_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_Double_ \| `initial` |`-xf-spacing: 8;`|

> [!NOTE]
> `initial` значение является допустимым для всех свойств. Он очищает значение (Сброс к значениям по умолчанию), установленного из другой стиль.

В настоящее время поддерживаются следующие свойства:

- `all: initial`.
- Свойства макета (поле или сетки).
- Собирательные свойства, такие как `font`, и `border`.

Кроме того, не `inherit` наследования значения и поэтому не поддерживается. Поэтому невозможно например, задать `font-size` свойства в макете и ожидать, что все [ `Label` ](xref:Xamarin.Forms.Label) экземпляров в макете, чтобы наследовать значение. Единственным исключением является `direction` свойство, которое имеет значение по умолчанию из `inherit`.

### <a name="color"></a>Цвет

Следующие `color` поддерживаются значения:

- `X11` [цвета](https://en.wikipedia.org/wiki/X11_color_names/), который соответствует цветов CSS, предварительно определенные цвета UWP и Xamarin.Forms цвета. Обратите внимание на то, что эти значения цвета нечувствительны к регистру.
- шестнадцатеричный цвета: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- цвета RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Значения находятся в диапазоне от 0 до 255, или 0 – 100%.
- RGBA цвета: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. Значение непрозрачности находится в диапазоне от 0,0-1.0.
- цвета HSL: `hsl(120, 100%, 50%)`. H значение находится в диапазоне от 0 до 360, а s и l — в диапазоне 0 – 100%.
- цвета hsla: `hsla(120, 100%, 50%, .8)`. Значение непрозрачности находится в диапазоне от 0,0-1.0.

### <a name="thickness"></a>Thickness

Один, два, три или четыре `thickness` поддерживаются значения, отделенных друг от друга пробелами:

- Одно значение указывает толщина.
- Два значения указывают толщину вертикальной, а затем по горизонтали.
- Три значения соответствуют верхней части страницы по горизонтали (слева и справа), а затем толщина нижней.
- Четыре значения указывают верхней, а затем правой, нижней, а затем толщину левой.

> [!NOTE]
> CSS `thickness` значения отличаются от XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/) значения. Например, в XAML двух значений — `Thickness` указывающее толщину горизонтальной, а затем по вертикали, то время как значение четырех `Thickness` указывает слева, сверху, а затем справа, снизу толщины. Кроме того, XAML `Thickness` значения с разделителями-запятыми.

### <a name="namedsize"></a>NamedSize

Без учета регистра следующие `namedsize` поддерживаются значения:

- `default`
- `micro`
- `small`
- `medium`
- `large`

Точный смысл каждого `namedsize` значение зависит от платформы и зависящие от представления.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS в Xamarin.Forms с помощью Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS, по [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [MonkeyAppCSS (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [Словари ресурсов](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Задание стиля приложений Xamarin.Forms с помощью стилей XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
