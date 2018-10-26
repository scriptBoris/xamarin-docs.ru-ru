---
title: Использование платформы естественного языка с помощью Xamarin.iOS
description: Этот документ описывает платформу естественного языка. Появился в iOS 12, платформа естественный язык — предпочтительный iOS API-интерфейса для распознавания языка, идентификация частей речи и распознавания именованных сущностей.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: b0a5d3e75ebbcd70a60a3e4e76eebe166117d31c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131848"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Использование платформы естественного языка с помощью Xamarin.iOS

Появился в iOS 12, framework естественного языка позволяет обработки естественного языка на устройстве. Он поддерживает распознавание языка, разметки и добавление тегов. Разметки разбивает текст на его компонент слова, предложения или абзаца; Добавление тегов определяет части речи, людей, мест и организаций.

Пользовательские модели машинного Обучения Core также может использоваться платформой естественного языка для классификации и пометка текста в специализированных контекстов.

[NSLinguisticTagger](https://developer.xamarin.com/api/type/Foundation.NSLinguisticTagger/) класс по-прежнему доступен. Тем не менее платформа естественный язык — предпочтительный механизм для обработки естественного языка.

## <a name="sample-app-xamarinnl"></a>Пример приложения: XamarinNL

Чтобы научиться использовать платформу естественного языка с помощью Xamarin.iOS, взгляните на [XamarinNL пример приложения](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL).
Этот пример приложения демонстрирует использование структурой естественного языка для:

- [Распознает языков](#recognizing-languages).
- [Разбиение текста на слова и предложения](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Тег с именем сущности и частей речи](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Распознавание языков

**Распознаватель** вкладке образец приложения демонстрирует использование [`NLLanguageRecognizer`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguageRecognizer/)
Чтобы определить язык для блока текста.

> [!NOTE]
> Распознавание языка находится определенного типа классификации текста. Платформа естественного языка также поддерживает пользовательский текст классификации с помощью предоставленной разработчиком моделей машинного Обучения Core. Дополнительные сведения, взгляните на [Знакомство с платформой Framework естественного языка](https://developer.apple.com/videos/play/wwdc2018/713/) сеанса из WWDC 2018 г.

### <a name="dominant-language"></a>Главный языка

Коснитесь **языка** кнопки для идентификации доминирующего языка в вводимых пользователем данных.

`HandleDetermineLanguageButtonTap` Метод `LanguageRecognizerViewController` использует [`GetDominantLanguage`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage/)
метод `NLLanguageRecognizer` для выборки [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
в тексте основного языка:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Язык вероятности

Коснитесь **вероятности языка** кнопку, чтобы получить список гипотез язык для ввода данных пользователем.

`HandleLanguageProbabilitiesButtonTap` Метод `LanguageRecognizerViewController` создает экземпляр класса `NLLanguageRecognizer` и запрашивает у него [`Process`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.Process/)
текст для пользователя. Затем он вызывает распознаватель языка [`GetNativeLanguageHypotheses`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses)
метод, который извлекает словарь языков и оцененных вероятностей. `LanguageRecognizerTableViewController` Класс отображается этих языков и вероятности.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

Потенциальные `NLLanguage` значениям относятся:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Полный список поддерживаемых языков доступен как часть [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
Документация по API перечисления.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Маркирование текста на слова, предложения и абзацы

**Разметчика** вкладке примера приложения показано, как выделить блок текста в свои слова компонента или предложений с [ `NLTokenizer` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTokenizer/).

Коснитесь **слова** или **предложения** кнопку, чтобы получить список токенов. Каждый токен, связанный с слово или предложение в исходном тексте.

`ShowTokens` разбиений, введенные пользователем в токены, вызвав [`GetTokens`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTokenizer.GetTokens/)
метод `NLTokenizer`. Этот метод возвращает массив [`NSValue`](https://developer.xamarin.com/api/type/Foundation.NSValue/)
объекты, каждый упаковки `NSRange` значение, соответствующее токен в исходном тексте.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController` Выполняет визуализацию одного токена в каждой ячейке таблицы. Он извлекает `NSRange` из маркера `NSValue`, находит соответствующую строку в исходном тексте и задает метку на ячейка представления таблиц:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Добавление тегов именованных сущностей и частей речи

**Средство создания тегов** вкладке XamarinNL образец приложения демонстрирует использование [`NLTagger`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagger/)
класс должен быть сопоставлен категории маркеры входной строки.
Платформа естественного языка включает встроенную поддержку распознавание людей, мест, организаций и частей речи.

> [!NOTE]
> Платформа естественного языка также поддерживает пользовательские схемы расстановки тегов с помощью предоставленной разработчиком моделей машинного Обучения Core. Дополнительные сведения, взгляните на [Знакомство с платформой Framework естественного языка](https://developer.apple.com/videos/play/wwdc2018/713/) сеанса из WWDC 2018 г.

Коснитесь **именованных сущностей** или **частей речи** кнопку для выборки:

- Массив `NSValue` объектов, каждый упаковки `NSRange` для токена в исходном тексте.
- Массив [ `NLTag` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/) значения — категории для `NSValue` токенов в один и тот же индекс массива.

В `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` и `HandleNamedEntitiesButtonTap` каждый вызов `ShowTags`, передав вдоль [ `NLTagScheme` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagScheme/) — либо `NLTagScheme.LexicalClass` (по частям речи) или `NLTagScheme.NameType` (для именованных сущностей).

`ShowTags` Создает `NLTagger`, создание его экземпляра с помощью массива `NLTagScheme` типов, для которой он будет направлен запрос (в данном случае только переданный `NLTagScheme` значение). Затем он использует [`GetTags`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTagger.GetTags/)
метод `NLTagger` для определения теги, относящиеся к текст в вводимых пользователем данных.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

Теги отображаются в таблице по `LanguageTaggerTableViewController`.

Потенциальные `NLTag` значениям относятся:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Полный список поддерживаемых тегов доступен как часть [`NLTag`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/)
Документация по API перечисления.

## <a name="related-links"></a>Связанные ссылки

- [Пример приложения — XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [Знакомство с Framework естественного языка](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Естественного языка (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
