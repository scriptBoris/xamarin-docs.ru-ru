---
title: Запись Xamarin.Forms
description: В этой статье описываются способы использования класса входа Xamarin.Forms для принятия однострочный текст или пароля, вводимого в приложении.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 95afdfde878759d4a598e200d16fe6fb1fa2005e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998251"
---
# <a name="xamarinforms-entry"></a>Запись Xamarin.Forms

_Однострочный текст или пароля_

Xamarin.Forms `Entry` используется для ввода текста на одну строку. `Entry`, Например `Editor` Просмотр, поддерживает несколько типов клавиатуры. Кроме того `Entry` можно использовать в качестве поля пароля.

## <a name="display-customization"></a>Настройки отображения

### <a name="setting-and-reading-text"></a>Установка и чтение текста

`Entry`, Как и с другими представлениями представления текста предоставляет `Text` свойство. Это свойство может использоваться для задания и считывать текст, представленный `Entry`. В следующем примере показано задание `Text` свойства в XAML:

```xaml
<Entry Text="I am an Entry" />
```

В C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Чтобы прочитать текст, доступ к `Text` свойства в C#:

```csharp
var text = MyEntry.Text;
```

> [!NOTE]
> Ширина `Entry` может быть определена путем задания его `WidthRequest` свойство. Не зависят от ширину `Entry` , определенное на основе значения из его `Text` свойство.

### <a name="limiting-input-length"></a>Ограничение длины входных

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) Свойство может использоваться для ограничения длины входных данных, допустимое для [ `Entry` ](xref:Xamarin.Forms.Entry). Это свойство должно быть присвоено положительное целое число:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Объект [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) свойства значение 0 указывает, что входные данные не будет разрешено и значение `int.MaxValue`, который является значением по умолчанию для [ `Entry` ](xref:Xamarin.Forms.Entry), указывает, что не действующие ограничения на число символов, которые могут быть введены.

### <a name="keyboards"></a>Клавиатуры

Клавиатуры, выводится тогда, когда пользователи взаимодействуют с `Entry` могут быть заданы программно с помощью `Keyboard` свойство.

Доступны следующие параметры для типа клавиатуры.

- **По умолчанию** &ndash; клавиатуры по умолчанию
- **Чат** &ndash; используется для сообщения и местах где полезны эмодзи
- **Адрес электронной почты** &ndash; используется при вводе адреса электронной почты
- **Числовые** &ndash; используется при вводе чисел
- **Телефон** &ndash; используется при вводе телефонных номеров
- **URL-адрес** &ndash; используется для ввода пути к файлам и веб-адреса

Существует [пример каждого сочетания](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) нашего раздела «инструкции».

### <a name="enabling-and-disabling-spell-checking"></a>Включение и отключение проверка орфографии

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) Свойство, управляет ли проверка орфографии включена. По умолчанию задано значение `true`. Как пользователь вводит текст, заданный опечаток.

Однако для некоторых сценариях записи текста, например ввода имени пользователя, проверка орфографии дает негативной, поэтому следует отключить, задав [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) свойства `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Когда [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) свойству `false`и пользовательских сочетаний не используется, средство проверки орфографии собственного будет отключена. Тем не менее если [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) имеет был набор, который отключает орфографии поиска, например [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), `IsSpellCheckEnabled` свойство учитывается. Таким образом, свойство не позволяет включить проверку орфографии для `Keyboard` , явно отключает его.

### <a name="placeholders"></a>Заполнители

`Entry` можно задать для отображения текста заполнителя, когда он не хранит ввод данных пользователем. На практике это часто рассматривается в формах для уточнения содержимого, которое подходит для данного поля. Цвет текста заполнителя, не могут быть изменены и будет таким же, вне зависимости от `TextColor` параметр. Если проект предусматривает цвет настраиваемого заполнителя, необходимо переключиться на [пользовательское средство отрисовки](). Создаст следующие `Entry` с «Username» в качестве заполнителя в XAML:

```xaml
<Entry Placeholder="Username" />
```

В C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Пример записи заполнителя")

### <a name="password-fields"></a>Поля пароля

`Entry` предоставляет `IsPassword` свойство. Когда `IsPassword` является `true`, содержимое поля будут представлены в виде кругов черным:

В XAML:

```xaml
<Entry IsPassword="true" />
```

В C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Пример записи IsPassword")

Заполнители, которые могут использоваться с экземплярами `Entry` , настроенные в качестве пароля:

В XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

В C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Запись IsPassword и примере заполнитель")


### <a name="colors"></a>Цвета

Использовать настраиваемый фон и цвета текста с помощью следующих привязываемые свойства можно задать запись.

- **TextColor** &ndash; задает цвет текста.
- **BackgroundColor** &ndash; задает цвет фона для текста показано.

Особое внимание необходимо, чтобы гарантировать, что цвета будет доступна для использования на каждой платформе. Так как каждая платформа имеет различные значения по умолчанию для цветов текста и фона, часто необходимо задать оба, если задается один.

Чтобы задать цвет текста записи, используйте следующий код:

В XAML:

```xaml
<Entry TextColor="Green" />
```

В C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "Пример записи TextColor")

Обратите внимание, что заполнитель не влияет на заданный `TextColor`.

Чтобы задать цвет фона в XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

В C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "Пример записи BackgroundColor")

Следите за тем, чтобы убедиться в том, что цвета фона и текста, которые вы выбрали могут использоваться на каждой платформе и не закрывают любой текст заполнителя.

## <a name="events-and-interactivity"></a>События и интерактивность

Запись предоставляет два события:

- [TextChanged](xref:Xamarin.Forms.Entry.TextChanged) &ndash; возникает при изменении текста в записи. Содержит текст до и после изменения.
- [Завершено](xref:Xamarin.Forms.Entry.Completed) &ndash; вызывается, когда пользователь завершен входные данные с помощью возвращаемого значения клавиши на клавиатуре.

### <a name="completed"></a>Завершено

`Completed` Событие используется для реагирования на завершение взаимодействия с записью. `Completed` вызывается, когда пользователь завершает входных данных с полем, введя клавиша return на клавиатуре. Обработчик для события представляет собой Универсальное событие обработчика, используя отправителя и `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

Завершенное событие можно подписаться в XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

и C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>TextChanged

`TextChanged` Событие используется для реагирования на изменения в содержимое поля.

`TextChanged` возникает каждый раз, когда `Text` из `Entry` изменения. Обработчик для события принимает экземпляр `TextChangedEventArgs`. `TextChangedEventArgs` предоставляет доступ к старое и новое значения `Entry` `Text` через `OldTextValue` и `NewTextValue` свойства:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged` Событие можно подписаться в XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

и C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Связанные ссылки

- [Текст (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Операция API](xref:Xamarin.Forms.Entry)
