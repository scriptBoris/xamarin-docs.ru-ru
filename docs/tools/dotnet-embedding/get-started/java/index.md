---
title: Приступая к работе с Java
description: Этот документ описывает, как приступить к работе с внедрения .NET с помощью Java. Оно описывает требования к системе для установки и поддерживаемые платформы.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 53871a5311cdba824b0bddca37dd5c416d06e272
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066813"
---
# <a name="getting-started-with-java"></a>Приступая к работе с Java

Это странице началу работы для Java, в котором описываются основы работы для всех поддерживаемых платформ.

## <a name="requirements"></a>Требования

Использование внедрения .NET с помощью Java, вам потребуется:

* Java 1.8 или более поздней версии
* [Моно 5.0](http://www.mono-project.com/download/)

Для Mac:

* Xcode 8.3.2 или более поздней версии

Для Windows:

* Visual Studio 2017 г. с поддержкой C++
* Windows 10 SDK

Для Android:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) или более поздней версии
* [Android Studio 3.x](https://developer.android.com/studio/index.html) с Java 1.8

Можно использовать [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) для редактирования и компиляции кода C#.

> [!NOTE]
> Более ранних версий Xcode, Visual Studio, Xamarin.Android, Android Studio и Mono _могут_ работать, но проверялась и не поддерживается.

## <a name="installation"></a>Установка

Внедрение .NET в настоящее время доступна в [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Это будет помещать **Embeddinator 4000.exe** в **пакетов/Embeddinator-4000/tools** каталога.

Кроме того, сборки .NET, внедрение из источника, см. наш [репозитории](https://github.com/mono/Embeddinator-4000/) и [дополнение](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) документа инструкции.

## <a name="platforms"></a>Платформы

Java в настоящее время находится в состоянии предварительной версии для Windows, Android и macOS.

Выбрана платформа, передав `--platform=<platform>` аргумент командной строки для средства внедрения .NET. В настоящее время `macOS`, `Windows`, и `Android` поддерживаются.

### <a name="macos-and-windows"></a>macOS и Windows

Для разработки можно использовать любой IDE Java, который поддерживает Java 1.8. Android Studio можно использовать даже для этого при необходимости [см. Здесь](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Выходной файл JAR-ФАЙЛ можно использовать, как и любой стандартной Java jar-файл.

### <a name="android"></a>Android

Убедитесь, что вы уже настроенные для разработки приложений Android перед попыткой создать его с помощью внедрения .NET. [Инструкциям](~/tools/dotnet-embedding/get-started/java/android.md) предполагается, что вы уже выполнил успешное создания и развертывания приложения с компьютера.

Android Studio рекомендуется для разработки приложений, но других интегрированных средах разработки должна работать, пока отсутствует поддержка [формат файла AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Дополнительные сведения

* [Приступая к работе в Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Обратные вызовы на Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Предварительное исследование Android](~/tools/dotnet-embedding/android/index.md)
* [Ограничения внедрения .NET](~/tools/dotnet-embedding/limitations.md)
* [Вклад в проект с открытым исходным кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
