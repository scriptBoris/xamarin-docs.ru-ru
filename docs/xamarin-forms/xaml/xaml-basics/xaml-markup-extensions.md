---
title: Часть 3. Расширения разметки XAML
description: Расширения разметки XAML составляют важную функцию в XAML, которые позволяют свойства, чтобы присвоить объекты или значения, задаваемые косвенными ссылками из других источников.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 3/27/2018
ms.openlocfilehash: 6fcb051d2c24c7da169106b06ad5ebfc91edafa6
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935621"
---
# <a name="part-3-xaml-markup-extensions"></a>Часть 3. Расширения разметки XAML

_Расширения разметки XAML составляют важную функцию в XAML, которые позволяют свойства, чтобы присвоить объекты или значения, задаваемые косвенными ссылками из других источников. Расширения разметки XAML особенно важны для совместного использования объектов и ссылки на константы, используемые в рамках всего приложения, но они находят их наибольшую служебной программы в привязках данных._

## <a name="xaml-markup-extensions"></a>Расширения разметки XAML

Как правило XAML используется для задания свойств объекта в явные значения, такие как строка, число, члена перечисления или строку, которая преобразуется в значение в фоновом.

Иногда тем не менее, свойства вместо этого необходимо ссылаться на значения, определенные где-то иначе или которой может потребоваться лишь небольшой объем обработки кодом во время выполнения. Для этих целей XAML *расширения разметки* доступны.

Эти расширения разметки XAML не являются расширениями XML. XAML — вполне допустимо XML. Они называются «расширения» так, как они обеспечиваются кода в классах, реализующих `IMarkupExtension`. Можно написать собственные расширения пользовательской разметки.

Во многих случаях расширения разметки XAML содержит мгновенно понятные в файлах XAML, так как они отображаются в виде параметров атрибутов с разделителями в фигурные скобки: {и}, но иногда расширения разметки отображаются в разметке как обычные элементы.

## <a name="shared-resources"></a>Общие ресурсы

Некоторые страницы XAML содержит несколько представлений с помощью свойства, заданные значения. Например, многие из значения свойств для этих `Button` одинаковы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Если одно из этих свойств необходимо изменить, можно внести изменения только один раз, а не три раза. Если бы это был код, скорее всего используемого константы и статические объекты только для чтения для обеспечения актуальности такие значения, согласованное и легко изменить.

В XAML, одним из популярных решений для хранения подобных значений или объектов в *словарь ресурсов*. `VisualElement` Класс определяет свойство, именуемое `Resources` типа `ResourceDictionary`, словаря с ключами типа `string` и значения типа `object`. Вы можете поместить объекты в данный словарь и затем ссылаться на них из разметки, все это в XAML.

Чтобы использовать словарь ресурсов на странице, включите пару `Resources` теги элемента свойства. Лучше всего поместить эти данные в верхней части страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

Это также необходимо, чтобы явно включить `ResourceDictionary` теги:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Теперь объекты и значения различных типов могут добавляться в словарь ресурсов. Эти типы должны быть допускающий создание экземпляров. Они не могут быть абстрактные классы, например. Эти типы также должны иметь открытый конструктор без параметров. Каждый элемент требует ключ словаря, указанного с помощью `x:Key` атрибута. Пример:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Эти два элемента являются значениями типа структуры `LayoutOptions`и каждый имеет уникальный ключ и один или два свойства. В код и разметку, это гораздо чаще всего используется статические поля класса `LayoutOptions`, но здесь более удобно для задания свойств.

Теперь необходимо задать `HorizontalOptions` и `VerticalOptions` свойства этих кнопок, к этим ресурсам, и это будет сделано с `StaticResource` расширения разметки XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

`StaticResource` Расширение разметки всегда разделенное фигурных скобок и включает ключ словаря.

Имя `StaticResource` отличает его от `DynamicResource`, который также поддерживает Xamarin.Forms. `DynamicResource` для ключей словаря, связанных со значениями, которые могут измениться во время выполнения, хотя `StaticResource` обращается к элементам в словаре только в том случае, когда при создании элементов на странице.

Для `BorderWidth` свойства, она необходима для хранения типа double в словаре. XAML удобно определяет теги для основных типов данных, таких как `x:Double` и `x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Не нужно задать для него три строки. Для этого угол поворота этой записи словаря принимает только вверх на одну строку:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Эти два ресурса можно ссылаться в так же, как `LayoutOptions` значения:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Для типа ресурсов `Color`, можно использовать те же представления строки, используемыми для непосредственного назначения атрибуты из этих типов. Преобразователи типов вызываются при создании ресурса. Ниже приведен ресурс типа `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Часто программы набора `FontSize` свойства является членом `NamedSize` перечисления, такие как `Large`. `FontSizeConverter` Работает незаметно, преобразовать его в значение зависит от платформы с помощью класса `Device.GetNamedSized` метод. Тем не менее, при определении ресурса размер шрифта, более разумно использовать числовое значение, показанный здесь как `x:Double` типа:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Теперь все свойства кроме `Text` определяются параметры ресурсов:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Можно также использовать `OnPlatform` в словаре ресурсов, чтобы определить различные значения для платформ. Вот как `OnPlatform` объект может быть частью словарь ресурсов для разных текста цветов:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Обратите внимание, что `OnPlatform` получает оба `x:Key` атрибут, поскольку это объект словаря и `x:TypeArguments` атрибут, поскольку он является универсальным классом. `iOS`, `Android`, И `UWP` атрибутов преобразуются в `Color` значениями при инициализации объекта.

Ниже приведен окончательный полный файл XAML с тремя кнопками, доступ к шести общих значений.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:String x:Key="fontSize">Large</x:String>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

Снимки экрана проверить согласованный дизайн и стилей зависят от платформы:

[![](xaml-markup-extensions-images/sharedresources.png "Оформленных элементов управления")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "оформленных элементов управления")

Несмотря на то, что чаще всего используется для определения `Resources` коллекции в верхней части страницы, имейте в виду, `Resources` определяется свойство `VisualElement`, и может иметь `Resources` коллекций для других элементов на странице. Например, попробуйте добавить одно для `StackLayout` в этом примере:

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Вы обнаружите, что цвет текста кнопки теперь отображается синим цветом. По сути, каждый раз, когда средство синтаксического анализа XAML обнаруживает `StaticResource` расширения разметки, он выполняет поиск вверх по визуальному дереву и использует первый `ResourceDictionary` встречает содержит такой ключ.

Одним из наиболее распространенных типов объектов, хранящихся в словарях ресурсов является Xamarin.Forms `Style`, который определяет коллекцию свойств параметров. В этой статье рассматриваются стили [стили](~/xamarin-forms/user-interface/styles/index.md).

Иногда разработчики знакомы с XAML интересно, если они визуальный элемент можно поместить например `Label` или `Button` в `ResourceDictionary`. Хотя это конечно возможно, он ничего не дает. Цель `ResourceDictionary` является совместное использование объектов. Визуальный элемент нельзя сделать общим. Тот же экземпляр не может находиться в два раза на одной странице.

## <a name="the-xstatic-markup-extension"></a>Расширение разметки x: Static

Несмотря на сходство их имена `x:Static` и `StaticResource` сильно отличаются. `StaticResource` Возвращает объект из словаря ресурсов при `x:Static` обращается к одной из следующих:

- открытое статическое поле
- Общее статическое свойство
- открытое поле константы
- член перечисления.

`StaticResource` Расширение разметки поддерживается реализацией XAML, которые определяют словаря ресурсов, хотя `x:Static` является неотъемлемой частью XAML, как `x` префикса выявляет.

Ниже приведены несколько примеров, демонстрирующих как `x:Static` могут явно ссылаться на статические поля и члены перечисления:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

На данный момент это не слишком впечатляет. Но `x:Static` расширение разметки можно также ссылаться на статические поля или свойства из собственного кода. Например, вот `AppConstants` класс, который содержит некоторые статические поля, которые можно использовать на нескольких страницах приложения:

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Чтобы сослаться на статические поля этого класса в файле XAML, необходимо каким-то образом указать в файле XAML, где находится этот файл. Это делается с помощью объявления пространства имен XML.

Помните, что файлы XAML, создаваемых как часть стандартного шаблона XAML Xamarin.Forms содержат два объявления пространства имен XML: одна для доступа к Xamarin.Forms классов, а другой — для ссылки на тегах и атрибутах, встроенными в XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Вам потребуется Дополнительные объявления пространств имен XML для доступа к другим классам. Каждый дополнительный объявление пространства имен XML определяет новый префикс. Для доступа к классам, локальным для библиотеки .NET Standard общего приложения, такие как `AppConstants`, программисты XAML часто используют префикс `local`. Объявление пространства имен необходимо указать имя пространства имен CLR (Common Language Runtime), также известный как .NET имя пространства имен, представляющее собой имя, которое отображается в C# `namespace` определения или в `using` директивы:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Можно также определить объявления пространств имен XML для пространств имен .NET, в любой сборке, на который ссылается на библиотеки .NET Standard. Например, вот `sys` префикс для standard .NET `System` пространство имен, которое находится в **mscorlib** сборки, который один раз раскрывается как «Microsoft библиотекой среды выполнения объекта», но теперь означает «многоязыковой поддержки Standard Объект библиотекой среды выполнения.» Так как это другую сборку, необходимо также указать имя сборки, в этом случае **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Обратите внимание, что ключевое слово `clr-namespace` следует двоеточие, а затем имя пространства имен .NET, и точку с запятой, ключевое слово `assembly`, знак равенства, а также имя сборки.

Да, двоеточие стоит `clr-namespace` , но следует знак равенства `assembly`. Синтаксис был определен в этом так намеренно: объявления пространства имен XML наиболее ссылки URI, который начинается имени схемы URI, таких как `http`, которого всегда следует двоеточие. `clr-namespace` Часть этой строки предназначена для имитации что по соглашению.

Оба эти объявления пространств имен, включаются в **StaticConstantsPage** образца. Обратите внимание, что `BoxView` присваивается измерения `Math.PI` и `Math.E`, но масштабированные с коэффициентом 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

Размер результирующего `BoxView` относительно экрана зависит от платформы:

 [![](xaml-markup-extensions-images/staticconstants.png "Элементы управления, используя расширение разметки x: Static")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "элементы управления, используя расширение разметки x: Static")

## <a name="other-standard-markup-extensions"></a>Другие расширения стандартной разметки

Несколько расширений разметки являются внутренними для XAML и поддерживается в файлах XAML Xamarin.Forms. Некоторые из них не используются очень часто, но необходимы, когда они нужны:

-  Если свойство имеет отличный от `null` значение по умолчанию, но вы хотите присвоить `null`, задайте для него значение `{x:Null}` расширение разметки.
-  Если свойство имеет тип `Type`, можно назначить его `Type` с помощью расширения разметки `{x:Type someClass}`.
-  Можно определить массивы в XAML с помощью `x:Array` расширение разметки. Это расширение разметки имеет обязательный атрибут с именем `Type` , указывающее тип элементов в массиве.
- `Binding` Расширение разметки рассматривается в [часть 4. Основные сведения о привязке данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>Расширение разметки ConstraintExpression

Расширения разметки может иметь свойства, но они не были заданы как атрибуты XML. В расширении разметки параметры свойств разделяются запятыми и кавычки отображаются в фигурные скобки.

Это можно проиллюстрировать на примере с именем расширения разметки Xamarin.Forms `ConstraintExpression`, который используется с `RelativeLayout` класса. Как константу, относительно родителя или другие именованное представление, можно указать расположение или размер дочернее представление. Синтаксис `ConstraintExpression` позволяет задать положения или размера представления, используя `Factor` раз свойство из другого представления, а также `Constant`. Ничего сложнее, чем, требуется код.

Ниже приведен пример.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

Возможно, наиболее важный урок, рекомендуется делать из этого примера используется следующий синтаксис расширения разметки: кавычки необходимо заключать в фигурные скобки расширения разметки. При вводе расширения разметки в файле XAML, логично, требуется заключить в кавычки значения свойств. Не покидайте!

Вот ее запуск.

[![](xaml-markup-extensions-images/relativelayout.png "Относительный макет, используя ограничения")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "относительного макета с помощью ограничений")

## <a name="summary"></a>Сводка

Расширения разметки XAML, показанный здесь важно поддерживают файлы XAML. Но возможно наиболее ценных расширения разметки XAML является `Binding`, который описан в следующей части этой серии [часть 4. Основные сведения о привязке данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Часть 5. От привязки данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
