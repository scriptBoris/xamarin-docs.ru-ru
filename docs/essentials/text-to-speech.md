---
title: 'Xamarin.Essentials: преобразование текста в речь'
description: Класс TextToSpeech в Xamarin.Essentials позволяет приложению использовать встроенные преобразования текста в речь обработчиков для произнесения текста назад с устройства, а также для запроса доступных языков, поддерживающих ядро.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 9383411074bc43af1034138aadbb6ac5494c2c01
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815665"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: преобразование текста в речь

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**TextToSpeech** класс позволяет приложению использовать встроенные преобразования текста в речь обработчиков для произнесения текста назад с устройства, а также для запроса доступных языков, поддерживающих ядро.

## <a name="using-text-to-speech"></a>С помощью преобразования текста в речь

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Функциональные возможности преобразования текста в речь работает путем вызова `SpeakAsync` метод с текстом и необязательных параметров и возвращает после завершения utterance. 

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) => 
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

Этот метод принимает необязательный токен CancellationToken остановить utterance сразу после запуска. 
```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

Преобразование текста в речь автоматически помещаются в очередь запросы речи из одного потока. 

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>Параметры речи

Для большего контроля над как произнесении аудио, предоставляя `SpeakSettings` , позволяет тома, Прецессии и собственного языкового стандарта.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Ниже приведены поддерживаемые значения для этих параметров.

| Параметр | Минимум | Максимум |
| --- | :---: | :---: |
| Шаг | 0 | 2.0 |
| Тома | 0 | 1.0 |

### <a name="speech-locales"></a>Национальные настройки распознавания речи

Каждая платформа предлагает языковых стандартов для произнесения назад текст на нескольких языках и диакритические знаки. Каждая платформа имеет различные коды и способы указания, поэтому Essentials предоставляет кросс платформенные `Locale` класс и предоставляет способ запросить их с помощью `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Ограничения

- При вызове в нескольких потоках, utterance очереди не гарантируется.
- Воспроизведение звука в фоновом режиме не поддерживается официально.

## <a name="api"></a>API

- [TextToSpeech исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Документация по TextToSpeech API](xref:Xamarin.Essentials.TextToSpeech)
