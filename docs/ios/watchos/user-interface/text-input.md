---
title: Работа с watchOS ввода текста в Xamarin
description: В этом документе описывается watchOS ввода текста в Xamarin. В нем описывается PresentTextInputController метода, ретуширование, обычный текст, эмодзи и режим диктовки.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 2092b12254008936f2c5b6a7d9dd610ff751e802
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122365"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Работа с watchOS ввода текста в Xamarin

Apple Watch не предоставляет клавиатуры для пользователей во входной строке, однако он поддерживает некоторые альтернативы watch с поддержкой:

- Выбор из списка предварительно определенных параметров текста
- Диктовка Siri
- Выбор эмодзи
- Scribble распознавания рукописного ввода буквы, буквы (впервые представлено в watchOS 3).

Имитатор сейчас не поддерживает режим диктовки, но вы все еще сможете проверить ввод текста контроллера другие параметры, например Scribble, как показано ниже:

![](text-input-images/textinput-sml.png "Тестирование параметр scribble")

Для приема ввода текста в приложении для Apple watch:

1. Создайте массив строк предварительно заданных параметров.
2. Вызовите `PresentTextInputController` с массивом, следует ли разрешить эмодзи, или нет и `Action` , вызываемого при завершении пользователем.
3. В действие завершения Проверьте входной результат и предпринять соответствующие действия в приложении (возможно задание метки текстовое значение).

В следующем фрагменте кода представляет три предварительно определенные параметры для пользователя:

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

`WKTextInputMode` Перечисление имеет три значения:

- Обычный
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Обычный

При этом обычный режим, пользователь может:

- Режим диктовки,
- Scribble, или
- из списка предварительно определенных, который предоставляет приложение.

[![](text-input-images/plain-scribble-sml.png "Режим диктовки, Scribble, или из списка предварительно определенных, предоставляющий приложения")](text-input-images/plain-scribble.png#lightbox)

Результат всегда возвращается как `NSObject` , может быть приведен к `string`.

## <a name="emoji"></a>Эмодзи

Существует два вида эмодзи.

- Регулярные эмодзи Юникода
- Анимированные изображения

Когда пользователь выбирает эмодзи Юникода, он возвращается в виде строки.

Если выбран анимированном изображении эмодзи `result` в завершение будет содержать обработчик `NSData` , содержащий эмодзи `UIImage`.

## <a name="accepting-dictation-only"></a>Только принимая диктовки

Для перевода пользователя непосредственно на экран диктовки без отображения есть предложения (или параметр Scribble):

- передать пустой массив для списка предложений и
- Задайте `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

При пользователь говоря, в окне контрольных значений отображаются следующий экран, в том числе текст, как понятно (например «This is a test»):

![](text-input-images/dictation.png "Рассказывая пользователя, на экране Контрольные значения отображается текст, как понятно")

Как только они кнопку **сделать** возвращается текст кнопки.



## <a name="related-links"></a>Связанные ссылки

- [Apple doc текста и метки](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Введение в watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
