---
title: .NET, внедрение в Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 5c8d493bf54ee1a8a1e7d4b3266451c78a4aa51e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123691"
---
# <a name="net-embedding-on-android"></a>.NET, внедрение в Android

В некоторых случаях может потребоваться добавить в существующий проект Android native библиотеку Xamarin .NET. Чтобы сделать это, можно использовать [Embeddinator до 4000](https://www.nuget.org/packages/Embeddinator-4000/) средство, чтобы превратить библиотеку .NET в собственной библиотеки, могут быть включены в собственное приложение Android на основе Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Требования к Xamarin.Android

Для Xamarin.Android для работы с внедрение .NET вам потребуется следующее:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) должен быть установлен.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) должен быть установлен.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) должен быть установлен.


## <a name="using-embeddinator-4000"></a>С помощью Embeddinator до 4000

Чтобы использовать библиотеку .NET в собственный проект Android, сделайте следующее:

1.  Создание C# проект библиотеки Android.

2.  Установка [Embeddinator до 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Найдите **Embeddinator 4000.exe** и добавьте его в ваш **путь**. Пример:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Запустите Embeddinator до 4000 на сборку библиотеки. Пример:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Используйте созданный файл AAR в проекте Java в Android Studio.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Требования к Xamarin.Android

Для Xamarin.Android для работы с внедрение .NET вам потребуется следующее:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) должен быть установлен.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) должен быть установлен.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) должен быть установлен.

-   **Mono** &ndash; [Mono 5.0](http://www.mono-project.com/download/) должен быть установлен (mono устанавливается вместе с Visual Studio для Mac).


## <a name="using-embeddinator-4000"></a>С помощью Embeddinator до 4000

Чтобы использовать библиотеку .NET в собственный проект Android, сделайте следующее:

1.  Создание C# проект библиотеки Android.

2.  Установка [Embeddinator до 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Найдите **Embeddinator 4000.exe** и добавьте **mono** в путь. Пример:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Запустите Embeddinator до 4000 на сборку библиотеки. Пример:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Используйте созданный файл AAR в проекте Java в Android Studio.

-----

Варианты использования и командной строки описаны в [Embeddinator до 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) документации.


## <a name="callbacks"></a>Обратные вызовы

Дополнительные сведения о [вызовы между C# и Java](callbacks.md).

## <a name="samples"></a>Примеры

* [Образец приложения прогнозов погоды](https://github.com/jamesmontemagno/embeddinator-weather)
