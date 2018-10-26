---
title: Часть 1. Начало работы с XAML
description: В приложении Xamarin.Forms XAML главным образом используется для определения визуальное содержимое страницы и работает совместно с файлом кода.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/10/2018
ms.openlocfilehash: f75e49c04ded99b3f7468709926277648f9f3729
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103170"
---
# <a name="part-1-getting-started-with-xaml"></a>Часть 1. Начало работы с XAML

_В приложении Xamarin.Forms XAML обычно используется для определения визуального содержимого страницы и работает совместно с C# файл с выделенным кодом._

Файл с выделенным кодом обеспечивает поддержку код для разметки. Вместе эти два файла участвуют в определение класса, включающего дочерние представления и инициализации свойств. В файле XAML классы и свойства указываются с помощью XML-элементы и атрибуты и связи между разметки и кода создаются.

## <a name="creating-the-solution"></a>Создание решения

Чтобы приступить к изменению первого файла XAML, используйте Visual Studio или Visual Studio для Mac для создания нового решения Xamarin.Forms. (Перейдите на вкладку ниже соответствующие вашей среде).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

В Windows, с помощью Visual Studio выберите **файл > Создать > проект** в меню. В **новый проект** диалоговом окне выберите **Visual C# > кросс-платформенные** слева, а затем **мобильное приложение (Xamarin.Forms)** из списка в центре. 

![](get-started-with-xaml-images/win/newprojectdialog.w157.png "Диалоговое окно нового проекта")

Выбрать расположение для решения, присвойте ему имя **XamlSamples** (или любые желаемые) и нажмите клавишу **ОК**.

На следующем экране выберите **пустое приложение** шаблона и **.NET Standard** стратегии совместного использования кода:

![](get-started-with-xaml-images/win/newcrossplatformapp.png "Диалоговое окно нового приложения")

Нажмите клавишу **ОК**. 

В решении создаются четыре проекта: **XamlSamples** библиотеки .NET Standard, **XamlSamples.Android**, **XamlSamples.iOS**и универсальной платформы Windows решение, **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

В Visual Studio для Mac выберите **файл > новое решение** в меню. В **новый проект** диалоговое окно, выберите **Многоплатформенность > приложение** слева, и **приложение с пустыми формами** (*не* **приложение форм** ) из списка шаблонов:

![](get-started-with-xaml-images/mac/newprojectdialog1.png "Диалоговое окно нового проекта 1")

Нажмите клавишу **Далее**.

В следующем диалоговом окне укажите имя проекта **XamlSamples** (или любые желаемые). Убедитесь, что **использования .NET Standard** переключателя:

![](get-started-with-xaml-images/mac/newprojectdialog2.png "Диалоговое окно нового проекта 2")

Нажмите клавишу **Далее**. 

В следующем диалоговом окне можно выбрать расположение для проекта:

![](get-started-with-xaml-images/mac/newprojectdialog3.png "Диалоговое окно нового проекта 3")

Нажмите клавишу **Создание**

Решения создаются три проекта: **XamlSamples** библиотеки .NET Standard, **XamlSamples.Android**, и **XamlSamples.iOS**. 

-----

После создания **XamlSamples** решение, может потребоваться протестировать среду разработки, выбрав различных проектов платформы как автозагружаемый проект решения, и построение и развертывание простого приложения, созданные шаблон проекта на реальных устройств или эмуляторов.

Если не нужно писать код для платформы общей **XamlSamples** проекта библиотеки .NET Standard является, где вам предстоит провести практически все времени на программирование. Эти статьи не будет оказывается за пределами этого проекта.

### <a name="anatomy-of-a-xaml-file"></a>Анатомия файл XAML

В рамках **XamlSamples** библиотеки .NET Standard являются пары файлов со следующими именами:

- **App.XAML**, файл XAML; и
- **App.XAML.cs**, C# *кода* файла, связанного с файлом XAML.

Вам потребуется щелкните стрелку рядом с полем **App.xaml** для просмотра файла с выделенным кодом. 

Оба **App.xaml** и **App.xaml.cs** участвовать в класс с именем `App` , наследуемый от класса `Application`. Большинство классов с файлами XAML contribute к классу, который является производным от `ContentPage`; эти файлы использовать XAML для определения визуального содержимого всей страницы. Это справедливо для других двух файлах **XamlSamples** проекта:

- **MainPage.xaml**, файл XAML; и
- **MainPage.xaml.cs**, C# файл с выделенным кодом.

**MainPage.xaml** файл выглядит следующим образом (несмотря на то, что форматирование может немного отличаться):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!" 
               VerticalOptions="Center" 
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

Два пространства имен XML ( `xmlns`) объявления относятся к URI, первое первый взгляд на веб-сайте Xamarin а второе — на корпорации Майкрософт. Не трудитесь проверка какие эти коды URI, выберите пункт. Нет ничего нет. Они представляют просто собой URI, принадлежащие Xamarin и Майкрософт, и они по сути функцию, как идентификаторы версий.

Первое объявление пространства имен XML означает, что теги, определенные в файле XAML без префикса ссылаются на классы в Xamarin.Forms, например `ContentPage`. Второе объявление пространства имен определяет префикс `x`. Используется для нескольких элементов и атрибутов, которые являются внутренними для XAML сам которого поддерживает и другие реализации XAML. Тем не менее эти элементы и атрибуты немного отличаются в зависимости от года, внедренных в URI. Xamarin.Forms поддерживает спецификации XAML 2009, но не целиком.

`local` Объявление пространства имен позволяет обращаться к другим классам из проекта библиотеки .NET Standard.

В конце этого первый тег `x` префикс используется для атрибута с именем `Class`. Так как использование этого `x` префикс практически универсальной для пространства имен XAML, атрибуты XAML, такие как `Class` , почти всегда рассматриваются как `x:Class`.

`x:Class` Атрибут задает полное имя класса .NET: `MainPage` в класс `XamlSamples` пространства имен. Это означает, что этот файл XAML определяется новый класс с именем `MainPage` в `XamlSamples` пространство имен, которое является производным от `ContentPage`— тег, в котором `x:Class` появляется атрибут.

`x:Class` Атрибут может присутствовать только в корневом элементе файла XAML для определения производного C# класса. Это только новый класс, определенный в файле XAML. Все, что отображается в файле XAML вместо этого просто создан из существующих классов и инициализирован.

**MainPage.xaml.cs** файл выглядит следующим образом (помимо неиспользуемые `using` директивы):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

`MainPage` Класс является производным от `ContentPage`, но Обратите внимание, что `partial` определение класса. Это предполагает, что должно быть другом определении разделяемого класса для `MainPage`, но где именно? И что это такое `InitializeComponent` метод? 

Когда Visual Studio создает проект, он проводит синтаксический анализ файла XAML, чтобы создать C# файл кода. Если заглянуть в **XamlSamples\XamlSamples\obj\Debug** каталога, вы найдете файл с именем **XamlSamples.MainPage.xaml.g.cs**. «g» означает "для созданных". Это других определение разделяемого класса `MainPage` , содержащий определение `InitializeComponent` метод, вызываемый из `MainPage` конструктор. Эти два частичных `MainPage` определения классов может быть скомпилирован вместе. В зависимости от того, скомпилирован ли XAML является или нет в файле XAML или двоичную форму файла XAML внедряются в исполняемый файл.

Во время выполнения кода в вызовах конкретной платформы проекта `LoadApplication` , передавая в него новый экземпляр `App` класса в библиотеке .NET Standard. `App` Конструктор класса создает `MainPage`. Конструктор этого класса вызывает `InitializeComponent`, который затем вызывает `LoadFromXaml` метод, который извлекает из библиотеки .NET Standard в XAML-файл (или его скомпилированный двоичный файл). `LoadFromXaml` Инициализирует все объекты, определенные в файле XAML, они будут соединены вместе в родительско дочерних отношений, присоединяет обработчики событий, определенных в коде на события, определенные в файле XAML и задает результирующий дерево объектов, как содержимое страницы.

Несмотря на то, что обычно не нужно тратить много времени с помощью созданных файлов кода, иногда исключения среды выполнения вызывается на код в созданных файлов, поэтому необходимо ознакомиться с ними.

При компиляции и запуска этой программы `Label` элемент отображается в центре страницы, как видно из XAML. Три платформы, слева направо, iOS, Android и универсальной платформы Windows:

[![](get-started-with-xaml-images/xamlsamples.png "По умолчанию отображения Xamarin.Forms")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "отображения по умолчанию Xamarin.Forms")

Для более интересных визуальных элементов, все, что нужно несколько интересных XAML.

## <a name="adding-new-xaml-pages"></a>Добавление новых страниц XAML

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Для добавления других XAML на базе `ContentPage` классов в проект, выберите **XamlSamples** библиотеки .NET Standard проекта и вызвать **проект > Добавить новый элемент** пункта меню. В левой части **Добавление нового элемента** диалоговом окне выберите **Visual C#**  и **Xamarin.Forms**. В списке выберите **страницы содержимого** (не **страницы содержимого (C#)**, который создает код только страницы, или **представление содержимого**, которого не является страницей). Присвойте странице имя, например, **HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.w157.png "Новый диалоговое окно добавления элемента")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Чтобы добавить другие XAML на базе `ContentPage` классов в проект, выберите **XamlSamples** библиотеки .NET Standard проекта и вызвать **файл > новый файл** пункта меню. В левой части **новый файл** диалоговом окне выберите **Forms** слева, и **Forms ContentPage Xaml** (не **Forms ContentPage**, который Создает код только страницу, или **представление содержимого**, который не является страницей). Присвойте странице имя, например, **HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "Диалоговое окно создания файла")

-----

В проект будут добавлены два файла **HelloXamlPage.xaml** и файл с выделенным кодом **HelloXamlPage.xaml.cs**. 

## <a name="setting-page-content"></a>Установка содержимого страницы

Изменить **HelloXamlPage.xaml** файл таким образом, только теги для `ContentPage` и `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>
        
    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content` Теги являются частью уникальный синтаксис XAML. Поначалу они может показаться недопустимый XML-код, но они допустимы. Период не специальный символ в XML.

`ContentPage.Content` Теги называются *элемент свойства* теги. `Content` является свойством `ContentPage`и обычно устанавливается в одно представление или макета с дочерних представлений. Обычно свойства становятся атрибутами в XAML, но было бы сложно настраивать `Content` атрибутов для сложного объекта. По этой причине свойство выражается в виде XML-элемент, состоящий из имени класса и имя свойства, разделенные точкой. Теперь `Content` может быть установлено между `ContentPage.Content` теги, следующим образом:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Также Обратите внимание, что `Title` установлен атрибут корневого тега.

В настоящее время, связи между классами, свойства и XML должно быть очевидно: класс Xamarin.Forms (такие как `ContentPage` или `Label`) отображается в файле XAML как элемент XML. Свойства этого класса, включая `Title` на `ContentPage` и семь свойств `Label`— обычно появляются в виде атрибутов XML.

Многие сочетания клавиш существуют для задания значений этих свойств. Некоторые свойства доступны базовые типы данных: например, `Title` и `Text` свойства имеют тип `String`, `Rotation` имеет тип `Double`, и `IsVisible` (который является `true` по умолчанию и здесь может быть задано только для иллюстрации) имеет тип `Boolean`.

`HorizontalTextAlignment` Свойство имеет тип `TextAlignment`, который является перечислением. Для свойства любого типа перечисления все, что нужно питания — это имя члена.

Для более сложных типов свойств, тем не менее, преобразователи типов используются для синтаксического анализа XAML. Это классы в Xamarin.Forms, который является производным от `TypeConverter`. Много открытых классов, но некоторые — нет. Для этого конкретного файла XAML некоторые из этих классов играют роль в фоновом:

-  `LayoutOptionsConverter` для `VerticalOptions` свойство
-  `FontSizeConverter` для `FontSize` свойство
-  `ColorTypeConverter` для `TextColor` свойство

Эти преобразователи определяют допустимый синтаксис значения свойств.

`ThicknessTypeConverter` Может обрабатывать одной, двух или четырех чисел, разделенных запятыми. Если одно число, он применяется ко всем четырем сторонам. С двумя числами во-первых, левого и правого заполнения, а второй — верхней и нижней частях. Четыре цифры находятся в порядке слева, сверху, справа и нижней.

`LayoutOptionsConverter` Может преобразовать имена открытые статические поля `LayoutOptions` структуры для значений типа `LayoutOptions`.

`FontSizeConverter` Может обрабатывать `NamedSize` члена или числовой размер.

`ColorTypeConverter` Принимает имена открытые статические поля `Color` структуры или шестнадцатеричные значения RGB, независимо от наличия альфа-канала, предшествует знак решетки (#). Ниже приведен синтаксис без альфа-канал.

 `TextColor="#rrggbb"`

Каждый из мало буквы является шестнадцатеричной цифрой. Вот, как включено альфа-канал:

 `TextColor="#aarrggbb">`

Для альфа-канала Имейте в виду, что FF — полностью непрозрачный и 00 является полностью прозрачным.

Две другие форматы позволяют пользователю указать только один шестнадцатеричная цифра для каждого канала:

 `TextColor="#rgb"` `TextColor="#argb"`

В этих случаях цифра повторяется для образования значения. Например #CF3 является CC-FF-33 цвета RGB.

## <a name="page-navigation"></a>Навигация по страницам

При запуске **XamlSamples** программы, `MainPage` отображается. Чтобы увидеть новые `HelloXamlPage` можно либо задать его в качестве нового запуска в **App.xaml.cs** файл, или перейдите на новую страницу из `MainPage`.

Чтобы реализовать навигации, сначала необходимо изменить код на **App.xaml.cs** конструктор, чтобы `NavigationPage` создается объект:

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

В **MainPage.xaml.cs** конструктор, можно создать простой `Button` и использовать обработчик событий для перехода к `HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

Установка `Content` свойства страницы заменяет параметр `Content` свойства в файле XAML. При компиляции и развертывания новой версии этой программы, на экране появляется кнопка. Нажав клавишу, он переходит к `HelloXamlPage`. Ниже приведен результирующий страницы на iPhone, Android и универсальной платформы Windows.

[![](get-started-with-xaml-images/helloxaml1.png "Поворот текста метки")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "поворачивать текст метки")

Можно перейти обратно к `MainPage` с помощью **< обратно** кнопки в iOS с помощью стрелку влево, в верхней части страницы или в нижней части телефона на устройстве Android или стрелку влево в верхней части страницы в Windows 10.

Вы можете экспериментировать с XAML для различных способов для подготовки к просмотру `Label`. Если вам нужно внедрить любые символы Юникода в текст, можно использовать стандартный синтаксис XML. Например чтобы поместить приветствие в смарт-кавычки, используйте следующую команду:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Вот, как оно выглядит:

[![](get-started-with-xaml-images/helloxaml2.png "Текст метки с символами Юникода, повернуто")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "поворачивать текст метки с символами Юникода")

## <a name="xaml-and-code-interactions"></a>XAML и кода взаимодействия

**HelloXamlPage** образец содержит только один `Label` на странице, но это крайне редко. Большинство `ContentPage` набор производных `Content` свойства макета некоторых сортировки, такие как `StackLayout`. `Children` Свойство `StackLayout` определяется типом `IList<View>` , но он является объектом типа `ElementCollection<View>`, и что коллекции могут заполняться с помощью нескольких представлений или других значений. В XAML эти отношения родитель потомок устанавливаются с обычной иерархией, в XML. Ниже приведен файл XAML для новой страницы с именем **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Этот файл XAML синтаксически завершена, и вот как выглядит:

[![](get-started-with-xaml-images/xamlpluscode1.png "Несколько элементов управления на странице")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "нескольких элементов управления на странице")

Тем не менее вы, скорее всего рассмотрим следующую программу, чтобы быть функционально объемов. Возможно `Slider` должен вызвать `Label` для отображения текущего значения и `Button` вероятно функционирует должным образом, что-то в рамках программы.

Как вы увидите в [часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), задание отображения `Slider` с использованием синтаксиса `Label` могут обрабатываться полностью в XAML с привязкой данных. Однако полезно сначала просмотрите код решение. Тем не менее, обработка `Button` щелкните определенно требует код. Это означает, что файл с выделенным кодом для `XamlPlusCodePage` должен содержать обработчики для `ValueChanged` событие `Slider` и `Clicked` событие `Button`. Давайте добавим их:

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Эти обработчики событий не обязательно должны быть открытыми.

В файле XAML `Slider` и `Button` теги должны включать атрибуты для `ValueChanged` и `Clicked` , которые ссылаются на эти обработчики событий:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Обратите внимание на то, что назначение обработчика события имеет тот же синтаксис, как присвоения значения свойству.

Если обработчик `ValueChanged` событие `Slider` используем `Label` для отображения текущего значения, обработчик должен ссылаться на этот объект из кода. `Label` Нуждается в имени, который указывается с помощью `x:Name` атрибута.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x` Префикс `x:Name` атрибут указывает, что этот атрибут является внутренним по отношению к XAML.

Имя, назначаемые `x:Name` атрибут имеет те же правила, что C# имена переменных. К примеру оно должно начинаться с буквы или символа подчеркивания и не содержать внутренних пробелов.

Теперь `ValueChanged` можно задать обработчик событий `Label` для отображения новых `Slider` значение. Новое значение доступна из аргументов события:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Или удалось получить обработчик `Slider` объект, который создает это событие из `sender` аргумент и получить `Value` свойство из того, что:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

При первом запуске программы, `Label` не отображает `Slider` поскольку значение `ValueChanged` событие еще не запущено. Однако любое изменение `Slider` приводит значения для отображения:

[![](get-started-with-xaml-images/xamlpluscode2.png "Отображаемое значение ползунка")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "отображаемое значение \"ползунок\"")

Теперь для `Button`. Посмотрим, в ответ на `Clicked` событий, отображая оповещение с `Text` кнопки. Обработчик событий можно безопасно привести `sender` аргумент `Button` и затем обратиться к ее свойствам:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

Этот метод определен как `async` поскольку `DisplayAlert` метод является асинхронным и должны предваряться символом `await` оператор, который возвращает при завершении метода. Так как этот метод получает `Button` срабатыванием события из `sender` аргумента, тот же обработчик может использоваться для нескольких кнопок.

Вы уже видели, что объект, определенный в XAML можно активировать событие, обрабатывается в файл с выделенным кодом, и что файл кода программной может быть объект, определенный в XAML, используя имя, назначенное к нему с помощью `x:Name` атрибута. Это два основных способа, которые взаимодействуют код и XAML.

Некоторые дополнительные функции анализа в как XAML works можно получить с помощью проверки вновь созданным **файл XamlPlusCode.xaml.g.cs**, которая теперь включает в себя любое имя, назначенных какой-либо `x:Name` атрибут в качестве скрытого поля. Здесь является упрощенной версией этого файла:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

Объявление это поле позволяет свободно использовать в любом месте переменную `XamlPlusCodePage` файл разделяемого класса в вашей юрисдикции. Во время выполнения присвоенное полю значение после XAML, проанализирован. Это означает, что `valueLabel` поле является `null` при `XamlPlusCodePage` конструктор начинается, но действительными после `InitializeComponent` вызывается.

После `InitializeComponent` возвращает элементов управления обратно в конструктор, как будто они был создан и инициализирован в коде, были построены визуальные элементы страницы. Какой-либо роли больше не воспроизведения файла XAML в классе. Можно управлять этими объектами, на странице способом, который требуется, например, путем добавления представления к `StackLayout`, или параметр `Content` свойства страницы на другое полностью. Вы можете «пройти по дереву» с помощью проверки `Content` свойства страницы и элементы в `Children` коллекции макетов. Можно задать свойства для представлений, доступ к таким образом или динамически назначаются обработчики событий к ним.

Вы можете. Это страницу и XAML это только средство для построения его содержимого.

## <a name="summary"></a>Сводка

В этом введении вы увидели вклад XAML-файл и файл кода для определения класса и взаимодействие файлов XAML и кода. Но XAML также имеет свой собственный уникальный синтаксических функций, разрешить его для использования в гибкой. Вы сможете ознакомиться в [часть 2. Синтаксис XAML Essential](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Часть 5. От привязки данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
