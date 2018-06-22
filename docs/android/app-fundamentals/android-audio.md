---
title: Android аудио
description: Для ОС Android предоставляет широкие возможности для мультимедиа, включающая аудио и видео. В этом руководстве основное внимание уделяется звука в Android и рассматриваются воспроизводить и записывать звук, используя встроенный проигрыватель и записи классов, а также низкоуровневые звуковой API. Она также охватывает работе с событиями аудио широковещательных другими приложениями, чтобы разработчики могут создавать приложения правильно.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: aff0d67549707129bfc85246318c33c522e4f1f6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771799"
---
# <a name="android-audio"></a>Android аудио

_Для ОС Android предоставляет широкие возможности для мультимедиа, включающая аудио и видео. В этом руководстве основное внимание уделяется звука в Android и рассматриваются воспроизводить и записывать звук, используя встроенный проигрыватель и записи классов, а также низкоуровневые звуковой API. Она также охватывает работе с событиями аудио широковещательных другими приложениями, чтобы разработчики могут создавать приложения правильно._


## <a name="overview"></a>Обзор

Современных мобильных устройств, принявших функциональные возможности, ранее потребовалась бы выделенных частей оборудования &ndash; фотоаппараты, проигрывателей музыки и видео устройства записи. По этой причине платформы мультимедиа стали компонентом первого класса в API мобильных устройств.

Android предоставляет широкие возможности для мультимедиа. В этой статье проверяет работу со звуком в Android и рассматриваются следующие темы

1.  **Воспроизведение звука с MediaPlayer** &ndash; применения встроенного `MediaPlayer` класса для воспроизведения звука, включая локальные звуковые файлы и потоковых звуковых файлов с `AudioTrack` класса.

2.  **Запись звука** &ndash; применения встроенного `MediaRecorder` класс записывать звук.

3.  **Работа с аудио уведомления** &ndash; использование звуковых уведомлений для создания долгосрочных приложений, которые правильно реагировать на события (например, входящие звонки), приостановка или Отмена выходы аудио.

4.  **Работа с аудио нижнего уровня** &ndash; воспроизведение аудио с помощью `AudioTrack` класса, записав буферов памяти. Запись звука с помощью `AudioRecord` класса и чтение непосредственно из буферов памяти.


## <a name="requirements"></a>Требования

В этом руководстве требуется Android 2.0 (API уровня 5) или более поздней версии. Обратите внимание на то, что отладка звука на Android должна быть выполнена на устройстве.

Это необходимо для запроса `RECORD_AUDIO` разрешений в **AndroidManifest.XML**:

![Необходимые разрешения раздел манифеста Android с ЗАПИСЬЮ\_АУДИО включена](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Воспроизведение звука с классом MediaPlayer

Самый простой способ воспроизведение звука в Android — со встроенной [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) класса.
`MediaPlayer` можно воспроизводить локальных или удаленных файлов, передав ему путь к файлу. Тем не менее `MediaPlayer` очень учитывают состояние и вызова одного из его методов в неправильном состоянии приведет к созданию исключения. Очень важно для взаимодействия с `MediaPlayer` в порядке, описанные ниже, чтобы избежать ошибок.



### <a name="initializing-and-playing"></a>Инициализация и воспроизведения

Воспроизведение аудио с `MediaPlayer` требует следующую последовательность:

1. Создания нового экземпляра [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) объекта.

1. Настройка файла для воспроизведения через [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) метод.

1. Вызовите [Подготовка](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) метод для инициализации проигрыватель.

1. Вызовите [запустить](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start/) метод, чтобы начать воспроизведение звука.


В следующем примере демонстрируется использование этой привязки:

```csharp
protected MediaPlayer player;
public void StartPlayer(String  filePath)
{
  if (player == null) {
    player = new MediaPlayer();
  } else {
    player.Reset();
    player.SetDataSource(filePath);
    player.Prepare();
    player.Start();
  }
}
```


### <a name="suspending-and-resuming-playback"></a>Приостановка и возобновление воспроизведения

Воспроизведение может быть приостановлен путем вызова [Пауза](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) метод:

```csharp
player.Pause();
```

Чтобы возобновить воспроизведение приостановленной, вызовите [запустить](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) метод.
Это будет возобновлена с приостановленной расположение в воспроизведении:

```csharp
player.Start();
```

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) метод проигрыватель завершает текущее воспроизведения:

```csharp
player.Stop();
```

Когда игрок не нужны, ресурсы должны быть освобождены вызовом [выпуска](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) метод:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>С помощью класса MediaRecorder для записи звука

Следствием `MediaPlayer` для записи звука в Android [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) класса. Как `MediaPlayer`, его зависящих от состояния и переходы через несколько состояний, чтобы добраться до момента, где она может начать запись. Для записи звука, `RECORD_AUDIO` должно иметь значение. Инструкции по настройке приложения см. разрешения [работа с AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Инициализация и записи

Записи звука с `MediaRecorder` необходимо выполнить следующие действия:

1. Создания нового экземпляра [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) объекта.

2. Укажите, какое устройство, чтобы использовать для записи звука ввода с [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) метод.

3. Установка звуковой формат файла выходных данных с помощью [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) метод. Список поддерживаемых типов аудио см [Android поддерживается носителя форматирует](http://developer.android.com/guide/appendix/media-formats.html).

4. Вызовите [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) метод, чтобы задать тип кодирования аудио.

5. Вызовите [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) метод, чтобы указать имя выходного файла, который записывается звуковых данных.

6. Вызовите [Подготовка](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) метод для инициализации средства записи.

7. Вызовите [запустить](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) метод, чтобы начать запись.


В следующем образце кода показано следующее:

```csharp
protected MediaRecorder recorder;
void RecordAudio (String filePath)
{
  try {
    if (File.Exists (filePath)) {
      File.Delete (filePath);
    }
    if (recorder == null) {
      recorder = new MediaRecorder (); // Initial state.
    } else {
      recorder.Reset ();
      recorder.SetAudioSource (AudioSource.Mic);
      recorder.SetOutputFormat (OutputFormat.ThreeGpp);
      recorder.SetAudioEncoder (AudioEncoder.AmrNb);
      // Initialized state.
      recorder.SetOutputFile (filePath);
      // DataSourceConfigured state.
      recorder.Prepare (); // Prepared state
      recorder.Start (); // Recording state.
    }
  } catch (Exception ex) {
    Console.Out.WriteLine( ex.StackTrace);
  }
}
```


### <a name="stopping-recording"></a>Остановка записи

Чтобы остановить запись, вызовите `Stop` метод `MediaRecorder`:

```csharp
recorder.Stop();
```



### <a name="cleaning-up"></a>Очистка

Один раз `MediaRecorder` был остановлен, вызовите [Сброс](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) метод, чтобы переключить его обратно в состояние бездействия:

```csharp
recorder.Reset();
```

Когда `MediaRecorder` — больше не нужен, его ресурсы должны быть освобождены вызовом [выпуска](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) метод:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Управление звуковых уведомлений



### <a name="the-audiomanager-class"></a>Класс AudioManager

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) класс предоставляет доступ к аудио уведомлений, которые позволяют приложениям знал, когда выполнена звуковых событиях. Эта служба также предоставляет доступ к другим аудио функции, такие как управление режимом тома и звонка. `AudioManager` Позволяет приложению обрабатывать аудио уведомления для управления воспроизведения звука.



### <a name="managing-audio-focus"></a>Управление фокусом аудио

Звуковые ресурсы устройства (встроенный проигрыватель и записи) являются общими для всех выполняющихся приложений.

По сути, это аналогично приложений на настольном компьютере, где только одно приложение имеет фокус клавиатуры: после выбора одного из приложений, запущенных щелчком мыши-, ввод с клавиатуры отправляется только этому приложению.

Аудио фокус аналогичные идеи и предотвращает более одного приложения из воспроизведения или записи звука в то же время. Сложнее, чем фокус клавиатуры, так как он является добровольным &ndash; приложения можно пропустить этот факт, что он в настоящее время не имеет аудио фокуса и воспроизвести вне зависимости от &ndash; и так как существуют различные типы аудио фокус, которое может быть по запросу. Например если только инициатор запроса должен воспроизведение звука в течение очень короткого времени, может запросить временных фокус.

Аудио фокус могут немедленно, или изначально запрещен и предоставлены более поздней версии. Например если приложение запросы аудио фокус во время звонка, будет запрещен, но фокус также может быть предоставлена, после завершения телефонный звонок. Таким образом чтобы отреагировать соответствующим образом, если отзываются аудио фокус регистрируется прослушиватель. Запрашивает аудио фокус используется для определения того, является ли это ОК для воспроизведения или записи звука.

Дополнительные сведения о звуковых фокус в разделе [Управление аудио фокус](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Регистрация обратного вызова для аудио фокус

Регистрация `FocusChangeListener` обратного вызова из `IOnAudioChangeListener` является важной частью процесса получения и освобождения аудио фокус. Это так, как предоставление аудио фокуса может быть отложена на более поздний срок. Например приложение может запросить воспроизведение музыки во время звонка. Не удалось предоставить аудио фокус, до завершения телефонный звонок.

По этой причине объект обратного вызова, передается в качестве параметра в `GetAudioFocus` метод `AudioManager`, и именно этот вызов, который регистрирует обратный вызов. Если фокус аудио изначально запрещен, но предоставлено более поздней версии, приложение уведомляется путем вызова `OnAudioFocusChange` во время обратного вызова. Определить приложения, аудио фокус переводится сейчас используется тот же метод.

Когда приложение завершило с ресурсами, аудио, он вызывает `AbandonFocus` метод `AudioManager`и снова передается в обратный вызов. Это отменяет регистрацию обратного вызова и освобождает звуковые ресурсы, чтобы другие приложения могут получать фокус аудио.



#### <a name="requesting-audio-focus"></a>Запрашивает аудио фокус

Шаги, необходимые для запроса ресурсов, звуковые устройства может выглядеть следующим образом:

1.  Получение дескриптора `AudioManager` системной службы.

2.  Создайте экземпляр класса обратного вызова.

3.  Запрос звуковые ресурсы устройства путем вызова `RequestAudioFocus` метод `AudioManager` . Параметры — объект обратного вызова, тип потока (Музыка, голосового вызова, кольцевой и т.д.) и тип доступа, запрашиваемого вправо (звуковые ресурсы могут быть запрошены моментально или в течение неопределенного периода, например).

4.  Если запрос разрешен, `playMusic` немедленно вызывается метод и начинает воспроизведение звука.

5.  Если запрос отклоняется, не дальнейшие действия не выполняются. В этом случае звук воспроизводится только при отсутствии ответа на запрос позже.


В следующем примере показаны следующие действия:

```csharp
Boolean RequestAudioResources(INotificationReceiver parent)
{
  AudioManager audioMan = (AudioManager) GetSystemService(Context.AudioService);
  AudioManager.IOnAudioFocusChangeListener listener  = new MyAudioListener(this);
  var ret = audioMan.RequestAudioFocus (listener, Stream.Music, AudioFocus.Gain );
  if (ret == AudioFocusRequest.Granted) {
    playMusic();
    return (true);
  } else if (ret == AudioFocusRequest.Failed) {
    return (false);
  }
  return (false);
}
```


#### <a name="releasing-audio-focus"></a>Освобождение аудио фокус

После завершения воспроизведения записи `AbandonFocus` метод `AudioManager` вызывается. Это позволяет другим приложением для получения звуковые ресурсы устройства. Другие приложения будет отправлено уведомление этого изменения аудио фокус, если они зарегистрировали свои собственные прослушиватели.


## <a name="low-level-audio-api"></a>Низкий уровень аудио API

Низкоуровневые звуковой API обеспечивают больший контроль над аудио воспроизведения и записи, так как они взаимодействуют напрямую с буферов памяти, вместо использования файла идентификаторы URI. Существуют некоторые сценарии, где этот подход является предпочтительным. Такие сценарии:

1.  При воспроизведении из зашифрованных звуковые файлы.

2.  При воспроизведении последовательность коротких роликов.

3.  Потокового аудио.


### <a name="audiotrack-class"></a>Класс AudioTrack

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) класс использует низкоуровневые звуковой API для записи и низкоуровневые эквивалентно `MediaPlayer` класса.


#### <a name="initializing-and-playing"></a>Инициализация и воспроизведения

Для воспроизведения звука, новый экземпляр `AudioTrack` должен быть создан. Список аргументов переданные [конструктор](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) указывает способ воспроизведения звука образца, содержащегося в буфере. Аргументы являются:

1.  Тип потока &ndash; голоса, мелодии звонка, музыка, системы или звуковых сигналов.

2.  Частота &ndash; частоту выборки, выраженное в Гц.

3.  Конфигурация канала &ndash; моно или стерео.

4.  Звуковой формат &ndash; 8-разрядное или 16-разрядная кодировка.

5.  Размер буфера &ndash; в байтах.

6.  Режим буфера &ndash; потоковой передачи или статическим.


После создания экземпляра [воспроизведение](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) метод `AudioTrack` вызывается, чтобы установить его до начала воспроизведения. Запись звука буфера `AudioTrack` начинает воспроизведение:

```csharp
void PlayAudioTrack(byte[] audioBuffer)
{
  AudioTrack audioTrack = new AudioTrack(
    // Stream type
    Stream.Music,
    // Frequency
    11025,
    // Mono or stereo
    ChannelOut.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length,
    // Mode. Stream or static.
    AudioTrackMode.Stream);

    audioTrack.Play();
    audioTrack.Write(audioBuffer, 0, audioBuffer.Length);
}
```


#### <a name="pausing-and-stopping-the-playback"></a>Приостановка и остановка воспроизведения

Вызовите [приостановить](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) метод приостанавливает воспроизведение:

```csharp
audioTrack.Pause();
```

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) метод окончательно нарушит воспроизведения:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Очистка

Когда `AudioTrack` — больше не нужен, его ресурсы должны быть освобождены вызовом [выпуска](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>Класс AudioRecord

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) класса является эквивалентом `AudioTrack` на стороне записи. Как и `AudioTrack`, он использует буферы памяти напрямую, вместо файлов и URI. Он требует `RECORD_AUDIO` задать разрешения в манифест.


#### <a name="initializing-and-recording"></a>Инициализация и записи

Первым шагом является создание нового [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) объекта. Список аргументов переданные [конструктор](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) содержит все сведения, необходимые для записи. В отличие от в `AudioTrack`, где аргументы являются главным образом перечислений, эквивалентные аргументы в `AudioRecord` являются целыми числами. Сюда входит следующее.

1.  Оборудование аудио входного источника, например микрофона.

2.  Тип потока &ndash; голоса, мелодии звонка, музыка, системы или звуковых сигналов.

3.  Частота &ndash; частоту выборки, выраженное в Гц.

4.  Конфигурация канала &ndash; моно или стерео.

5.  Звуковой формат &ndash; 8-разрядное или 16-разрядная кодировка.

6.  Буфер размер в байтах


Один раз `AudioRecord` создан, его [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) вызывается метод. Теперь все готово для начала записи. `AudioRecord` Постоянно считывает аудио буфер для входа и записывает это входным для звукового файла.

```csharp
void RecordAudio()
{
  byte[] audioBuffer = new byte[100000];
  var audRecorder = new AudioRecord(
    // Hardware source of recording.
    AudioSource.Mic,
    // Frequency
    11025,
    // Mono or stereo
    ChannelIn.Mono,
    // Audio encoding
    Android.Media.Encoding.Pcm16bit,
    // Length of the audio clip.
    audioBuffer.Length
  );
  audRecorder.StartRecording();
  while (true) {
    try
    {
      // Keep reading the buffer while there is audio input.
      audRecorder.Read(audioBuffer, 0, audioBuffer.Length);
      // Write out the audio file.
    } catch (Exception ex) {
      Console.Out.WriteLine(ex.Message);
      break;
    }
  }
}
```


#### <a name="stopping-the-recording"></a>Остановка записи

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) метод завершает записи:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Очистка

Когда `AudioRecord` объект больше не нужен, вызов его [выпуска](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) метод освобождает все ресурсы, связанные с ним:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Сводка

Для ОС Android предоставляет мощная платформа для воспроизведения, записи и управления аудио. В этой статье рассматривается как воспроизводить и записывать звук с помощью высокоуровневые `MediaPlayer` и `MediaRecorder` классы. Затем он анализировать использование звуковых уведомлений для общего доступа к ресурсам, звуковые устройства между приложениями. Наконец он обрабатываются как для воспроизведения и записи звука с помощью API нижнего уровня, который взаимодействовать непосредственно с буферов памяти.


## <a name="related-links"></a>Связанные ссылки

- [Работа с аудио (пример)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Проигрыватель](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Средство записи носителя](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Система управления звуком](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Звуковой дорожки](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Средство записи звука](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
