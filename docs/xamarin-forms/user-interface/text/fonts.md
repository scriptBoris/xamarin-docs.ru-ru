---
title: Шрифты в Xamarin.Forms
description: В этой статье объясняется, как для указания сведений о шрифт для элементов управления, отображающие текст в Xamarin.Forms приложений.
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 3d5fe936da9086dd7201b7ee7d91185b81eb65a1
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269035"
---
# <a name="fonts-in-xamarinforms"></a>Шрифты в Xamarin.Forms

В этой статье описываются как Xamarin.Forms позволяет указать атрибуты шрифта (включая вес и размер) на элементы управления, отображающие текст. Сведения о шрифте может быть [указанных в коде](#Setting_Font_in_Code) или [указанные в XAML](#Setting_Font_in_Xaml).
Можно также использовать [пользовательский шрифт](#Using_a_Custom_Font).

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>Установка шрифта в коде

Используйте три свойства шрифте всех элементов управления, отображающие текст:

- **Семейство FontFamily** &ndash; `string` имя шрифта.
- **FontSize** &ndash; размер шрифта, как `double`.
- **FontAttributes** &ndash; строка, определяющая стиль сведения, такие как *курсив* и **Полужирный** (с помощью `FontAttributes` перечисления в C#).

Этот код показан способ создать метку и укажите размер и вес для отображения.

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>Размер шрифта

`FontSize` Свойство может быть присвоено значение типа double, например:

```csharp
label.FontSize = 24;
```

Можно также использовать `NamedSize` перечисления, который имеет четыре встроенных возможностей; Xamarin.Forms выбирает оптимальный размер для каждой платформы.

-  **Micro**
-  **Небольшой**
-  **Средний**
-  **Большой**

`NamedSize` Перечисления можно использовать везде, где `FontSize` можно задать с помощью `Device.GetNamedSize` метод, чтобы преобразовать значение в `double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>Атрибуты шрифта

Стили шрифтов, такие как **полужирным** и *курсивом* может устанавливаться на `FontAttributes` свойство. В настоящее время поддерживаются следующие значения:

-  **None**
-  **Полужирный**
-  **курсив**

`FontAttribute` Перечисления можно использовать следующим образом (можно указать один атрибут или `OR` их вместе):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

Некоторые элементы управления Xamarin.Forms (такие как `Label`) также поддерживают разные атрибуты шрифтов в строку с помощью `FormattedString` класса. Объект `FormattedString` состоит из одного или более `Span`s, каждый из которых может иметь свой собственный форматирование атрибуты.

`Span` Класс имеет следующие атрибуты:

* **Текст** &ndash; отображаемое значение
* **Семейство FontFamily** &ndash; имя шрифта
* **FontSize** &ndash; размер шрифта
* **FontAttributes** &ndash; , например сведения о стиле *курсивом* и **полужирным шрифтом**
* **ForegroundColor** &ndash; цвет текста
* **BackgroundColor** &ndash; цвет фона

Пример создания и отображения `FormattedString` ниже &ndash; Обратите внимание, что он назначен метки `FormattedText` свойство и не `Text` свойство.

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```

### <a name="setting-font-info-per-platform"></a>Сведения о шрифте настройки каждой платформы

Кроме того `Device.RuntimePlatform` свойство может использоваться для установки шрифтов имен для каждой платформы, как показано в этом коде:

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

Хорошим источником информации о шрифтах для iOS — [iosfonts.com](http://iosfonts.com).

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>Установка шрифта в XAML

Xamarin.Forms элементы управления, отображаемого текста, имеют `Font` свойство, которое может быть задано в XAML. Для задания шрифта в XAML проще использовать именованные размер значений перечисления, как показано в следующем примере:

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

Имеется встроенный преобразователь для `Font` свойство, позволяющее все параметры шрифта можно выразить в виде строкового значения в языке XAML. В следующих примерах показано, как указать атрибуты шрифтов и размеров в XAML:

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

Чтобы указать несколько `Font` сочетание необходимые параметры в единую `Font` строке атрибута. Строка атрибута шрифта должен быть в формате `"[font-face],[attributes],[size]"`. Важен порядок параметров, все параметры являются необязательными и несколько `attributes` могут быть заданы, например:

```xaml
<Label Text="Small bold text" Font="Bold, Micro" />
<Label Text="Medium custom font" Font="MarkerFelt-Thin, 42" />
<Label Text="Really big bold and italic text" Font="Bold, Italic, 72"  />
```

`FormattedString` Класс также может использоваться в XAML, как показано ниже:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) Можно также использовать в XAML для подготовки к просмотру на каждой платформе другой шрифт. В следующем примере используется пользовательский шрифт для операций ввода-вывода (<span style="font-family:MarkerFelt-Thin">тонкой MarkerFelt</span>) и указывает атрибуты или размера на других платформах:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

При определении пользовательских начертание шрифта, рекомендуется всегда использовать `OnPlatform`, как трудно найти шрифт, который доступен на всех платформах.

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>С помощью пользовательского шрифта

С помощью отличных от встроенных гарнитуры шрифта требует определенную кодировку конкретную платформу. На этом снимке экрана показан пользовательский шрифт **Lobster** из [Google открытая шрифты](https://www.google.com/fonts) к просмотру с помощью Xamarin.Forms.

 [![Пользовательский шрифт в iOS и Android](fonts-images/custom-sml.png "пользовательские шрифты пример")](fonts-images/custom.png#lightbox "примере пользовательские шрифты")

Ниже описаны шаги, необходимые для каждой платформы. Если включают файлы пользовательского шрифта с помощью приложения, обязательно убедитесь, что шрифт лицензии разрешены для распространения.

### <a name="ios"></a>iOS

Можно отобразить пользовательский шрифт, сначала обеспечивая его загрузки, а затем обращения к нему по имени с помощью Xamarin.Forms `Font` методы.
Следуйте инструкциям в [этой записи блога](http://blog.xamarin.com/custom-fonts-in-ios/):

1. Добавить файл шрифта с **действие при построении: BundleResource**, и
2. Обновление **Info.plist** файла (**шрифты, предоставляемые приложения**, или `UIAppFonts`, ключ), затем
3. Ссылаться на него по имени везде, где можно определить шрифт в Xamarin.Forms!

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

Xamarin.Forms для Android можно ссылаться на пользовательский шрифт, который был добавлен в проект, следуя определенный стандарт именования. Сначала следует добавить файл шрифта для **активы** папки в проект приложения и набора *действие при построении: AndroidAsset*. Затем используйте полный путь и *имя шрифта* разделенных решетки (#), как имя шрифта в Xamarin.Forms, как показано в следующем фрагменте кода:

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms для платформ Windows может ссылаться на пользовательский шрифт, который был добавлен в проект, следуя определенный стандарт именования. Сначала следует добавить файл шрифта для **/Assets/шрифты и** папки в проект приложения и набора <span class="UIItem">сборки: содержимое действия</span>. Затем используйте полный путь и шрифта имени файла, следуют решетки (#) и <span class="UIItem">имя шрифта</span>, как показано в следующем фрагменте кода:

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> Обратите внимание, что имя файла шрифта и имя шрифта может отличаться. Чтобы узнать имя шрифта в Windows, щелкните правой кнопкой мыши файл TTF и выберите **предварительной версии**. Имя шрифта можно определить, затем из окна предварительного просмотра.

Итак, общий код для приложения полностью готов. Теперь мы реализуем код набора номера для конкретной платформы в качестве [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

### <a name="xaml"></a>XAML

Можно также использовать [ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values) в XAML для отрисовки пользовательского шрифта:

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

<a name="Summary" />

## <a name="summary"></a>Сводка

Xamarin.Forms предоставляет параметры по умолчанию позволяют размер текст легко для всех поддерживаемых платформах. Он также позволяет задать начертание и размер &ndash; даже по-разному для каждой платформы &ndash; когда требуется более точное управление. `FormattedString` Класс может использоваться для создания строка, содержащая спецификации шрифтов с помощью `Span` класса.

Информацию о шрифта можно также задать в XAML, с помощью атрибутов шрифта в правильном формате или `FormattedString` элемент с `Span` дочерних элементов.


## <a name="related-links"></a>Связанные ссылки

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
