---
title: 'Xamarin.Essentials: преобразование текста в речь'
description: Класс TextToSpeech в Xamarin.Essentials позволяет приложению использовать встроенные механизмы преобразования текста в речь, чтобы озвучивать текст на устройстве, а также запрашивать доступные языки, которые может поддерживать подсистема.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 08/30/2018
ms.openlocfilehash: 5b0d6bb2ab2621de5322a13b3b0e56e5bc6e93d8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123587"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: преобразование текста в речь

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **TextToSpeech** позволяет приложению использовать встроенные механизмы преобразования текста в речь, чтобы озвучивать текст на устройстве, а также запрашивать доступные языки, которые может поддерживать подсистема.

## <a name="using-text-to-speech"></a>Использование преобразования текста в речь

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

При преобразовании текста в речь вызывается метод `SpeakAsync` с текстом и дополнительными параметрами и выполняется возврат после завершения фрагмента речи.

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

Этот метод принимает необязательный параметр `CancellationToken`, чтобы остановить высказывание после его запуска.

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

Функция преобразования текста в речь автоматически ставит в очередь запросы на озвучивание речи из одного и того же потока.

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

### <a name="speech-settings"></a>Настройки речи

Для более высокого уровня контроля над воспроизведением звука применяется параметр `SpeakSettings`, который позволяет установить громкость, высоту тона и языковой стандарт.

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

Ниже приведены поддерживаемые значения этих параметров.

| Параметр | Минимум | Максимум |
| --- | :---: | :---: |
| Высота тона | 0 | 2.0 |
| Громкость | 0 | 1.0 |

### <a name="speech-locales"></a>Языковой стандарт

Каждая платформа поддерживает разные языковые стандарты, чтобы применять разные языки и акценты. Платформы используют разные коды и способы указания языкового стандарта. Поэтому Xamarin.Essentials предоставляет кроссплатформенный класс `Locale` и способ запросить их с помощью `GetLocalesAsync`.

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

- Порядок очереди запросов на озвучивание не гарантируется при вызове нескольких потоков.
- Воспроизведение фонового звука официально не поддерживается.

## <a name="api"></a>API

- [Исходный код TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Документация по API TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
