---
title: Android речи
description: В этой статье рассматриваются основные принципы использования очень мощный Android.Speech пространства имен. С момента ее создания Android не смог распознать речь и выводят их в виде текста. Это относительно простой процесс. Для преобразования текста в речь, тем не менее, процесс будет более сложной, а не просто речи должны приниматься во внимание, но также языков, доступных и установленных в системе преобразования текста в речь (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: e88f6e24cbf4c8b2f0c0486c6408e234e87066cc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104353"
---
# <a name="android-speech"></a>Android речи

_В этой статье рассматриваются основные принципы использования очень мощный Android.Speech пространства имен. С момента ее создания Android не смог распознать речь и выводят их в виде текста. Это относительно простой процесс. Для преобразования текста в речь, тем не менее, процесс будет более сложной, а не просто речи должны приниматься во внимание, но также языков, доступных и установленных в системе преобразования текста в речь (TTS)._

## <a name="speech-overview"></a>Общие сведения о речи

Составление системы, который «понимает» телефонных разговоров и enunciates, что такое типизированные — для преобразования речи в текст и текста в речь — является постоянно растущим область в разработки мобильных приложений, как растет потребность в обычных средств общения с нашей устройствами. Во многих случаях предложений where having это функция, которая преобразует текст в речь, или наоборот, — это очень полезное средство, чтобы включить в приложение android.

Например с clamp вниз при использовании мобильного телефона, снижая, пользователи хотят руки бесплатный способ работы своих устройств. Множество различных Android форм-факторов, таких как Android Wear — и включение постоянно расширяющуюся из них может использовать устройства Android (такие как планшеты и дополняет Примечание), создала большего размера фокус в отличных приложений TTS.

Google предоставляет разработчику широкий набор интерфейсов API в пространстве имен Android.Speech для большинства экземпляров включения «речь виду» (например, программное обеспечение, разработанное for the blind).  Пространство имен включает средство, позволяющее текст преобразован в речи через `Android.Speech.Tts`, контроль над ядро, используемое для выполнения преобразования, а также ряд `RecognizerIntent`s, которые позволяют им речи, преобразуемое в текст.

Хотя средства существуют для распознавания речи быть понятным, существуют ограничения, зависящие от оборудования, используемого. Маловероятно, что устройство успешно интерпретирует все, что слышит каждого из доступных языков.

## <a name="requirements"></a>Требования

Существуют нет особых требований, в этом руководстве, отличный от необходимости микрофона и выступает с докладами устройства.

Устройства Android интерпретации речи лежит использование `Intent` с соответствующими `OnActivityResult`.
Очень важно, однако для распознавания речи непонятным, но интерпретированных к тексту. Различие очень важно.

### <a name="the-difference-between-understanding-and-interpreting"></a>Разница между понимание и интерпретации

Простое определение понимания является возможность определить, тон и контекста сказанного подлинное значение. Для интерпретации просто означает, что слова и вывода их в другой форме.

Рассмотрим следующий простой пример, используемый в повседневной диалога: 

<kbd>Привет как дела?</kbd>

Без перегиба (акцент делался на конкретных слов или части слов) это простой вопрос. Тем не менее если большие интервалы применяется к строке, человек, который ожидает передачи данных обнаружит, что спрашивающим не слишком довольны и возможно потребуется была или что спрашивающим unwell. Если акцент делается на «are», обычно больше интересует ответ запросом пользователя.

Без звука довольно мощным инструментом, обработку, чтобы использовать перегиба и степень искусственного интеллекта (ИИ), чтобы понять контекст, программное обеспечение не может начинать, чтобы понять, что было сказано, наиболее простой телефоне можно сделать является преобразование речи в текст.

## <a name="setting-up"></a>Настройка

Прежде чем использовать системы распознавания речи, всегда разумно установите флажок, чтобы убедиться, что устройство имеет микрофона. Бы особого смысла при попытке запуска приложения в Блокнот Kindle или Google без него установлены.

Следующий пример кода демонстрирует запрос при наличии микрофона и если это не так, чтобы создать оповещение. Если микрофон не доступен на этом этапе вы бы либо закройте действия или отключить возможность записи речи.

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

### <a name="creating-the-intent"></a>Создание цели

Цель для системы распознавания речи использует определенный тип намерение вызывается `RecognizerIntent`. Этого намерения управляет большим числом параметров, включая как долго ждать с бездействия, пока запись считается за любые дополнительные языки для распознавания и выход и любой текст, включаемых в `Intent`в модальное диалоговое окно, что означает, что инструкции. В этом фрагменте кода `VOICE` — `readonly int` для распознавания в `OnActivityResult`.

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

Текст, интерпретированную из речи, которые будут предоставляться в `Intent`, который возвращается, когда действие завершения и производится через `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Эта команда возвращает `IList<string>`, из которого индекс можно использовать и отображать, в зависимости от количества языков, запрашиваемый в намерения абонента (и указано в `RecognizerIntent.ExtraMaxResults`). Так как с любой список, это стоит посмотреть, чтобы было достаточно данных для отображения.

При прослушивании возвращаемое значение `StartActivityForResult`, `OnActivityResult` метод имеет могут передаваться.

В следующем примере `textBox` является `TextBox` используется для выхода, что были диктовки. Столь же может использоваться для передачи в некоторую форму интерпретатора и из него текст, приложение можно сравнить текст и ветви в другую часть приложения.

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
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>Преобразование текста в речь

Преобразование текста в речь, не является довольно обратное речи в текст и полагается на два ключевых компонента; модуль преобразования текста в речь, устанавливается на устройстве и язык.

Главным образом, устройства Android имеют со значением по умолчанию установлены службы преобразования текста в РЕЧЬ Google и хотя бы один язык. Это определяются, когда устройство предварительно настроен и будет основываться на где находится устройство во время (например, телефоне в Германии установит немецкого языка, тогда как в Америке будет иметь американский английский).

### <a name="step-1---instantiating-texttospeech"></a>Шаг 1 - TextToSpeech создания экземпляров

`TextToSpeech` может принимать до трех параметров, первые два являются обязательными, а третий дополнительными (`AppContext`, `IOnInitListener`, `engine`). Прослушиватель используется для привязки к службе и тестирования для сбоя с помощью механизма, что любое количество ядер, доступных Android текста в речь. Как минимум устройство будет иметь Google собственный обработчик.

### <a name="step-2---finding-the-languages-available"></a>Шаг 2 - поиск доступных языков

`Java.Util.Locale` Класс содержит полезные метод, вызванный `GetAvailableLocales()`. Этот список языков, поддерживаемых распознавания речи можно затем протестировать установленные языки.

Это очень просто для создания списка языков «понятны». Всегда будет язык по умолчанию (язык пользователя, которые сначала настраивают устройства), поэтому в этом примере `List<string>` имеет в качестве первого параметра «Default», в оставшейся части списка заполняются в зависимости от результата `textToSpeech.IsLanguageAvailable(locale)`.

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

Этот код вызывает [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) для проверки, если языковой пакет для заданного языкового стандарта уже присутствует на устройстве. Этот метод возвращает [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), который указывает, доступен ли переданный язык. Если `LanguageAvailableResult` указывает, что язык `NotSupported`, то есть пакет не голоса (даже для загрузки) для этого языка. Если `LanguageAvailableResult` присваивается `MissingData`, то может возникнуть ситуация скачать новый языковой пакет, как описано ниже в шаге 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Шаг 3 - Установка скорости и шаг

Android позволяет пользователю изменить звук речи путем изменения `SpeechRate` и `Pitch` (частота скорость и тон речи). Это имеет значения от 0 до 1, с «обычными» распознавания речи, 1 для обоих.

### <a name="step-4---testing-and-loading-new-languages"></a>Шаг 4 - тестирование и загрузки новых языков

Загрузка нового языка выполняется с помощью `Intent`. Результат этого намерения вызывает [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) вызываемого метода. В отличие от примера речи в текст (который используется [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) как `PutExtra` параметр `Intent`), тестирования и загрузке `Intent`являются `Action`-на основе:

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; запускает действие из платформы `TextToSpeech` ядра для проверки правильности установки и доступность языковых ресурсов на устройстве.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; запускает действие, пользователю будет предложено загрузить необходимые языки.

В следующем примере кода показано, как использовать эти действия для тестирования языковых ресурсов и загрузить новый язык.

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

`TextToSpeech.Engine.ActionCheckTtsData` проверяет доступность языковых ресурсов. `OnActivityResult` вызывается после завершения этого теста. Если языковые ресурсы необходимо загрузить, `OnActivityResult` запускает `TextToSpeech.Engine.ActionInstallTtsData` запускать действие, которое позволяет пользователям скачивать необходимые языки. Обратите внимание, это `OnActivityResult` реализации не проверяет `Result` кода, потому что в этом упрощенном примере определение уже выполнен языкового пакета необходимо загрузить.

`TextToSpeech.Engine.ActionInstallTtsData` Причины действие **преобразования текста в РЕЧЬ Google голосовые данные** действия должны быть представлены пользователю для выбора языков для скачивания:

![Преобразования текста в РЕЧЬ Google голосовых данных действия](speech-images/01-google-tts-voice-data.png)

Например пользователь может выберите французский и щелкните значок скачивания, чтобы скачать французский голосовых данных:

![Пример скачивания французского языка](speech-images/02-selecting-french.png)

Установка этих данных выполняется автоматически после завершения загрузки.


### <a name="step-5---the-ioninitlistener"></a>Шаг 5. IOnInitListener

Для действия иметь возможность преобразования текста в речь, метод интерфейса `OnInit` должен быть реализован (это второй параметр, указанный для экземпляра `TextToSpeech` класса). Это инициализирует прослушиватель и проверяет результат.

Прослушиватель должен проверять на наличие обоих `OperationResult.Success` и `OperationResult.Failure` как минимум.
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

В этом руководстве мы рассматривали основные преобразования текста в речь и речи в текст и возможных методов включения их в собственных приложениях. Хотя они не охватывают каждом конкретном случае, вы добавили основные интерпретацию речи, как установить новые языки и как повысить inclusivity приложений.



## <a name="related-links"></a>Связанные ссылки

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Преобразование текста в речь (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Преобразование речи в текст (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Пространство имен Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Пространство имен Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
