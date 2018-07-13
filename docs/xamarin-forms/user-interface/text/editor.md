---
title: Редактор Xamarin.Forms
description: В этой статье объясняется, как использовать элемент управления редактора Xamarin.Forms принимать ввод многострочного текста в приложении.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 4879ff88d5bbdab5aa92024bee7f50239a141e3b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995871"
---
# <a name="xamarinforms-editor"></a>Редактор Xamarin.Forms

_Ввод многострочного текста_

`Editor` Элемент управления используется для ввода нескольких линий. В этой статье рассматривается:

- **[Настройки](#customization)**  &ndash; параметры клавиатуры и цвета.
- **[Интерактивные возможности](#interactivity)**  &ndash; события, которые можно прослушивать для обеспечения взаимодействия.

## <a name="customization"></a>Настройка

### <a name="setting-and-reading-text"></a>Установка и чтение текста

`Editor`, Как и с другими представлениями представления текста предоставляет `Text` свойство. Это свойство может использоваться для задания и считывать текст, представленный `Editor`. В следующем примере показано задание `Text` свойства в XAML:

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

### <a name="keyboards"></a>Клавиатуры

Клавиатуры, выводится тогда, когда пользователи взаимодействуют с `Editor` могут быть заданы программно с помощью [ ``Keyboard`` ](xref:Xamarin.Forms.Keyboard) свойство.

Доступны следующие параметры для типа клавиатуры.

- **По умолчанию** &ndash; клавиатуры по умолчанию
- **Чат** &ndash; используется для сообщения и местах где полезны эмодзи
- **Адрес электронной почты** &ndash; используется при вводе адреса электронной почты
- **Числовые** &ndash; используется при вводе чисел
- **Телефон** &ndash; используется при вводе телефонных номеров
- **URL-адрес** &ndash; используется для ввода пути к файлам веб-адреса

Существует [пример каждого сочетания](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) нашего раздела «инструкции».

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
