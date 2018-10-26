---
title: Android аудио
description: ОС Android предоставляет расширенную поддержку мультимедиа, которая объединяет в себе аудио и видео. В этом руководстве рассматриваются аудио в Android и охватывает воспроизводить и записывать звук, с помощью встроенных аудио-плейер и классов средства записи, а также API низкого уровня аудио. Он также рассматривается работа с аудио события широковещательных другими приложениями, таким образом, разработчики могут создавать правильно работающие приложения.
ms.prod: xamarin
ms.assetid: 646ED563-C34E-256D-4B56-29EE99881C27
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 9b7e9354250881074fc6f0db5d97dc83e4d3fa77
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114669"
---
# <a name="android-audio"></a>Android аудио

_ОС Android предоставляет расширенную поддержку мультимедиа, которая объединяет в себе аудио и видео. В этом руководстве рассматриваются аудио в Android и охватывает воспроизводить и записывать звук, с помощью встроенных аудио-плейер и классов средства записи, а также API низкого уровня аудио. Он также рассматривается работа с аудио события широковещательных другими приложениями, таким образом, разработчики могут создавать правильно работающие приложения._


## <a name="overview"></a>Обзор

Современные мобильные устройства, принявших функциональные возможности, ранее потребовало бы выделенных частей оборудования &ndash; камер, музыкальные проигрыватели и видеомагнитофоны. По этой причине мультимедиа платформ стали первостепенного компонента в мобильные интерфейсы API.

Android предоставляет расширенную поддержку мультимедиа. В этой статье рассматриваются работа с аудио в Android и рассматриваются следующие темы

1.  **Воспроизведение звука MediaPlayer** &ndash; с помощью встроенной `MediaPlayer` класс для воспроизведения аудио, включая локальные файлы аудио и потоковых звуковых файлов с `AudioTrack` класса.

2.  **Запись звука** &ndash; с помощью встроенной `MediaRecorder` класс записывать звук.

3.  **Работа с аудио уведомлениями** &ndash; использование звуковые уведомления для создания хорошо работающие приложения, которые правильно реагировать на события (такие как входящие телефонные вызовы) путем его приостановки или Отмена их звуковые выходные данные.

4.  **Работа с аудио низкоуровневая** &ndash; воспроизведение звука с помощью `AudioTrack` класса, записав буферов памяти. Запись звука с помощью `AudioRecord` класса и чтение непосредственно из буферов памяти.


## <a name="requirements"></a>Требования

С этим руководством требуется Android 2.0 (уровень API 5) или более поздней версии. Обратите внимание на то, что отладку звука на устройстве Android необходимо выполнять на устройстве.

Это необходимо для запроса `RECORD_AUDIO` разрешения в **AndroidManifest.XML**:

![Необходимые разрешения раздел манифеста Android с ЗАПИСЬЮ\_АУДИО включена](android-audio-images/image01.png)



## <a name="playing-audio-with-the-mediaplayer-class"></a>Воспроизведение звука с помощью класса MediaPlayer

Самый простой способ воспроизведение звука в Android — со встроенной [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) класса.
`MediaPlayer` можно воспроизвести локальных или удаленных файлов, передав путь к файлу. Тем не менее `MediaPlayer` очень состояние символов и вызова одного из его методов в неверном состоянии приведет к возникновению исключения. Очень важно для взаимодействия с `MediaPlayer` в порядке, описанных ниже, чтобы избежать ошибок.



### <a name="initializing-and-playing"></a>Инициализация и воспроизведение

Воспроизведение звука с `MediaPlayer` требуется следующая последовательность:

1. Создайте новый экземпляр [MediaPlayer](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/) объекта.

1. Настройте файл для воспроизведения через [SetDataSource](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.SetDataSource/p/Java.IO.FileDescriptor/) метод.

1. Вызовите [Подготовка](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Prepare/) метод для инициализации проигрывателя.

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

Воспроизведение может быть приостановлено путем вызова [Пауза](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Pause%28%29/) метод:

```csharp
player.Pause();
```

Чтобы возобновить работу приостановленной воспроизведения, вызовите [запустить](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Start%28%29/) метод.
Это будет возобновлена с приостановленной расположение во время воспроизведения:

```csharp
player.Start();
```

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Stop%28%29/) метод игрок завершает текущие воспроизведения:

```csharp
player.Stop();
```

Когда игрок не нужны, необходимо ли освободить ресурсы путем вызова [выпуска](https://developer.xamarin.com/api/member/Android.Media.MediaPlayer.Release%28%29/) метод:

```csharp
player.Release();
```



## <a name="using-the-mediarecorder-class-to-record-audio"></a>С помощью класса MediaRecorder запись аудио

Следствие `MediaPlayer` для записи звука в Android — [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) класса. Как и `MediaPlayer`, его состояние символов и переходит через несколько состояний, чтобы добраться до момента, когда они начинают записи. Для записи звука, `RECORD_AUDIO` разрешение должно быть задано. Инструкции по настройке приложения разрешения см. в разделе [работа с AndroidManifest.xml](~/android/platform/android-manifest.md).


### <a name="initializing-and-recording"></a>Инициализация и записи

Запись звука с `MediaRecorder` необходимо выполнить следующие действия:

1. Создайте новый экземпляр [MediaRecorder](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/) объекта.

2. Укажите, какое устройство, чтобы использовать для записи звукового ввода через [SetAudioSource](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioSource/p/Android.Media.AudioSource/) метод.

3. Установка звуковой формат файла выходных данных с помощью [SetOutputFormat](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFormat/p/Android.Media.OutputFormat/) метод. Список поддерживаемых типов аудио см. в разделе [Android поддерживаемых форматов медиафайлов](http://developer.android.com/guide/appendix/media-formats.html).

4. Вызовите [SetAudioEncoder](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetAudioEncoder/p/Android.Media.AudioEncoder/) метод, чтобы задать тип кодирования аудио.

5. Вызовите [SetOutputFile](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.SetOutputFile/p/System.String/) метод, чтобы задать имя выходного файла, который записывается звуковых данных.

6. Вызовите [Подготовка](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Prepare%28%29/) метод для инициализации средства записи.

7. Вызовите [запустить](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Start%28%29/) метод, чтобы начать запись.


В следующем образце кода показана последовательность:

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

Один раз `MediaRecorder` была остановлена, вызовите [Сброс](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Reset%28%29/) метод, чтобы поместить его обратно в состояние бездействия:

```csharp
recorder.Reset();
```

Когда `MediaRecorder` — больше не нужен, его ресурсы должны быть освобождены, вызвав [выпуска](https://developer.xamarin.com/api/member/Android.Media.MediaRecorder.Release%28%29/) метод:

```csharp
recorder.Release();
```


## <a name="managing-audio-notifications"></a>Управление звуковые уведомления



### <a name="the-audiomanager-class"></a>Класс AudioManager

[AudioManager](https://developer.xamarin.com/api/type/Android.Media.AudioManager/) класс предоставляет доступ к звуковые уведомления, которые позволяют приложениям при возникновении событий аудио. Эта служба также предоставляет доступ к другим аудио функции, такие как режим тома и звонка. `AudioManager` Позволяет приложению обрабатывать звуковые уведомления для управления воспроизведения звука.



### <a name="managing-audio-focus"></a>Управление фокусом аудио

Звуковые ресурсы устройства (встроенный проигрыватель и записи) являются общими для всех выполняющихся приложений.

По сути, это похоже на приложения на настольном компьютере, где находится фокус клавиатуры, только одно приложение: после щелчком мыши его, выбрав один из запущенных приложений, ввод с клавиатуры поступает только к этому приложению.

Аудио фокус аналогичные идеей и предотвращает более одного приложения из воспроизведения или записи звука в то же время. Это сложнее, чем фокус клавиатуры, так как он является добровольным &ndash; приложения можно проигнорировать этот факт, что он в настоящее время не имеет аудио фокуса и воспроизведение в любом случае &ndash; и так как существуют различные типы звуковых фокуса, который может быть запрошено. Например если только запрашивающая сторона должна воспроизведение звука в течение очень короткого времени, может запросить временных фокус.

Аудио фокус может удовлетворить немедленно, или изначально запрещен и предоставлены более поздней версии. Например если приложение запросы аудио фокус во время звонка, будут отклонены, но фокус также может быть предоставлена, после завершения звонка. Таким образом чтобы реагировать соответствующим образом, если отзываются аудио фокус регистрируется прослушиватель. Запрос аудио фокус используется для определения того, является ли это хорошо для воспроизведения или записывать звук.

Дополнительные сведения о звуковых фокус, см. в разделе [Управление аудио фокус](http://developer.android.com/training/managing-audio/audio-focus.html).



#### <a name="registering-the-callback-for-audio-focus"></a>Регистрация обратного вызова для аудио фокуса

Регистрация `FocusChangeListener` обратного вызова из `IOnAudioChangeListener` является важной частью получения и освобождения аудио фокус. Это обусловлено предоставление аудио фокуса может быть отложена до более позднее время. Например приложение может запросить воспроизведение музыки во время телефонного звонка. Не следует предоставлять аудио фокус, пока не будет завершено телефонный звонок.

По этой причине объект обратного вызова передается как параметр в `GetAudioFocus` метод `AudioManager`, и это именно тот вызов, регистрирует обратный вызов. Если фокус аудио изначально запрещен, но позже предоставлен, приложение уведомляется путем вызова `OnAudioFocusChange` при обратном вызове. Тот же метод позволяет сообщить приложению, что сейчас переводится фокус аудио.

Когда приложение завершило использование аудио ресурсов, он вызывает `AbandonFocus` метод `AudioManager`и снова передает в обратном вызове. Это отменяет регистрацию обратного вызова и освобождает ресурсы, аудио, другие приложения могут получать фокус аудио.



#### <a name="requesting-audio-focus"></a>Запрашивающий аудио фокус

Шаги, необходимые для запроса звуковые ресурсы устройства совпадают с приведенными ниже:

1.  Получить дескриптор `AudioManager` системной службы.

2.  Создайте экземпляр класса обратного вызова.

3.  Запросить звуковые ресурсы устройства путем вызова `RequestAudioFocus` метод `AudioManager` . Параметры являются объект обратного вызова, тип потока (музыки, голосовой вызов, кольцо и т.д.) и тип прямо запрошенный уровень доступа (звуковые ресурсы могут запрашиваться сразу же или в течение неопределенного периода, например).

4.  Если предоставляется запрос, `playMusic` немедленно вызывается метод, и начинает воспроизведение звукового файла.

5.  Если запрос отклоняется, никакие дополнительные действия не выполняются. В этом случае аудио воспроизводится только при отсутствии ответа на запрос позже.


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

После завершения воспроизведения дорожки `AbandonFocus` метод `AudioManager` вызывается. Это позволяет другое приложение для получения звуковые ресурсы устройства. Другие приложения, получите уведомление этого изменения фокус аудио, если они зарегистрировали свои собственные прослушиватели.


## <a name="low-level-audio-api"></a>Низкий уровень API аудио

Низкоуровневые API аудио предоставляют больший контроль над аудио воспроизводить и записывать, так как они взаимодействуют непосредственно с буферов памяти, вместо того чтобы использовать URI файлов. Существуют некоторые сценарии, где этот подход является предпочтительным. Эти сценарии включают:

1.  При воспроизведении из зашифрованного звуковые файлы.

2.  При воспроизведении последовательность коротких клипов.

3.  Потоковая передача аудио.


### <a name="audiotrack-class"></a>Класс AudioTrack

[AudioTrack](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/) класс использует API низкого уровня аудио для записи и низкоуровневые эквивалент `MediaPlayer` класса.


#### <a name="initializing-and-playing"></a>Инициализация и воспроизведение

Для воспроизведения аудио, новый экземпляр класса `AudioTrack` должен быть создан. Список аргументов переданные [конструктор](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/#memberlist) задает способ воспроизведения аудио образца, содержащихся в буфере. Аргументы являются:

1.  Тип Stream &ndash; голоса, мелодию звонка, музыка, системы или оповещения.

2.  Частота &ndash; частоту выборки, выраженное в Гц.

3.  Канал конфигурации &ndash; Mono или стерео.

4.  Звуковой формат &ndash; 8-разрядное или 16-разрядная кодировка.

5.  Размер буфера &ndash; в байтах.

6.  Режим буфера &ndash; потоковой передачи или статический.


После создания экземпляра [воспроизведение](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Play%28%29/) метод `AudioTrack` вызывается, чтобы настроить его для начала воспроизведения. Запись звука буфера `AudioTrack` начинается воспроизведение:

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

Вызовите [приостановить](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Pause%28%29/) метод для приостановки воспроизведения:

```csharp
audioTrack.Pause();
```

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Stop%28%29/) метода будет окончательно прекратить воспроизведения:

```csharp
audioTrack.Stop();
```


#### <a name="cleanup"></a>Очистка

Когда `AudioTrack` — больше не нужен, его ресурсы должны быть освобождены, вызвав [выпуска](https://developer.xamarin.com/api/member/Android.Media.AudioTrack.Release%28%29/):

```csharp
audioTrack.Release();
```


### <a name="the-audiorecord-class"></a>Класс AudioRecord

[AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) класс является эквивалентом `AudioTrack` на стороне записи. Как и `AudioTrack`, она использует буферы памяти напрямую, вместо файлов и URI. Для нее необходимо выполнение `RECORD_AUDIO` задать разрешения в манифесте.


#### <a name="initializing-and-recording"></a>Инициализация и записи

Первым шагом является создание нового [AudioRecord](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/) объекта. Список аргументов переданные [конструктор](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/#memberlist) содержит все сведения, необходимые для записи. В отличие от в `AudioTrack`, где аргументы являются главным образом перечислений, эквивалентные аргументы в `AudioRecord` являются целыми числами. Сюда входит следующее.

1.  Оборудование входной аудиоисточник например "микрофон".

2.  Тип Stream &ndash; голоса, мелодию звонка, музыка, системы или оповещения.

3.  Частота &ndash; частоту выборки, выраженное в Гц.

4.  Канал конфигурации &ndash; Mono или стерео.

5.  Звуковой формат &ndash; 8-разрядное или 16-разрядная кодировка.

6.  Буфер размер в байтах


Один раз `AudioRecord` создан, его [StartRecording](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.StartRecording%28%29/) вызывается метод. Теперь она готова начать запись. `AudioRecord` Постоянно считывает аудио буфер для входа и записывает это входные данные звуковой файл.

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


#### <a name="stopping-the-recording"></a>Остановить запись

Вызов [остановить](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Stop%28%29/) метод завершает запись:

```csharp
audRecorder.Stop();
```


#### <a name="cleanup"></a>Очистка

Когда `AudioRecord` объект больше не нужен, вызов его [выпуска](https://developer.xamarin.com/api/member/Android.Media.AudioRecord.Release%28%29/) метод освобождает все ресурсы, связанные с ним:

```csharp
audRecorder.Release();
```


## <a name="summary"></a>Сводка

ОС Android предоставляет мощную платформу для воспроизведения, запись и управление ими аудио. В этой статье рассматриваются как воспроизводить и записывать звук с помощью высокоуровневым `MediaPlayer` и `MediaRecorder` классы. Затем продемонстрировано, как использовать звуковые уведомления для совместного использования ресурсов, звуковые устройства между различными приложениями. Наконец он обработаны как воспроизведение и записывать звук, используя интерфейсы API низкого уровня, который взаимодействовать непосредственно с буферов памяти.


## <a name="related-links"></a>Связанные ссылки

- [Работа с аудио (пример)](https://developer.xamarin.com/samples/Example_WorkingWithAudio/)
- [Проигрыватель мультимедиа](https://developer.xamarin.com/api/type/Android.Media.MediaPlayer/)
- [Средство записи мультимедиа](https://developer.xamarin.com/api/type/Android.Media.MediaRecorder/)
- [Система управления звуком](https://developer.xamarin.com/api/type/Android.Media.AudioManager/)
- [Звуковая дорожка](https://developer.xamarin.com/api/type/Android.Media.AudioTrack/)
- [Средство записи звука](https://developer.xamarin.com/api/type/Android.Media.AudioRecord/)
