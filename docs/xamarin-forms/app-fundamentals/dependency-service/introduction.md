---
title: Общие сведения о DependencyService
description: В этой статье объясняется, как класс Xamarin.Forms DependencyService работает для доступа к собственным возможностям платформы.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 28c6daa361b7de09a0d9332b21f1b6f75e035850
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "38995418"
---
# <a name="introduction-to-dependencyservice"></a>Общие сведения о DependencyService

## <a name="overview"></a>Обзор

[`DependencyService`](xref:Xamarin.Forms.DependencyService) позволяет приложениям обращаться к функциям конкретной платформы из общего кода. Эта функция позволяет приложениям Xamarin.Forms делают все, что можно сделать собственного приложения.

`DependencyService` является служба локатора. На практике, определяется интерфейс и `DependencyService` находит правильная реализация этого интерфейса в различных проектах платформы.

> [!NOTE]
> По умолчанию [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) будет только resolve реализаций платформы, которые имеют конструкторы без параметров. Тем не менее метод разрешения зависимостей могут внедряться в Xamarin.Forms, контейнер внедрения зависимостей или фабричные методы для разрешения реализации платформы. Этот подход можно использовать для разрешения реализации платформы, которые имеют конструкторы с параметрами. Дополнительные сведения см. в разделе [разрешение зависимостей в Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Как работает DependencyService

Приложения Xamarin.Forms должны использовать четыре компонента `DependencyService`:

- **Интерфейс** &ndash; требуемую функциональность определяется интерфейс, в общем коде.
- **Реализации на платформе** &ndash; классы, реализующие интерфейс необходимо добавить в каждый проект платформы.
- **Регистрация** &ndash; каждого реализующего класса должен быть зарегистрирован с `DependencyService` с помощью атрибута метаданных. Включение регистрации `DependencyService` найти реализации класса и предоставление его вместо интерфейса во время выполнения.
- **Вызов DependencyService** &ndash; общий код должен явно вызвать `DependencyService` попросить реализаций интерфейса.

Обратите внимание, что реализации необходимо указать для каждой платформы проекта в решении. Проекты платформы без реализации завершится ошибкой во время выполнения.

Структура приложения описан на следующей схеме:

![](introduction-images/overview-diagram.png "Структура приложений DependencyService")

### <a name="interface"></a>Интерфейс

Интерфейс, создаваемый вами определит, как взаимодействовать с функциям конкретной платформы. Будьте внимательны при разработке компонента в качестве компонента или пакета Nuget. Проектирование API можно сделать или прервать выполнение пакета. В приведенном ниже примере указывает простой интерфейс для говоря текст, который обеспечивает гибкость при определении слов, которые необходимо произнести, но оставляет реализация, которую нужно настроить для каждой платформы:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Реализация каждой платформы

После подходящий интерфейс был разработан, этот интерфейс должен быть реализован для каждой платформы, которая будет использоваться в проекте. Например, следующий класс реализует `ITextToSpeech` интерфейс в iOS:

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>Регистрация

Каждая реализация интерфейса должен быть зарегистрирован с `DependencyService` с помощью атрибута метаданных. Следующий код регистрирует реализацию для операций ввода-вывода:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Окончательная сборка, реализации платформы выглядит следующим образом:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

Примечание:, регистрация выполняется на уровне пространства имен, а не на уровне класса.

#### <a name="universal-windows-platform-net-native-compilation"></a>Универсальный Windows платформа .NET компиляция в собственном коде

Проекты универсальной платформы Windows, использующие параметр компиляции машинного кода .NET должны соответствовать [слегка](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) при инициализации Xamarin.Forms. Компиляции .NET native также требуется немного другой регистрации для службы зависимостей.

В **App.xaml.cs** файл, вручную зарегистрировать каждой службы зависимостей, определенные в проекте универсальной платформы Windows с помощью `Register<T>` метод, как показано ниже:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Примечание: с помощью ручной регистрации `Register<T>` является действует только в выпуске сборки с помощью компиляции .NET Native. Если опустить эту строку, отладочные сборки по-прежнему будет работать, но сборок выпуска не удастся загрузить службы зависимостей.

### <a name="call-to-dependencyservice"></a>Вызов DependencyService

Когда проект настроен с общий интерфейс и реализации для каждой платформы, использовать `DependencyService` для получения правой реализацию во время выполнения:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` найти правильный реализацию интерфейса `T`.

### <a name="solution-structure"></a>Структура решения

[Образец решения UsingDependencyService](https://developer.xamarin.com/samples/UsingDependencyService/) будет показано ниже, для iOS и Android, изменения кода, описанные выше выделена.

 [![iOS и Android решение](introduction-images/solution-sml.png "структура решения образца DependencyService")](introduction-images/solution.png#lightbox "DependencyService образец решения структуры")

> [!NOTE]
> Вы **необходимо** предоставлять реализацию в каждом проекте платформы. Если нет реализации интерфейса зарегистрирован, то `DependencyService` будет не удается разрешить `Get<T>()` метод во время выполнения.

## <a name="related-links"></a>Связанные ссылки

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
