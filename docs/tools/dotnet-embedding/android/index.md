---
title: Внедрение в Android .NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067399"
---
# <a name="net-embedding-on-android"></a>Внедрение в Android .NET

В некоторых случаях может потребоваться добавить в существующий проект Android native библиотека Xamarin .NET. Чтобы сделать это, можно использовать [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) , которая позволяет преобразовать библиотеку .NET в собственной библиотеки, которая может быть включена в машинном коде на языке Java Android приложение.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Требования к Xamarin.Android

Для Xamarin.Android для работы с .NET внедрения необходимо следующее:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) должен быть установлен.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) должен быть установлен.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) должен быть установлен.


## <a name="using-embeddinator-4000"></a>С помощью Embeddinator 4000

Для использования библиотеки .NET в собственный проект Android, выполните следующие действия:

1.  Создание проекта библиотеки Android в C#.

2.  Установка [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Найдите **Embeddinator 4000.exe** и добавить его в ваш **путь**. Пример:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Запустите Embeddinator 4000 на сборку библиотеки. Пример:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Используйте созданный файл AAR в проекте Java в Android Studio.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Требования к Xamarin.Android

Для Xamarin.Android для работы с .NET внедрения необходимо следующее:

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) должен быть установлен.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) должен быть установлен.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) должен быть установлен.

-   **Моно** &ndash; [моно 5.0](http://www.mono-project.com/download/) должен быть установлен (моно устанавливается вместе с Visual Studio для Mac).


## <a name="using-embeddinator-4000"></a>С помощью Embeddinator 4000

Для использования библиотеки .NET в собственный проект Android, выполните следующие действия:

1.  Создание проекта библиотеки Android в C#.

2.  Установка [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Найдите **Embeddinator 4000.exe** и добавьте **моно** в нужный путь. Пример:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Запустите Embeddinator 4000 на сборку библиотеки. Пример:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Используйте созданный файл AAR в проекте Java в Android Studio.

-----

Варианты использования и программы командной строки описаны в [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) документации.


## <a name="callbacks"></a>Обратные вызовы

Дополнительные сведения о [вызовы между C# и Java](callbacks.md).

## <a name="samples"></a>Примеры

* [Образец прогноз погоды](https://github.com/jamesmontemagno/embeddinator-weather)
