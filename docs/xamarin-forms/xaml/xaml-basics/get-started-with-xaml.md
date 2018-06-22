---
title: Часть 1. Начало работы с XAML
description: В приложении Xamarin.Forms XAML в основном используется для определения визуального содержимого страницы и работает вместе с файлом кода.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/10/2018
ms.openlocfilehash: 5883564841a4ef0e19518dd3b12ee00fe35ed778
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049731"
---
# <a name="part-1-getting-started-with-xaml"></a>Часть 1. Начало работы с XAML

_В приложении Xamarin.Forms XAML в основном используется для определения визуального содержимого страницы и работает совместно с файлом кода C#._

Файл кода обеспечивает поддержку кода для разметки. Вместе эти два файла участвовать в определении класса, включающего дочерние представления и инициализации свойств. В файле XAML классы и свойства, на которые имеются ссылки с XML-элементы и атрибуты и связи между разметки и кода создаются.

## <a name="creating-the-solution"></a>Создание решения

Чтобы приступить к изменению первый файл XAML, используйте Visual Studio или Visual Studio для Mac для создания нового решения Xamarin.Forms. (Перейдите на вкладку ниже, соответствующие вашей среде).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В Windows, с помощью Visual Studio выберите **файл > Создать > проект** в меню. В **новый проект** диалогового окна выберите **Visual C# > кроссплатформенный** слева, а затем **мобильное приложение (Xamarin.Forms)** из списка в центре. 

![](get-started-with-xaml-images/win/newprojectdialog.w157.png "Диалоговое окно нового проекта")

Выберите расположение для решения, присвойте ему имя **XamlSamples** (или любые желаемые) и нажмите клавишу **ОК**.

На следующем экране выберите **пустое приложение** шаблона и **.NET Standard** стратегии совместного использования кода:

![](get-started-with-xaml-images/win/newcrossplatformapp.png "Диалоговое окно нового приложения")

Press **OK**. 

Четыре проекты создаются в решении: **XamlSamples** .NET стандартной библиотеки **XamlSamples.Android**, **XamlSamples.iOS**и на универсальной платформе Windows решение, **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

В Visual Studio для Mac, выберите **файл > новое решение** в меню. В **новый проект** диалогового окна выберите **многоплатформенных > приложения** в левой части, и **пустое приложение форм** (*не* **приложения форм** ) из списка шаблонов:

![](get-started-with-xaml-images/mac/newprojectdialog1.png "Диалоговое окно нового проекта 1")

Нажмите клавишу **Далее**.

В следующем диалоговом окне укажите имя проекта **XamlSamples** (или любые желаемые). Убедитесь, что **использования .NET Standard** переключателя:

![](get-started-with-xaml-images/mac/newprojectdialog2.png "Диалоговое окно нового проекта 2")

Нажмите клавишу **Далее**. 

В следующем диалоговом окне можно выбрать расположение для проекта:

![](get-started-with-xaml-images/mac/newprojectdialog3.png "Диалоговое окно нового проекта 3")

Нажмите клавишу **создания**

Три проекты создаются в решении: **XamlSamples** .NET стандартной библиотеки **XamlSamples.Android**, и **XamlSamples.iOS**. 

-----

После создания **XamlSamples** решение, может потребоваться тестирования путем выбора различных проектов платформы как автозагружаемый проект решения среды разработки, а также построение и развертывание простого приложения, созданные шаблон проекта для эмуляторов телефона или физических устройств.

Если не требуется писать код конкретной платформы, общий **XamlSamples** является .NET Standard проект библиотеки, где вам предстоит провести практически все время программирования. Эти статьи не будет оказывается за пределами этого проекта.

### <a name="anatomy-of-a-xaml-file"></a>Составляющие XAML-файла

В пределах **XamlSamples** .NET стандартной библиотеки являются пары файлов со следующими именами:

- **App.XAML**, XAML-файл; и
- **App.XAML.cs**, C# *кода* файл, связанный с файлом XAML.

Вам потребуется щелкните стрелку рядом с **App.xaml** для просмотра файла кода. 

Оба **App.xaml** и **App.xaml.cs** участие в класс с именем `App` , производный от `Application`. Большинство классов с помощью XAML-файлов влияют на класс, производный от `ContentPage`; эти файлы использовать XAML для определения визуальное содержимое целой страницы. Это верно для двух файлов в **XamlSamples** проекта:

- **MainPage.xaml**, XAML-файл; и
- **MainPage.xaml.cs**, файл кода C#.

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

Два пространства имен XML ( `xmlns`) объявления относятся к URI, первый первый взгляд на веб-сайте Xamarin а второй — на корпорации Майкрософт. Беспокойтесь проверка какие эти идентификаторы URI, выберите пункт. Не существует. Они представляют просто собой URI, принадлежащих Xamarin и Microsoft, и они по сути функциональные возможности, что идентификаторы версий.

Первое объявление пространства имен XML означает, что теги, определенные в файле XAML без префикса ссылаются на классы в Xamarin.Forms, например `ContentPage`. Второе объявление пространства имен определяет префикс `x`. Этот параметр используется для нескольких элементов и атрибутов, которые являются внутренними для XAML сам которого поддерживает и другие реализации XAML. Тем не менее эти элементы и атрибуты, немного различаются в зависимости от года, внедренных в URI. Xamarin.Forms поддерживает спецификации XAML 2009, но не все его.

`local` Объявление пространства имен позволяет обращаться к другим классам из проекта библиотеки .NET Standard.

В конце первой тега `x` префикс используется для атрибута с именем `Class`. Так как использование этого `x` префикс — практически универсальной для пространства имен XAML, атрибуты XAML, таких как `Class` , почти всегда рассматриваются как `x:Class`.

`x:Class` Атрибут задает полное имя класса .NET: `MainPage` класса в `XamlSamples` пространства имен. Это означает, что этот XAML-файл определяет новый класс с именем `MainPage` в `XamlSamples` пространство имен, которое является производным от `ContentPage`— тег, в котором `x:Class` появится атрибут.

`x:Class` Может использоваться только в корневом элементе XAML-файла для определения производного класса C#. Это только новый класс, определенный в файле XAML. Все, что отображается в файле XAML вместо просто создается из существующих классов и инициализирована.

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

`MainPage` Класс является производным от `ContentPage`, но Обратите внимание `partial` определения класса. Это предполагает, что должно быть другое определение разделяемого класса для `MainPage`, но где именно? И что это такое `InitializeComponent` метод? 

Если Visual Studio создает проект, он анализирует файл XAML для создания файла кода C#. Если заглянуть в **XamlSamples\XamlSamples\obj\Debug** каталога, вы найдете в файл с именем **XamlSamples.MainPage.xaml.g.cs**. Созданный расшифровывается «g». Другие определение разделяемого класса для `MainPage` , содержащий определение `InitializeComponent` метод, вызываемый из `MainPage` конструктор. Эти два частичных `MainPage` определения класса может быть скомпилирован друг с другом. В зависимости от того, скомпилирован ли XAML является или нет файл XAML или двоичной форме в файле XAML внедряются в исполняемый файл.

Во время выполнения кода в конкретной платформы проекта вызывает `LoadApplication` метод, передавая ей новый экземпляр `App` класса в библиотеке .NET Standard. `App` Создает конструктор класса `MainPage`. Конструктор этого класса вызывает `InitializeComponent`, который затем вызывает `LoadFromXaml` метод, который извлекает из библиотеки .NET Standard XAML-файл (или его скомпилированном двоичном файле). `LoadFromXaml` Инициализирует все объекты, определенные в XAML-файле, соединяет их все вместе в родительско дочерних отношений, присоединяет обработчики событий, определенный в коде для события, определенные в файле XAML и задает результирующего дерева объектов, как содержимое страницы.

Несмотря на то, что обычно не нужно тратить много времени с помощью созданных файлов кода, иногда исключения среды CLR вызывается на код в созданных файлов, поэтому следует ознакомиться с ними.

После компиляции и запуска этой программы, `Label` элемент отображается в центре страницы, как видно в XAML. Три платформы слева направо, iOS, Android и UWP:

[![](get-started-with-xaml-images/xamlsamples.png "По умолчанию отображения Xamarin.Forms")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "отображения по умолчанию Xamarin.Forms")

Для более интересные визуальные элементы и все необходимые дополнительные интересных XAML.

## <a name="adding-new-xaml-pages"></a>Добавление новых страниц XAML

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Чтобы добавить другие XAML-приложения `ContentPage` классов в проект, выберите **XamlSamples** .NET стандартной библиотеки проекта и вызова неуправляемого кода **проект > Добавить новый элемент** пункта меню. В левой части **Добавление нового элемента** диалогового окна выберите **Visual C#** и **Xamarin.Forms**. Выберите из списка **страницы содержимого** (не **содержимого страницы (C#)**, который создает код только страницы, или **представление содержимого**, которой не является страницей). Присвойте странице имя, например, **HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.w157.png "Новое диалоговое окно добавления элемента")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Чтобы добавить другие XAML-приложения `ContentPage` классов в проект, выберите **XamlSamples** .NET стандартной библиотеки проекта и вызвать **файл > новый файл** пункта меню. В левой части **новый файл** диалогового окна выберите **Forms** слева, и **Forms ContentPage Xaml** (не **ContentPage форм**, который Создает код только страницы, или **представление содержимого**, который не является страницей). Присвойте странице имя, например, **HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "Диалоговое окно создания файла")

-----

Будут добавлены в проект два файла **HelloXamlPage.xaml** и файл кода программной **HelloXamlPage.xaml.cs**. 

## <a name="setting-page-content"></a>Установка содержимого страницы

Изменить **HelloXamlPage.xaml** так, чтобы только теги являются характерными для `ContentPage` и `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>
        
    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content` Теги являются частью уникальный синтаксис XAML. Сначала они может показаться недопустимый XML-код, но они являются допустимыми. Период не специальный символ в формате XML.

`ContentPage.Content` Теги, называются *элемент свойства* тегов. `Content` Это свойство `ContentPage`и обычно имеет значение единое представление или макет с дочерние представления. Обычно свойства становятся атрибутами в XAML, но было бы сложно настраивать `Content` атрибутов для сложного объекта. По этой причине свойство представляется как элемент XML, состоящее из имени класса и имя свойства, разделенных точкой. Теперь `Content` может быть установлено между `ContentPage.Content` тегов, следующим образом:

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

Также Обратите внимание, что `Title` установлен атрибут в корневом теге.

В настоящее время должно быть видно связь между классы, свойства и XML: класс Xamarin.Forms (такие как `ContentPage` или `Label`) отображается в файле XAML как элемент XML. Свойства этого класса, включая `Title` на `ContentPage` и семь свойств `Label`— обычно появляются как атрибуты XML.

Чтобы задать значения этих свойств существует многие сочетания клавиш. Некоторые свойства являются базовые типы данных: например, `Title` и `Text` свойства имеют тип `String`, `Rotation` имеет тип `Double`, и `IsVisible` (который является `true` по умолчанию и задано только для на рисунке) имеет тип `Boolean`.

`HorizontalTextAlignment` Свойство относится к типу `TextAlignment`, который является перечислением. Для свойства любого типа перечисления все, что нужно питания — имя члена.

Для более сложных типов свойств, тем не менее, преобразователи типов используются для синтаксического анализа XAML. Они относятся к классам в Xamarin.Forms, который является производным от `TypeConverter`. Много открытых классов, но некоторые — нет. Для этого конкретного файла XAML некоторые из этих классов играют роль в фоновом:

-  `LayoutOptionsConverter` для `VerticalOptions` свойства
-  `FontSizeConverter` для `FontSize` свойства
-  `ColorTypeConverter` для `TextColor` свойства

Эти конвертеры определяют допустимый синтаксис свойств.

`ThicknessTypeConverter` Может обрабатывать одного, двух или четырех чисел, разделенных запятыми. Если задано одно число, оно применяется ко всем четырем сторонам. С двумя числами во-первых, левого и правого заполнения и второй — верхней и нижней. Четыре цифры находятся в порядке слева, сверху, справа и нижней.

`LayoutOptionsConverter` Можно преобразовать имена открытые статические поля `LayoutOptions` структуру для значений типа `LayoutOptions`.

`FontSizeConverter` Может обрабатывать `NamedSize` члена или числовой размер.

`ColorTypeConverter` Принимает имена открытые статические поля `Color` структуры или шестнадцатеричных значений RGB, независимо от наличия альфа-канала, предшествует знак решетки (#). Ниже приведен синтаксис без альфа-канала.

 `TextColor="#rrggbb"`

Каждый мало буквы — шестнадцатеричная цифра. Вот, как включается альфа-канала.

 `TextColor="#aarrggbb">`

Альфа-канала Имейте в виду, что FF — полностью непрозрачный и 00 полностью прозрачна.

Два формата позволяют указать только один шестнадцатеричная цифра для каждого канала:

 `TextColor="#rgb"` `TextColor="#argb"`

В этих случаях эта цифра повторяется для образования значения. Например #CF3 — копия-FF-33 цвета RGB.

## <a name="page-navigation"></a>Навигация по страницам

При запуске **XamlSamples** программы, `MainPage` отображается. Чтобы увидеть вновь `HelloXamlPage` можно либо задать его в качестве нового запуска в **App.xaml.cs** файла или перехода на новую страницу из `MainPage`.

Чтобы реализовать навигации, сначала измените код в **App.xaml.cs** конструктор, чтобы `NavigationPage` создается объект:

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

Установка `Content` свойства страницы заменяет параметр `Content` свойства в XAML-файле. После компиляции и развертывание новой версии этой программы на экране появляется кнопка. Нажав клавишу, он переходит к `HelloXamlPage`. Ниже приведен результирующий страницу на iPhone, Android и UWP.

[![](get-started-with-xaml-images/helloxaml1.png "Поворот текста метки")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "поворачивать текст метки")

Можно перейти к `MainPage` с помощью **< обратно** кнопку на iOS с помощью стрелку влево в верхней части страницы или в нижней части телефона на Android, или Стрелка влево в верхней части страницы в Windows 10.

Вы можете поэкспериментировать с XAML для различных способов для подготовки к просмотру `Label`. Если вам нужно внедрить любые символы Юникода в тексте, можно использовать стандартный синтаксис XML. Например включаемые в парные приветствие, используйте следующую команду:

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Вот, как оно выглядит:

[![](get-started-with-xaml-images/helloxaml2.png "Текст метки с символами Юникода, повернуто")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "поворачивать текст метки с символами Юникода")

## <a name="xaml-and-code-interactions"></a>XAML и код взаимодействия

**HelloXamlPage** пример содержит только один `Label` на странице, но это необычной. Большинство `ContentPage` набор производные `Content` отсортировать макет некоторые свойства, такие как `StackLayout`. `Children` Свойство `StackLayout` определяется типом `IList<View>` , но фактически это объект типа `ElementCollection<View>`, и что коллекция может быть заполнена несколько представлений или другие макеты. В XAML этих родительско дочерних отношений устанавливаются с обычной иерархии XML. Ниже приведен файл XAML для новой страницы с именем **XamlPlusCodePage**:

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

Этот файл XAML синтаксически завершена, и вот как оно выглядит:

[![](get-started-with-xaml-images/xamlpluscode1.png "Несколько элементов управления на странице")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "нескольких элементов управления на странице")

Тем не менее скорее всего, рассмотрим следующую программу, чтобы быть функционально объемов. Возможно `Slider` должен вызвать `Label` для отображения текущего значения и `Button` , вероятно, предназначен для сделать что-нибудь в программе.

Как вы увидите в [часть 4. Основные сведения о привязке данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), задание отображения `Slider` с использованием `Label` могут быть обработаны полностью в XAML с привязкой данных. Однако полезно для просмотра кода решения сначала. Даже в этом случае обработка `Button` щелкните определенно требует кода. Это означает, что файл кода для `XamlPlusCodePage` должен содержать обработчики для `ValueChanged` событие `Slider` и `Clicked` событие `Button`. Давайте добавим их:

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

В файле XAML `Slider` и `Button` теги должны включать атрибуты для `ValueChanged` и `Clicked` события, которые ссылаются на эти обработчики:

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

Обратите внимание, что назначение обработчик для события совпадает с синтаксисом присваивания значения свойству.

Если обработчик `ValueChanged` событие `Slider` будет использовать `Label` для отображения текущего значения, обработчик должен ссылаться на этот объект из кода. `Label` Нуждается в имени, который указывается с помощью `x:Name` атрибута.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x` Префикс `x:Name` атрибут указывает, что этот атрибут привязан к XAML.

Имя, назначаемые `x:Name` атрибут имеет те же правила в качестве имен переменных в C#. Например он начинается с буквы или символа подчеркивания и не должно содержать внедренные пробелов.

Теперь `ValueChanged` можно задать обработчик событий `Label` для отображения нового `Slider` значение. Новое значение доступен из аргументов события:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Или может получить обработчик `Slider` объекта, вызвавшая данное событие из `sender` аргумент и получить `Value` свойство из того, что:

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

При запуске программы, `Label` не отображает `Slider` поскольку `ValueChanged` событие еще не запущено. Любое изменение, но `Slider` приводит значение для отображения:

[![](get-started-with-xaml-images/xamlpluscode2.png "Отображаемое значение ползунка")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "отображаемое значение \"ползунок\"")

Теперь для `Button`. Давайте имитировать ответ `Clicked` событий, отображая оповещения с `Text` кнопки. Обработчик событий можно безопасно привести `sender` аргумент `Button` и затем получить доступ к его свойствам:

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

Метод определяется как `async` из-за `DisplayAlert` метод является асинхронным и должны предваряться символом `await` оператор, который возвращает при завершении метода. Так как этот метод получает `Button` срабатыванием события из `sender` аргумента, и тот же обработчик может использоваться для нескольких кнопок.

Вы уже видели, что объект, определенный в XAML может инициировать событие, обрабатываемый в файл кода и доступность объект, определенный в XAML, используя имя, назначенное в файл кода программной `x:Name` атрибута. Это два основных способа, взаимодействующих код и XAML.

Некоторые дополнительные функции анализа в как можно достигнутой XAML works, проверив вновь созданным **файл XamlPlusCode.xaml.g.cs**, которой теперь включает в себя любое имя, ни одному `x:Name` атрибут как закрытое поле. Здесь является упрощенной версией этого файла:

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

Объявления этого поля позволяет свободно использовать в любом месте переменную `XamlPlusCodePage` файл разделяемого класса под вашей юрисдикции. Во время выполнения назначается после XAML, проверен. Это означает, что `valueLabel` поле является `null` при `XamlPlusCodePage` конструктор начинается, но действительными после `InitializeComponent` вызывается.

После `InitializeComponent` возвращает управление конструктора, как если они уже создан и инициализировать в код были построены визуальные элементы страницы. XAML-файл больше не играет любой роли в классе. Можно управлять этими объектами, на странице способом, который требуется, например, путем добавления представления к `StackLayout`, или параметр `Content` свойства страницы на другое полностью. Для «прохода по дереву» путем проверки `Content` свойства страниц и элементов в `Children` коллекции макетов. Можно задать свойства для представлений, доступ к таким образом, или динамическое назначение для них обработчики событий.

Вы можете. Это на странице, и XAML является только средством для построения его содержимого.

## <a name="summary"></a>Сводка

Это введение было показано, как XAML-файл и файл кода влияет на определение класса, а также взаимодействие файлов XAML и кода. Но XAML также имеет свой собственный уникальный синтаксических функции, позволяющие использовать очень гибко. Можно приступить к изучению их в [часть 2. Синтаксис Essential XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Часть 5. Из привязка данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
