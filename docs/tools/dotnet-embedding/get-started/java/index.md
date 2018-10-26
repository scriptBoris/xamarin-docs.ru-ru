---
title: Приступая к работе с Java
description: В этом документе описывается, как приступить к использованию внедрение .NET с помощью Java. В нем описывается требования к системе, установки и поддерживаемых платформ.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: 74146378d2ee534b357abd73ff7e1563a49a7e4a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121754"
---
# <a name="getting-started-with-java"></a>Приступая к работе с Java

Это начальной странице начала работы для Java, которые описаны основы для всех поддерживаемых платформ.

## <a name="requirements"></a>Требования

Чтобы использовать внедрение .NET с помощью Java, вам потребуется:

* Java 1.8 или более поздней версии
* [Mono 5.0](http://www.mono-project.com/download/)

Для Mac:

* Xcode 8.3.2 или более поздней версии

Для Windows:

* Visual Studio 2017 с поддержкой C++
* Windows 10 SDK

Для Android:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) или более поздней версии
* [Android Studio 3.x](https://developer.android.com/studio/index.html) с Java 1.8

Можно использовать [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) для редактирования и компиляции вашей C# кода.

> [!NOTE]
> Более ранних версиях Xcode, Visual Studio, Xamarin.Android, Android Studio и Mono _может_ работать, но проверялась и не поддерживается.

## <a name="installation"></a>Установка

Внедрение .NET в настоящее время доступна в [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Это переведет **Embeddinator 4000.exe** в **пакетов/Embeddinator-4000/tools** каталога.

Кроме того, можно построить внедрение .NET из источника, см. в разделе наших [репозиторий git](https://github.com/mono/Embeddinator-4000/) и [участвующая](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) документа инструкции.

## <a name="platforms"></a>Платформы

Java в настоящее время находится в состоянии предварительной версии для macOS, Windows и Android.

Платформа выбирается путем передачи `--platform=<platform>` аргумент командной строки для средства внедрение .NET. В настоящее время `macOS`, `Windows`, и `Android` поддерживаются.

### <a name="macos-and-windows"></a>macOS и Windows

Для разработки можно использовать любой интегрированной среде разработки Java, который поддерживает Java 1.8. Для этого можно использовать даже Android Studio при необходимости [см. в разделе ниже](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Выходной файл JAR-ФАЙЛ можно использовать как любой стандартный файл JAR-файл Java.

### <a name="android"></a>Android

Убедитесь, что вы уже настроенные разработка приложений, прежде чем пытаться создать ее с помощью внедрение .NET. [Инструкциями](~/tools/dotnet-embedding/get-started/java/android.md) предполагается, что вы уже успешно создали и развернули приложение Android с компьютера.

Android Studio рекомендуется для разработки приложений, но другие интегрированные среды разработки должно работать до тех пор, пока имеется поддержка [формат файла AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Дополнительные сведения

* [Начало работы в Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Обратные вызовы на устройстве Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Предварительное исследование Android](~/tools/dotnet-embedding/android/index.md)
* [Ограничения внедрение .NET](~/tools/dotnet-embedding/limitations.md)
* [Вносящая вклад в проект с открытым кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)
