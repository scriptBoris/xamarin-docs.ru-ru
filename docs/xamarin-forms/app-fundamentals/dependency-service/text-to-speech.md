---
title: Реализация преобразования текста в речь
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для вызова в собственном API преобразования текста в речь каждой платформы.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6d1948214b97a1b536b07b6420c32e4d27124518
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "38997547"
---
# <a name="implementing-text-to-speech"></a>Реализация преобразования текста в речь

В этой статье поможет вам при создании кроссплатформенного приложения, который использует [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) обратиться к собственным API преобразования текста в речь:

- **[Создание интерфейса](#Creating_the_Interface)**  &ndash; понять, как интерфейс создается в общем коде.
- **[iOS реализация](#iOS_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для iOS.
- **[Android реализации](#Android_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для Android.
- **[Реализация UWP](#WindowsImplementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)**  &ndash; сведения об использовании `DependencyService` выполнить вызов собственной реализации из общего кода.

Приложение с помощью `DependencyService` будет иметь следующую структуру:

![](text-to-speech-images/tts-diagram.png "Структура приложений DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Во-первых необходимо создаете интерфейс в общем коде, который выражает функцию, которую планируется реализовать. В этом примере интерфейс содержит один метод `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Создания кода для этого интерфейса в общем коде позволит приложению доступ к API-интерфейсы речи на каждой платформе Xamarin.Forms.

> [!NOTE]
> Классы, реализующие интерфейс должен иметь конструктор без параметров для работы с `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация iOS

Интерфейс должен быть реализован в каждом проекте приложения для конкретной платформы. Обратите внимание, что класс имеет конструктор без параметров, чтобы `DependencyService` можно создавать новые экземпляры.

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

`[assembly]` Атрибут регистрирует класс как реализация `ITextToSpeech` интерфейс, который означает, что `DependencyService.Get<ITextToSpeech>()` может использоваться в общем коде, чтобы создать его экземпляр.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация Android

Код для Android сложнее, чем версия iOS: требует реализации класса для наследования от конкретных Android `Java.Lang.Object` и реализовать `IOnInitListener` также интерфейс. Он также требуется доступ к текущему контексту Android, который предоставляется командлетом `MainActivity.Instance` свойство.

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

`[assembly]` Атрибут регистрирует класс как реализация `ITextToSpeech` интерфейс, который означает, что `DependencyService.Get<ITextToSpeech>()` может использоваться в общем коде, чтобы создать его экземпляр.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Реализация платформы универсальной Windows

Универсальная платформа Windows имеет API распознавания речи в `Windows.Media.SpeechSynthesis` пространства имен. Единственная загвоздка состоит в том, обязательно установите **"микрофон"** возможность в манифест, в противном случае доступ к речи, API-интерфейсы, блокируются.

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

`[assembly]` Атрибут регистрирует класс как реализация `ITextToSpeech` интерфейс, который означает, что `DependencyService.Get<ITextToSpeech>()` может использоваться в общем коде, чтобы создать его экземпляр.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь мы можно разработать и протестировать общий код, который обращается к интерфейсу преобразования текста в речь. Это простая страница имеется кнопка, которая активирует функцию распознавания речи. Она использует `DependencyService` для получения экземпляра `ITextToSpeech` интерфейс &ndash; во время выполнения этот экземпляр будет реализацию платформы, которая имеет полный доступ к собственным пакетом SDK для.

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

Выполнение этого приложения в iOS, Android и UWP, а затем нажмите кнопку приведет к приложения, обращаться к вам с помощью собственного speech SDK на каждой платформе.

 ![iOS и Android озвучивания текста кнопки](text-to-speech-images/running.png "пример преобразования текста в речь")


## <a name="related-links"></a>Связанные ссылки

- [С помощью DependencyService (пример)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)

