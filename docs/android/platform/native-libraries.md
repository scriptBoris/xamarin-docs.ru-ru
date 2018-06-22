---
title: С помощью собственных библиотек
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 0fa66f3a16047c18af19cb7257c778b498bc0c9b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774815"
---
# <a name="using-native-libraries"></a>С помощью собственных библиотек

Xamarin.Android поддерживает использование собственных библиотек через стандартный механизм PInvoke. Также можно связать дополнительные собственные библиотеки, которые не являются частью операционной системы в вашей .apk.

Для развертывания с приложением Xamarin.Android собственной библиотеки, добавьте в проект библиотеки двоичных и задать его **действие при построении** для **AndroidNativeLibrary**.

Чтобы развернуть в проекте библиотеки Xamarin.Android собственной библиотеки, добавьте в проект библиотеки двоичных и задайте его **действие при построении** для **EmbeddedNativeLibrary**.

Обратите внимание, что поскольку Android поддерживает несколько интерфейсов двоичных приложения (ABIs), Xamarin.Android необходимо знать, какие ABI собственной библиотеки, созданного для.
Это можно сделать двумя способами:

1.  Путь «проверки»
1.  С помощью `AndroidNativeLibrary/Abi` элемента в файле проекта


При сканировании пути имя родительского каталога собственной библиотеки используется для указания целевого ABI библиотеки. Таким образом при добавлении `lib/armeabi/libfoo.so` в проект, затем ABI будет иметь «вносятся» как `armeabi`.

Кроме того можно изменить файл проекта для явного указания ABI для использования:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Дополнительные сведения об использовании собственных библиотек см. в разделе [взаимодействия с собственным библиотекам](http://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio-2015"></a>Отладка машинного кода с помощью Visual Studio 2015

Если вы используете *Visual Studio 2015*, не нужно изменять файлы проекта (как описано выше).
Создавать и отлаживать C++ внутри решения Xamarin.Android, просто добавив ссылку на проект C++ **динамической общей библиотеки (Android)** проекта.

Visual Studio C++ разработчики могут видеть [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) образца попытаться осуществить отладку C++ из Visual Studio 2015 с Xamarin; и ссылаться на наш [блога](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) для получения дополнительной информации.



## <a name="related-links"></a>Связанные ссылки

- [SanAngeles_NativeDebug (sample)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
