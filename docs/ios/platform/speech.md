---
title: Распознавание речи в Xamarin.iOS
description: В этой статье представлен новый API распознавания речи и показано, как реализовать его в приложение Xamarin.iOS, поддерживающие распознавание речи для непрерывной и транскрипция речи (из прямые или записанные аудиопотоков) в текст.
ms.prod: xamarin
ms.assetid: 64FED50A-6A28-4833-BEAE-63CEC9A09010
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: c1f488213f9b3be945fd98e09f630c243d0b0d62
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122768"
---
# <a name="speech-recognition-in-xamarinios"></a>Распознавание речи в Xamarin.iOS

_В этой статье представлен новый API распознавания речи и показано, как реализовать его в приложение Xamarin.iOS, поддерживающие распознавание речи для непрерывной и транскрипция речи (из прямые или записанные аудиопотоков) в текст._

Новое в iOS 10, Apple имеет выпуска API распознавания речи, который позволяет приложению iOS поддерживает распознавание речи для непрерывной и транскрипция речи (из прямые или записанные аудиопотоков) в текст.

В соответствии с Apple API распознавания речи имеет следующие возможности и преимущества:

- Высокоточные
- Современные
- Простой в использовании
- Быстрый
- Поддерживает несколько языков
- Конфиденциальность пользователей отношениях

## <a name="how-speech-recognition-works"></a>Как работает распознавание речи

Распознавание речи реализуется в приложении iOS получение предварительно записанные или динамической аудио (в любой из языков, поддерживаемых API) и ее передачи в распознаватель речи, который возвращает транскрипция обычного текста в речь.

[![](speech-images/speech01.png "Как работает распознавание речи")](speech-images/speech01.png#lightbox)

### <a name="keyboard-dictation"></a>Клавиатуру с диктовкой

Когда большинство пользователей думаю распознавания речи на устройстве iOS, они думают встроенные голосовой помощник Siri, который был выпущен вместе с клавиатуру с диктовкой в iOS 5 с iPhone 4S.

Клавиатуру с диктовкой поддерживается любой элемент интерфейса, который поддерживает TextKit (такие как `UITextField` или `UITextArea`) и активируется пользователем кнопки диктовки (непосредственно слева от пробела) в виртуальной клавиатуры iOS.

Apple выпустила следующие статистические данные клавиатуру с диктовкой (собранные с момента 2011 г.):

- Клавиатуру с диктовкой широко использовался с момента его выпуска в iOS 5.
- Примерно через 65 000 приложений, использующих его в день.
- О трети всех iOS диктовки выполняется стороннее приложение.

Клавиатуру с диктовкой исключительно прост в использовании, так как требует никаких усилий со стороны разработчика, кроме подхода с применением элемент интерфейса TextKit при разработке пользовательского интерфейса приложения. Преимущество клавиатуру с диктовкой также не требует запросы специальные права из приложения, прежде чем можно будет использовать.

Приложения, использующие новые интерфейсы API распознавания речи потребует специальных разрешений, должны быть предоставлены пользователем, поскольку требуется распознавания речи, передачи и временного хранения данных на серверах компании Apple. См. в нашем [безопасности и конфиденциальности улучшения](~/ios/app-fundamentals/security-privacy.md) Дополнительные сведения см.

Несмотря на то легко реализовать клавиатуру с диктовкой, он иметь несколько ограничений и недостатки:

- Требуется использование текстового поля ввода и отображения клавиатуры.
- Она работает с звук в реальном времени, только для входных данных и приложение не может управлять процессом записи звука.
- Он предоставляет не контролирует язык, который используется для интерпретации речи пользователя.
- Нет способа для приложения, чтобы узнать, если кнопка диктовки даже доступно для пользователя.
- Приложение не может настраивать процесс записи звука.
- Он предоставляет очень поверхностный набора результатов, не содержит информации, например о времени и надежности.

### <a name="speech-recognition-api"></a>API распознавания речи

Новое в iOS 10, Apple выпустила API распознавания речи, который обеспечивает более удобную для приложения iOS для реализации распознавания речи. Этот API аналогичен тому, используемый Apple для создания мощных Siri и клавиатуру с диктовкой и он способен предоставления быстрой транскрипцию современные точности.

Результаты в API распознавания речи прозрачно настраиваются для отдельных пользователей, без необходимости собирать или получить доступ к любой личных данных пользователей приложения.

API распознавания речи предоставляет результаты обратно к вызывающему приложению в практически в реальном времени, как говорит пользователь, и предоставляет дополнительные сведения о результатах перевода чем только текст. Сюда входит следующее.

- Различных интерпретаций произнесенную пользователем.
- Уровни надежности для отдельных переводы.
- Сведения о времени.

Как уже говорилось, аудио для перевода можно предоставить либо путем динамического канала, или из предварительно записанные источника и в любом из более чем 50 языков и диалекты, поддерживаемые iOS 10.

API распознавания речи можно использовать на любом устройстве iOS под управлением iOS 10 и в большинстве случаев требуется активное подключение к Интернету, так как основная часть переводы происходит на серверах компании Apple. С другой стороны, некоторые устройства поддерживают всегда на более новые iOS, перевод на устройстве определенных языков.

Apple включает в себя API доступности, чтобы определить, доступна ли данного языка для перевода в текущий момент. Приложение должно использовать этот API вместо проверки для подключения к Интернету, сам напрямую.

Как указано выше в разделе диктовки клавиатуры, распознавание речи требует передачи и временного хранения данных на серверах компании Apple, через Интернет и таким образом, приложение _необходимо_ запрашивать разрешение пользователя для выполнения распознавание, включив `NSSpeechRecognitionUsageDescription` в его `Info.plist` файл и вызывая метод `SFSpeechRecognizer.RequestAuthorization` метод. 

На основе источника звука, используемый для распознавания речи, другие изменения в приложение `Info.plist` может потребоваться файл. См. в нашем [безопасности и конфиденциальности улучшения](~/ios/app-fundamentals/security-privacy.md) Дополнительные сведения см.

## <a name="adopting-speech-recognition-in-an-app"></a>Внедрение распознавания речи в приложении

Существует четыре основных шагов, которые разработчик должен принимать внедрить распознавания речи в приложении iOS.

- Описание использования в приложении `Info.plist` файлов с помощью `NSSpeechRecognitionUsageDescription` ключ. Например, приложение камеры может включать следующее описание _«Это позволяет сделать фотографию просто произнеся слово «Мак».»_
- Запросить авторизацию путем вызова `SFSpeechRecognizer.RequestAuthorization` способ предоставления пояснения (в `NSSpeechRecognitionUsageDescription` выше разделе) почему приложение хочет речи распознавания доступ к пользователю в диалоговом окне и дают возможность принять или отклонить.
- Создание запроса распознавания речи:
    * Предварительно аудио на диске, используйте `SFSpeechURLRecognitionRequest` класса.
    * Звук в реальном времени (или аудио из памяти), используйте `SFSPeechAudioBufferRecognitionRequest` класса.
- Передать на запрос распознавания речи распознаватель речи (`SFSpeechRecognizer`) начинается распознавание. Приложения могут при необходимости удерживать возвращенного `SFSpeechRecognitionTask` для наблюдения за результаты распознавания.

Ниже подробно рассматриваются следующие действия.

### <a name="providing-a-usage-description"></a>Предоставляет описание использования

Для предоставления необходимых `NSSpeechRecognitionUsageDescription` в `Info.plist` файл, выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Дважды щелкните `Info.plist` файл, чтобы открыть его для редактирования.
2. Переключиться в режим **источника** представления: 

    [![](speech-images/speech02.png "В представлении источника")](speech-images/speech02.png#lightbox)
3. Щелкните **добавьте новую запись**, введите `NSSpeechRecognitionUsageDescription` для **свойство**, `String` для **тип** и **Описание использования** как **значение**. Пример: 

    [![](speech-images/speech03.png "Добавление NSSpeechRecognitionUsageDescription")](speech-images/speech03.png#lightbox)
4. Если приложение будет обрабатывать динамические расшифровка дикторского текста для аудио, также требуется Описание использования микрофона. Щелкните **добавьте новую запись**, введите `NSMicrophoneUsageDescription` для **свойство**, `String` для **тип** и **Описание использования** как **значение**. Пример: 

    [![](speech-images/speech04.png "Добавление NSMicrophoneUsageDescription")](speech-images/speech04.png#lightbox)
4. Сохраните изменения в файле.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Дважды щелкните `Info.plist` файл, чтобы открыть его для редактирования.
3. Щелкните **добавьте новую запись**, введите `NSSpeechRecognitionUsageDescription` для **свойство**, `String` для **тип** и **Описание использования** как **значение**. Пример: 

    [![](speech-images/speech03w.png "Добавление NSSpeechRecognitionUsageDescription")](speech-images/speech03w.png#lightbox)
4. Если приложение будет обрабатывать динамические расшифровка дикторского текста для аудио, также требуется Описание использования микрофона. Щелкните **добавьте новую запись**, введите `NSMicrophoneUsageDescription` для **свойство**, `String` для **тип** и **Описание использования** как **значение**. Пример: 

    [![](speech-images/speech04w.png "Добавление NSMicrophoneUsageDescription")](speech-images/speech04w.png#lightbox)
4. Сохраните изменения в файле.

-----

> [!IMPORTANT]
> Не удалось предоставить любой из этих `Info.plist` ключи (`NSSpeechRecognitionUsageDescription` или `NSMicrophoneUsageDescription`) можно привести приложения, завершается сбоем без предупреждения, при попытке доступа к распознавания речи или микрофон для звук в реальном времени.




### <a name="requesting-authorization"></a>Запрашивает авторизацию

Чтобы запросить авторизации пользователя, который позволяет приложению получить доступ к распознавания речи, измените основной класс контроллера представления и добавьте следующий код:

```csharp
using System;
using UIKit;
using Speech;

namespace MonkeyTalk
{
    public partial class ViewController : UIViewController
    {
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Request user authorization
            SFSpeechRecognizer.RequestAuthorization ((SFSpeechRecognizerAuthorizationStatus status) => {
                // Take action based on status
                switch (status) {
                case SFSpeechRecognizerAuthorizationStatus.Authorized:
                    // User has approved speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Denied:
                    // User has declined speech recognition
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.NotDetermined:
                    // Waiting on approval
                    ...
                    break;
                case SFSpeechRecognizerAuthorizationStatus.Restricted:
                    // The device is not permitted
                    ...
                    break;
                }
            });
        }
    }
}
```

`RequestAuthorization` Метод `SFSpeechRecognizer` класс будет запрашивать разрешение от пользователя для доступа распознавание речи, используя причину, по которой разработчик из `NSSpeechRecognitionUsageDescription` ключ `Info.plist` файл.

Объект `SFSpeechRecognizerAuthorizationStatus` результат возвращается `RequestAuthorization` процедура обратного вызова метода, который может использоваться для выполнения действий на основе пользователя разрешения. 

> [!IMPORTANT]
> Apple предлагает ждать, пока пользователь начал действие в приложении, которое требует распознавания речи, прежде чем запросить это разрешение.

### <a name="recognizing-pre-recorded-speech"></a>Распознавание речи предварительно записанные

Если приложение для распознавания речи из предварительно записанные в формате WAV или MP3-файл, его можно использовать следующий код:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
...

public void RecognizeFile (NSUrl url)
{
    // Access new recognizer
    var recognizer = new SFSpeechRecognizer ();

    // Is the default language supported?
    if (recognizer == null) {
        // No, return to caller
        return;
    }

    // Is recognition available?
    if (!recognizer.Available) {
        // No, return to caller
        return;
    }

    // Create recognition task and start recognition
    var request = new SFSpeechUrlRecognitionRequest (url);
    recognizer.GetRecognitionTask (request, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said, \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}
```

Просмотрев этот код, подробно, во-первых, он пытается создать распознаватель речи (`SFSpeechRecognizer`). Если язык по умолчанию не поддерживается для распознавания речи, `null` возвращается и выхода функции.

Если для языка по умолчанию в распознаватель речи, приложение проверяет, если он доступен в настоящее время для распознавания с помощью `Available` свойство. Например распознавание могут оказаться недоступными в том случае, если на устройстве нет подключения к Интернету.

Объект `SFSpeechUrlRecognitionRequest` создается на основе `NSUrl` расположение файла предварительно записанные на устройстве iOS и он передается в распознаватель речи для обработки с помощью процедура обратного вызова.

При вызове функции обратного вызова, если `NSError` не `null` ошибка, которая должна обрабатываться. Так как распознавание речи выполняется постепенно, процедура обратного вызова может быть вызван более чем один раз, поэтому `SFSpeechRecognitionResult.Final` свойство проверяется ли перевод завершена и стать наилучшей версией перевод записывается (`BestTranscription`).

### <a name="recognizing-live-speech"></a>Распознавание речи в режиме реального времени

Если приложение хочет распознать речь в реальном времени, это очень похоже на распознавание речи предварительно записанные. Пример:

```csharp
using System;
using UIKit;
using Speech;
using Foundation;
using AVFoundation;
...

#region Private Variables
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
#endregion
...

public void StartRecording ()
{
    // Setup audio session
    var node = AudioEngine.InputNode;
    var recordingFormat = node.GetBusOutputFormat (0);
    node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
        // Append buffer to recognition request
        LiveSpeechRequest.Append (buffer);
    });

    // Start recording
    AudioEngine.Prepare ();
    NSError error;
    AudioEngine.StartAndReturnError (out error);

    // Did recording start?
    if (error != null) {
        // Handle error and return
        ...
        return;
    }

    // Start recognition
    RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
        // Was there an error?
        if (err != null) {
            // Handle error
            ...
        } else {
            // Is this the final translation?
            if (result.Final) {
                Console.WriteLine ("You said \"{0}\".", result.BestTranscription.FormattedString);
            }
        }
    });
}

public void StopRecording ()
{
    AudioEngine.Stop ();
    LiveSpeechRequest.EndAudio ();
}

public void CancelRecording ()
{
    AudioEngine.Stop ();
    RecognitionTask.Cancel ();
}
```

Просмотрев этот код, подробно, он создает несколько частных переменных для обработки процесса распознавания:

```csharp
private AVAudioEngine AudioEngine = new AVAudioEngine ();
private SFSpeechRecognizer SpeechRecognizer = new SFSpeechRecognizer ();
private SFSpeechAudioBufferRecognitionRequest LiveSpeechRequest = new SFSpeechAudioBufferRecognitionRequest ();
private SFSpeechRecognitionTask RecognitionTask;
```

Она использует AV Foundation записывать звук, который будет передан `SFSpeechAudioBufferRecognitionRequest` для обработки запроса распознавания:

```csharp
var node = AudioEngine.InputNode;
var recordingFormat = node.GetBusOutputFormat (0);
node.InstallTapOnBus (0, 1024, recordingFormat, (AVAudioPcmBuffer buffer, AVAudioTime when) => {
    // Append buffer to recognition request
    LiveSpeechRequest.Append (buffer);
});
```

Приложение пытается получить начать запись и все ошибки обрабатываются в том случае, если запись не может быть запущена:

```csharp
AudioEngine.Prepare ();
NSError error;
AudioEngine.StartAndReturnError (out error);

// Did recording start?
if (error != null) {
    // Handle error and return
    ...
    return;
}
```

Распознавание задача запускается, а маркер хранится задаче распознавания (`SFSpeechRecognitionTask`):

```csharp
RecognitionTask = SpeechRecognizer.GetRecognitionTask (LiveSpeechRequest, (SFSpeechRecognitionResult result, NSError err) => {
    ...
});
```

Функция обратного вызова используется таким же образом, чтобы той, которая использовалась выше на предварительно записанные речи.

Если запись является обрезанный остановлен пользователем, сообщается как ядро аудио, так и на запрос распознавания речи:

```csharp
AudioEngine.Stop ();
LiveSpeechRequest.EndAudio ();
```

Если пользователь отменит распознавания, затем информируются модуля звукового и задаче распознавания:

```csharp
AudioEngine.Stop ();
RecognitionTask.Cancel ();
```

Очень важно для вызова `RecognitionTask.Cancel` Если пользователь отменяет преобразование для освобождения памяти и процессора устройства.

> [!IMPORTANT]
> Не удалось предоставить `NSSpeechRecognitionUsageDescription` или `NSMicrophoneUsageDescription` `Info.plist` ключи можно привести приложения, завершается сбоем без предупреждения, при попытке доступа к распознавания речи или микрофон для звук в реальном времени (`var node = AudioEngine.InputNode;`). См. в разделе **предоставляя Описание использования** Дополнительные сведения в разделе выше.

## <a name="speech-recognition-limits"></a>Ограничения распознавания речи

При работе с распознавания речи в приложении iOS, Apple накладывает следующие ограничения:

- Распознавание речи предоставляется бесплатно для всех приложений, но его использование не неограниченное:
    - Устройства iOS отдельных имеют ограниченное число отзывы, которые могут быть выполнены в день.
    - Приложения будет регулироваться глобально на основе запроса в день.
- Приложения должны быть готовы обрабатывать распознавания речи сетевого подключения и сбои ограничение частоты использования.
- Распознавание речи может иметь высокую стоимость увеличится сетевой трафик и разряду батареи на устройстве iOS пользователя, из-за этого, Apple накладывает ограничение в строгом аудио длительность речи max примерно одна минута.

Если приложение регулярно достигнет предела регулирования частоты запросов, Apple запросом, что разработчик свяжитесь с ними.

## <a name="privacy-and-usability-considerations"></a>Рекомендации по удобство использования и конфиденциальности

Apple имеет следующие предложения для остается прозрачным и соблюдение конфиденциальности пользователей при включении распознавания речи в приложение iOS.

- При регистрации пользователя речи, убедитесь, что четко указать, что запись выполняется в пользовательском интерфейсе приложения. Например приложение может звук «запись» и отображать индикатор записи.
- Не используйте распознавания речи для конфиденциальные данные пользователя, такие как пароли, данные о работоспособности или финансовых данных.
- Показать результаты распознавания _перед_ обрабатывать их. Это не только предоставляет отзывы о том, что делает выполняет приложение, но пользователь может обрабатывать ошибки распознавания, как они были сделаны.

## <a name="summary"></a>Сводка

В этой статье имеет представлен новый API распознавания речи и показал, как реализовать его в приложение Xamarin.iOS, поддерживающие распознавание речи для непрерывной и транскрипция речи (из прямые или записанные аудиопотоков) в текст. 



## <a name="related-links"></a>Связанные ссылки

- [SpeakToMe (пример)](https://developer.xamarin.com/samples/monotouch/ios10/SpeakToMe/)
