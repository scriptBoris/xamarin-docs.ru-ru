---
title: Общие сведения о DependencyService
description: В этой статье объясняется, как класс Xamarin.Forms DependencyService обеспечивает доступ к собственным возможностям платформы.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 8f32255b6451b5b672293c8db42bb8b1ab38a7fd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061842"
---
# <a name="introduction-to-dependencyservice"></a>Общие сведения о DependencyService

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

## <a name="overview"></a>Обзор

[`DependencyService`](xref:Xamarin.Forms.DependencyService) позволяет приложениям обращаться к функциям конкретной платформы из общего кода. Благодаря этому приложения Xamarin.Forms могут делать все, что может делать собственное приложение.

`DependencyService` представляет собой указатель служб. На практике определяется интерфейс, а класс `DependencyService` находит подходящую реализацию этого интерфейса в проектах для разных платформ.

> [!NOTE]
> По умолчанию [`DependencyService`](xref:Xamarin.Forms.DependencyService) разрешает только зависящие от платформы реализации с конструкторами без параметров. Однако в Xamarin.Forms можно внедрить метод разрешения зависимостей, который разрешает зависящие от платформы реализации с помощью контейнера внедрения зависимостей или фабричных методов. Такой подход позволяет разрешать зависящие от платформы реализации, имеющие конструкторы с параметрами. Дополнительные сведения см. в статье [Разрешение зависимостей в Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Принципы работы DependencyService

Для использования `DependencyService` приложениям Xamarin.Forms требуются четыре компонента.

- **Интерфейс.** Требуемые функциональные возможности определяются в общем коде в виде интерфейса.
- **Реализация для платформы**. Классы, реализующие интерфейс, необходимо добавить в каждый проект платформы.
- **Регистрация**. Каждый реализующий класс должен быть зарегистрирован в `DependencyService` с помощью атрибута метаданных. Регистрация позволяет `DependencyService` находить реализующий класс и подставлять его вместо интерфейса во время выполнения.
- **Вызов DependencyService**. В общем коде должен явно вызываться класс `DependencyService` для запроса реализаций интерфейса.

Обратите внимание на то, что в решении должны быть предоставлены реализации для каждого проекта платформы. Если проект платформы не содержит реализации, во время выполнения произойдет ошибка.

Структура приложения представлена на следующей схеме:

![](introduction-images/overview-diagram.png "Структура приложения DependencyService")

### <a name="interface"></a>Интерфейс

Разрабатываемый интерфейс будет определять способ взаимодействия с зависящей от платформы функциональностью. Если вы разрабатываете компонент, который будет использоваться совместно как компонент или как пакет NuGet, будьте осторожны. Особенности интерфейса API могут нарушить работу пакета. В приведенном ниже примере определяется простой интерфейс для проговаривания текста. Он позволяет указывать проговариваемые слова и в то же время настраивать реализацию для каждой платформы.

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Реализация для платформ

Спроектировав интерфейс, его необходимо реализовать в проекте для каждой целевой платформы. Например, следующий класс реализует интерфейс `ITextToSpeech` в iOS:

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

Каждую реализацию интерфейса необходимо зарегистрировать в `DependencyService` с помощью атрибута метаданных. В следующем коде регистрируется реализация для iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

В целом реализация для конкретной платформы выглядит так:

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

Обратите внимание на то, что регистрация выполняется на уровне пространства имен, а не класса.

#### <a name="universal-windows-platform-net-native-compilation"></a>Компиляция .NET Native для универсальной платформы Windows

При инициализации Xamarin.Forms в проектах UWP, компилируемых посредством .NET Native, должна использоваться [немного другая конфигурация](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception). Кроме того, при использовании компиляции .NET Native регистрация зависимых служб должна производиться немного иначе.

В файле **App.xaml.cs** вручную зарегистрируйте каждую зависимую службу, определенную в проекте UWP, с помощью метода `Register<T>`, как показано ниже.

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Имейте в виду, что регистрация вручную с помощью метода `Register<T>` действует только в сборках выпуска с компиляцией .NET Native. Если опустить эту строку, отладочные сборки будут работать, но сборкам выпуска не удастся загрузить зависимую службу.

### <a name="call-to-dependencyservice"></a>Вызов DependencyService

После того как в проекте настроен общий интерфейс и реализации для каждой платформы, используйте `DependencyService` для получения подходящей реализации во время выполнения:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` находит подходящую реализацию интерфейса `T`.

### <a name="solution-structure"></a>Структура решения

Ниже приведен [пример решения UsingDependencyService](https://developer.xamarin.com/samples/UsingDependencyService/) для iOS и Android, в котором выделены описанные выше изменения кода.

 [![Решение для iOS и Android](introduction-images/solution-sml.png "Структура примера решения DependencyService")](introduction-images/solution.png#lightbox "Структура примера решения DependencyService")

> [!NOTE]
> Реализацию **необходимо** предоставить в проекте для каждой платформы. Если реализация интерфейса не зарегистрирована, классу `DependencyService` не удастся разрешить метод `Get<T>()` во время выполнения.

## <a name="related-links"></a>Связанные ссылки

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
