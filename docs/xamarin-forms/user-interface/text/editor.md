---
title: Редактор Xamarin.Forms
description: В этой статье объясняется, как использовать элемент управления редактора Xamarin.Forms принимать ввод многострочного текста в приложении.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/31/2018
ms.openlocfilehash: 23ba73eb4099a9db9e989c68ae7f381e1c96541d
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "39270460"
---
# <a name="xamarinforms-editor"></a>Редактор Xamarin.Forms

_Ввод многострочного текста_

[ `Editor` ](xref:Xamarin.Forms.Editor) Элемент управления используется для ввода нескольких линий. В этой статье рассматриваются следующие действия:

- **[Настройки](#customization)**  &ndash; параметры клавиатуры и цвета.
- **[Интерактивные возможности](#interactivity)**  &ndash; события, которые можно прослушивать для обеспечения взаимодействия.

## <a name="customization"></a>Настройка

### <a name="setting-and-reading-text"></a>Установка и чтение текста

[ `Editor` ](xref:Xamarin.Forms.Editor), Как и с другими представлениями представления текста предоставляет `Text` свойство. Это свойство может использоваться для задания и считывать текст, представленный `Editor`. В следующем примере показано задание `Text` свойства в XAML:

```xaml
<Editor Text="I am an Editor" />
```

В C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Чтобы прочитать текст, доступ к `Text` свойства в C#:

```csharp
var text = MyEditor.Text;
```

### <a name="limiting-input-length"></a>Ограничение длины входных

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) Свойство может использоваться для ограничения длины входных данных, допустимое для [ `Editor` ](xref:Xamarin.Forms.Editor). Это свойство должно быть присвоено положительное целое число:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Объект [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) свойства значение 0 указывает, что входные данные не будет разрешено и значение `int.MaxValue`, который является значением по умолчанию для [ `Editor` ](xref:Xamarin.Forms.Editor), указывает, что не действующие ограничения на число символов, которые могут быть введены.

### <a name="auto-sizing-an-editor"></a>Автоматического изменения размера редактор

[ `Editor` ](xref:Xamarin.Forms.Editor) Можно делать автоматическое масштабирование на его содержимое, устанавливая [ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize) свойства [ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), который представляет собой значение [ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption) перечисления. Это перечисление имеет два значения.

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) Указывает, что автоматическое изменение размеров отключено и значение по умолчанию.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) Указывает, что включено автоматическое изменение размеров.

Это можно сделать в коде следующим образом:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Если включено автоматическое изменение размеров, высота [ `Editor` ](xref:Xamarin.Forms.Editor) увеличивается, когда пользователь заполняет его с текстом, а высота будет уменьшаться, как пользователь удаляет текст.

> [!NOTE]
> [ `Editor` ](xref:Xamarin.Forms.Editor) Будет if не Авторазмер [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) свойства.

### <a name="customizing-the-keyboard"></a>Настройка клавиатуры

Клавиатуры, выводится тогда, когда пользователи взаимодействуют с [ `Editor` ](xref:Xamarin.Forms.Editor) могут быть заданы программно с помощью [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) свойства, чтобы одно из следующих свойств из [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) класса:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) — используется для сообщения и мест, где полезны эмодзи.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) — клавиатуры по умолчанию.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) — используется при вводе адреса электронной почты.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) — используется при вводе чисел.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) — используется при вводе текста, без каких-либо [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags) указанного.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) — используется при вводе телефонных номеров.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) — используется при вводе текста.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) — используется для ввода пути к файлам веб-адреса.

Это можно сделать в XAML следующим образом:

```xaml
<Editor Keyboard="Chat" />
```

Ниже приведен аналогичный код C#:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Примеры каждого клавиатуры можно найти в нашей [рецепты](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) репозитория.

[ `Keyboard` ](xref:Xamarin.Forms.Keyboard) Класс также имеет [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) фабричный метод, который может использоваться для настройки клавиатуры, указав режим регистр букв, проверка орфографии и предложения. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) значения перечисления заданы как аргументы для метода с настраиваемый `Keyboard` возвращению. `KeyboardFlags` Перечисление содержит следующие значения:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) — появившихся с клавиатурой.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) — Указывает, будет автоматически прописной первую букву каждого введенного предложения первое слово.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) — Указывает, что проверка орфографии выполняется на введенный текст.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) — Указывает, что варианты завершения будут предоставляться на введенный текст.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) — Указывает, будет автоматически прописной первую букву каждого слова.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) — Указывает, будет автоматически капитализация каждого символа.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) — Указывает, что будет выполняться без автоматического использования прописных букв.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) — Указывает, что проверка орфографии и завершение слова, содержащего будет выполняться на введенный текст.

В следующем примере кода XAML показано, как настроить значение по умолчанию [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) предлагаем завершение слова и реализуйте весь потенциал каждого введенного символа:

```xaml
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

Ниже приведен аналогичный код C#:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Включение и отключение проверка орфографии

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) Свойство, управляет ли проверка орфографии включена. По умолчанию задано значение `true`. Как пользователь вводит текст, заданный опечаток.

Однако для некоторых сценариях записи текста, например ввода имени пользователя, проверка орфографии дает негативной, поэтому следует отключить, задав [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) свойства `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Когда [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) свойству `false`и пользовательских сочетаний не используется, средство проверки орфографии собственного будет отключена. Тем не менее если [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) имеет был набор, который отключает орфографии поиска, например [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), `IsSpellCheckEnabled` свойство учитывается. Таким образом, свойство не позволяет включить проверку орфографии для `Keyboard` , явно отключает его.

### <a name="setting-placeholder-text"></a>Параметр замещающий текст

[ `Editor` ](xref:Xamarin.Forms.Editor) Можно задать, чтобы показать текст заполнителя, если он не хранит ввод данных пользователем. Это достигается путем установки [ `Placeholder` ](xref:Xamarin.Forms.Editor.Placeholder) свойства `string`и часто используется для указания типа содержимого, которое подходит для `Editor`. Кроме того, цвет текста заполнителя можно управлять, задав [ `PlaceholderColor` ](xref:Xamarin.Forms.Editor.PlaceholderColor) свойства [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="colors"></a>Цвета

`Editor` можно задать, чтобы использовать пользовательский цвет фона через `BackgroundColor` свойство. Особое внимание необходимо, чтобы гарантировать, что цвета будет доступна для использования на каждой платформе. Так как каждая платформа имеет различные значения по умолчанию для цвета текста, может потребоваться задать пользовательский цвет фона для каждой платформы. См. в разделе [работа с платформы Tweaks](~/xamarin-forms/platform/device.md) Дополнительные сведения об оптимизации пользовательский Интерфейс для каждой платформы.

В C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

В XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Редактор с BackgroundColor пример")

Убедитесь, что цвета фона и текста, которые вы выбрали могут использоваться на каждой платформе и не закрывают любой текст заполнителя.

## <a name="interactivity"></a>Интерактивность

`Editor` предоставляет два события:

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; возникает при изменении текста в редакторе. Содержит текст до и после изменения.
- [Завершено](xref:Xamarin.Forms.Editor.Completed) &ndash; вызывается, когда пользователь завершен входные данные с помощью возвращаемого значения клавиши на клавиатуре.

### <a name="completed"></a>Завершено

`Completed` Событие используется для реагирования на завершение взаимодействия с `Editor`. `Completed` вызывается, когда пользователь завершает входных данных с полем, введя клавиша return на клавиатуре. Обработчик для события представляет собой Универсальное событие обработчика, используя отправителя и `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

Завершенное событие можно подписаться в коде и XAML:

В C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

В XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged` Событие используется для реагирования на изменения в содержимое поля.

`TextChanged` возникает каждый раз, когда `Text` из `Editor` изменения. Обработчик для события принимает экземпляр `TextChangedEventArgs`. `TextChangedEventArgs` предоставляет доступ к старое и новое значения `Editor` `Text` через `OldTextValue` и `NewTextValue` свойства:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Завершенное событие можно подписаться в коде и XAML:

В коде:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

В XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```


## <a name="related-links"></a>Связанные ссылки

- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Редактор API](xref:Xamarin.Forms.Editor)
