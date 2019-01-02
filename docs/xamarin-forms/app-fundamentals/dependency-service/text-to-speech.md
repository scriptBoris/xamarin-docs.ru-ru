---
title: Реализация преобразования текста в речь
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для вызова собственного интерфейса API преобразования текста в речь каждой платформы.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 0351436259bb782e4f8e3a3405b9620c4e8b20bb
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050755"
---
# <a name="implementing-text-to-speech"></a>Реализация преобразования текста в речь

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

Эта статья поможет вам создать кроссплатформенное приложение, в котором [`DependencyService`](xref:Xamarin.Forms.DependencyService) используется для доступа к собственным интерфейсам API преобразования текста в речь:

- **[Создание интерфейса](#Creating_the_Interface)** &ndash; сведения о создании интерфейса в общем коде.
- **[Реализация в iOS](#iOS_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для iOS.
- **[Реализация в Android](#Android_Implementation)** &ndash; сведения о реализации интерфейса в машинном коде для Android.
- **[Реализация в UWP](#WindowsImplementation)** &ndash; сведения о реализации интерфейса в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)** &ndash; сведения об использовании `DependencyService` для вызова собственной реализации из общего кода.

У приложения, использующего `DependencyService`, будет следующая структура:

![](text-to-speech-images/tts-diagram.png "Структура приложения DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Сначала создайте в общем коде интерфейс для функциональности, которую вы планируете реализовать. В этом примере интерфейс содержит один метод `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Реализация этого интерфейса в общем коде позволит приложению Xamarin.Forms обращаться к речевым интерфейсам API на каждой платформе.

> [!NOTE]
> Для работы с `DependencyService` классы, реализующие интерфейс, должны иметь конструктор без параметров.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация в iOS

Интерфейс необходимо реализовать в проекте приложения для каждой платформы. Обратите внимание на то, что класс имеет конструктор без параметров. Это позволяет `DependencyService` создавать экземпляры.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

Атрибут `[assembly]` регистрирует экземпляры как реализацию интерфейса `ITextToSpeech`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<ITextToSpeech>()` в общем коде.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация в Android

Код для Android сложнее, чем для iOS: реализующий класс должен наследовать класс Android `Java.Lang.Object`, а также реализовывать интерфейс `IOnInitListener`. Кроме того, требуется доступ к текущему контексту Android, который предоставляется свойством `MainActivity.Instance`.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

Атрибут `[assembly]` регистрирует экземпляры как реализацию интерфейса `ITextToSpeech`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<ITextToSpeech>()` в общем коде.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация на универсальной платформе Windows

Универсальная платформа Windows имеет речевой интерфейс API в пространстве имен `Windows.Media.SpeechSynthesis`. Нужно лишь не забыть отметить возможность **Microphone** (Микрофон) в манифесте. В противном случае доступ к речевым интерфейсам API будет заблокирован.

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

Атрибут `[assembly]` регистрирует экземпляры как реализацию интерфейса `ITextToSpeech`. Это означает, что для создания его экземпляра можно использовать метод `DependencyService.Get<ITextToSpeech>()` в общем коде.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь можно написать и протестировать общий код, который обращается к интерфейсу преобразования текста в речь. На этой простой странице есть кнопка, которая активирует функцию преобразования в речь. Для получения экземпляра `ITextToSpeech` интерфейса &ndash; используется `DependencyService`. Во время выполнения этот экземпляр будет представлять собой зависящую от платформы реализацию с полным доступом к собственному пакету SDK.

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

Если запустить это приложение в iOS, Android или UWP и нажать кнопку, текст будет озвучен с помощью собственного пакета SDK соответствующей платформы.

 ![Кнопка преобразования текста в речь в iOS и Android](text-to-speech-images/running.png "Пример преобразования текста в речь")


## <a name="related-links"></a>Связанные ссылки

- [Использование DependencyService (пример)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
