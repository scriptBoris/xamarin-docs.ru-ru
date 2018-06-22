---
title: Android речи
description: В этой статье описываются основные принципы использования очень мощный Android.Speech пространства имен. С момента ее создания Android была возможность распознавания речи и выводят их как текст. Это относительно простой процесс. Для преобразования текста в речь, однако процесс является более сложным, как только не речи нужно принимать во внимание, но также языков, доступных и установленных в системе преобразования текста в речь (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/02/2018
ms.openlocfilehash: bdaa9bf09485c06551a2df15a2e3a4b410a53e75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769771"
---
# <a name="android-speech"></a>Android речи

_В этой статье описываются основные принципы использования очень мощный Android.Speech пространства имен. С момента ее создания Android была возможность распознавания речи и выводят их как текст. Это относительно простой процесс. Для преобразования текста в речь, однако процесс является более сложным, как только не речи нужно принимать во внимание, но также языков, доступных и установленных в системе преобразования текста в речь (TTS)._

## <a name="speech-overview"></a>Общие сведения о речи

С системой, в которой телефонных разговоров «понимает» и enunciates, что такое типизированные — речи в текст и преобразования текста в речь, — постоянно растущего область, в разработке мобильных приложений, как растет потребность в естественной связи с нашей устройствами. Существует несколько экземпляров где наличие компонента, который преобразование текста в речь, или наоборот, это средство удобно использовать для включения в приложение android.

Например с помощью приведения работу при использовании мобильного телефона параллельно, пользователи хотят руки свободного способ работы своих устройств. Множество различных Android форм-факторов, например Android носят — и включения расширяющие когда-либо из них будет использовать устройства Android (например, планшеты и дополняет Примечание) создал большего фокус эффективной TTS приложений.

Google предоставляет разработчику богатый набор интерфейсов API в пространстве имен Android.Speech для большинства экземпляров сделав устройством «речи виду» (например, программное обеспечение, разработанное для слепых).  Пространство имен включает средство, позволяющее текст быть преобразована в речи через `Android.Speech.Tts`, контроль над подсистему, используемую для выполнения преобразования, а также ряд `RecognizerIntent`s, которая допускает речи для преобразования в текст.

Существуют ограничения, в зависимости от оборудования, используемого средства существуют для речи должно быть понятно. Маловероятно, что устройство успешно интерпретирует все слышит во всех доступных языках.

## <a name="requirements"></a>Требования

Нет никаких особых требований, в этом руководстве, отличные от необходимости микрофона и динамика устройства.

Интерпретация речи устройства Android лежит использование `Intent` с соответствующими `OnActivityResult`.
Очень важно, однако для распознавания речи не поддерживается, но интерпретируемых в текст. Различие очень важно.

### <a name="the-difference-between-understanding-and-interpreting"></a>Разница между понимание и интерпретация

Простое определение понимания является возможность определить, сигнал и контекст реальные значения указанного. Для интерпретации просто означает слова и вывода их в другой форме.

Рассмотрим следующий простой пример, используемый в повседневной диалога. 

<kbd>Привет как дела?</kbd>

Без перегиба (учетом особенностей конкретных слов или части слов) это простой вопрос. Тем не менее если большие интервалы применяется к строке, лица прослушивание обнаружит что спрашивающим не слишком довольны и возможно должна была или что спрашивающим unwell. Если акцент делается на «are», пользователь, запросом, обычно более заинтересованы в ответе.

Без достаточно мощных звуковых, обработку, чтобы использовать перегиба и степени искусственный интеллект (AI) для понимания контекста, программное обеспечение не начинать, чтобы понять, что было сказано, наиболее простой телефонный можно сделать преобразования речи в текст.

## <a name="setting-up"></a>Настройка

Перед использованием речи системы, то целесообразно всегда убедитесь, что устройство имеет микрофона. Будет немного точки, при попытке запустить приложение на Блокнот электронная версия или Google без микрофон установлен.

В следующем примере демонстрируется запрос если микрофон доступен и если это не так, чтобы создать оповещение. Если микрофон не доступен на этом этапе вы бы выйти из действия либо отключить возможность записи речи.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Создание, назначение

Цель для системы речи использует определенный тип целей вызывается `RecognizerIntent`. Это назначение управляет большим числом параметров, включая продолжительность ожидать с бездействия запись считается через необходимые дополнительные языки для распознавания и вывода и текст для включения `Intent`в модальное диалоговое окно в качестве средства создания инструкции. В этом фрагменте кода `VOICE` — `readonly int` для распознавания в `OnActivityResult`.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Преобразование речи

Текст, полученный из речи доставляются в пределах `Intent`, который возвращается, когда действие завершения и возможен только через `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Будет возвращен `IList<string>`, из которого индекс можно использовать и отображать, в зависимости от количества языков, запрашиваемый в назначение вызывающего объекта (и указано в `RecognizerIntent.ExtraMaxResults`). Как с другим списком, стоит проверки убедитесь, что данные будут отображаться.

При прослушивании возвращаемое значение `StartActivityForResult`, `OnActivityResult` метод имеет поставка ожидается.

В следующем примере `textBox` — `TextBox` используется для выхода, что были предложения. Также может использоваться для передачи в некоторую форму интерпретатора и из него текст, приложение можно сравнить текст и ветви в другую часть приложения.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
             if (matches.Count != 0)
             {
                  string textInput = textBox.Text + matches[0];
                  textBox.Text = textInput;
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>Преобразование текста в речь

Преобразование текста в речь не довольно обратное речи в текст и основывается на два ключевых компонента; Модуль чтения текста вслух устанавливаются на устройстве, устанавливаемого языка.

Главным образом, Android устройства поставляются со значением по умолчанию установлена служба Google TTS и хотя бы один язык. Это устанавливается, когда устройство сначала настроить и будет основываться на где устройства во время (например, телефон в Германии установит немецкий язык, тогда как один Америки будет иметь американского английского).

### <a name="step-1---instantiating-texttospeech"></a>Шаг 1 - при создании TextToSpeech

`TextToSpeech` может принимать до трех параметров, первые два являются обязательными, а третий дополнительными (`AppContext`, `IOnInitListener`, `engine`). Прослушиватель используется для привязки к службе и проверка на ошибки с помощью подсистемы любое количество ядер, доступных Android текста в речь идет. Как минимум устройство будет иметь Google собственный модуль.

### <a name="step-2---finding-the-languages-available"></a>Шаг 2 - поиск доступных языков

`Java.Util.Locale` Класс содержит полезные метод с именем `GetAvailableLocales()`. Этот список языков, поддерживаемых речи впоследствии можно будет проверить от установленных языков.

Его несложно создать список языков «понятны». Всегда будет язык по умолчанию (язык пользователя задать при их сначала настроить свое устройство), поэтому в этом примере `List<string>` имеет в качестве первого параметра «Default», в зависимости от того, результат будет заполнено оставшейся части списка `textToSpeech.IsLanguageAvailable(locale)`.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

Этот код вызывает [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) для проверки, если языковой пакет для заданного языкового стандарта уже присутствует на устройстве. Этот метод возвращает [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), указывающее, доступен ли переданный язык. Если `LanguageAvailableResult` указывает, что язык `NotSupported`, отсутствует пакет нет голоса (даже для загрузки) для этого языка. Если `LanguageAvailableResult` равно `MissingData`, а затем можно загрузить новый языковой пакет, как описано ниже в шаге 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Шаг 3. Установка скорости и наклон

Android позволяет пользователю изменить звуковой речи, изменение `SpeechRate` и `Pitch` (коэффициент скорости и тон речи). Это переходит от 0 до 1, с «normal» речи, 1 для обоих.

### <a name="step-4---testing-and-loading-new-languages"></a>Шаг 4. тестирование и загрузке новых языков

Загрузка нового языка выполняется с помощью `Intent`. Это назначение приводит [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) вызываемого метода. В отличие от примера речи в текст (который используется [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) как `PutExtra` параметр `Intent`), тестирования и загрузке `Intent`являются `Action`-на основе:

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; запускает действие из платформы `TextToSpeech` механизм для проверки правильности установки и доступность языковые ресурсы на устройстве.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; запускает действие, которое предлагает пользователю загрузить необходимые языки.

В следующем примере кода показано, как использовать эти действия для тестирования языковых ресурсов и загрузки нового языка.

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` проверяет доступность языковые ресурсы. `OnActivityResult` вызывается после завершения этого теста. Если языковые ресурсы необходимо загрузить, `OnActivityResult` запускает `TextToSpeech.Engine.ActionInstallTtsData` действия, чтобы запустить действие, которое позволяет пользователю загрузить необходимые языки. Обратите внимание, это `OnActivityResult` реализации не проверяет `Result` кода, так как в этом упрощенном примере определение уже выполнен языкового пакета необходимо загрузить.

`TextToSpeech.Engine.ActionInstallTtsData` Причины действие **Google TTS голосовых данных** действия должны быть представлены пользователю для выбора языков для загрузки:

![Google TTS голосовых данных действия](speech-images/01-google-tts-voice-data.png)

В качестве примера пользователь может выбрать французский и щелкните значок загрузки для загрузки французский голосовых данных:

![Пример загрузки французского языка](speech-images/02-selecting-french.png)

Установка этих данных происходит автоматически после завершения загрузки.


### <a name="step-5---the-ioninitlistener"></a>Шаг 5 - IOnInitListener

Для действия иметь возможность преобразования текста в речь, метод интерфейса `OnInit` должен быть реализован (это второй параметр, указанный для создания экземпляра `TextToSpeech` класса). Инициализирует прослушиватель оно проверяет результат.

Прослушивателю необходимо протестировать для обеих `OperationResult.Success` и `OperationResult.Failure` как минимум.
В следующем примере показано, именно это:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Сводка

В этом руководстве мы рассматривали основные преобразования текста в речь и речи в текст и способов их включения в собственных приложениях. Хотя они не охватывают каждом конкретном случае, вы добавили основные интерпретацию речи, установить новые языки и увеличить inclusivity приложений.



## <a name="related-links"></a>Связанные ссылки

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Преобразование текста в речь (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Речи в текст (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Пространство имен Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Пространство имен Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
