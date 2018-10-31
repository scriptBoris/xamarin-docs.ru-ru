---
title: Устранение неполадок приложения tvOS, созданные с помощью Xamarin
description: Эта статья содержит различные советы помогут вам устранить ошибки во время разработки приложения tvOS, созданных с помощью Xamarin. Описываются известные проблемы и ошибки.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 60b54ffdce5f6bda2324c08bd3159eefeb5bc7f8
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50234861"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Устранение неполадок приложения tvOS, созданные с помощью Xamarin

_В этой статье рассматриваются известные проблемы, могут возникнуть при взаимодействии со службой поддержки tvOS Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Известные проблемы

Текущая версия поддержки tvOS Xamarin имеет следующие известные проблемы:

- **Платформа Mono** — Mono 4.3 Cryptography.ProtectedData не сможет расшифровать данные с версии 4.2 Mono. В результате NuGet будет не удается восстановить пакеты с ошибкой `Data unprotection failed` после настройки защищенного источника NuGet.
    - **Инструкции по решению** — в Visual Studio для Mac, необходимо будет снова добавить любой пакет NuGet источники, использовать проверку подлинности пароля прежде чем снова пытаться восстановить пакеты.
- **Visual Studio для Mac с F# надстройки** — ошибка при создании F# Android шаблон в Windows. Это по-прежнему должны правильно работать на компьютере Mac.
- **Xamarin.Mac** — когда единый шаблон проекта Xamarin.Mac, выполнив target Framework присвоено `Unsupported`, всплывающее окно `Could not connect to the debugger` может появиться.
    - **Возможных решений** — понизить версию платформы Mono с нашей стабильный канал.
- **Xamarin для Visual Studio & Xamarin.iOS** — при развертывании приложения WatchKit в Visual studio, ошибка `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` может появиться.

Вы найдете отчета, любые ошибки, вы на [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Устранение неполадок

В следующих разделах перечислены некоторые известные проблемы, которые могут возникнуть при использовании tvOS 9 с Xamarin.tvOS и решение этих проблем:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Недопустимый исполняемый файл — исполняемый файл не содержит bitcode

При попытке отправить на Xamarin.tvOS приложение для Apple TV App Store, может появиться сообщение об ошибке в форме _«Недопустимый исполняемый файл — исполняемый файл не содержит bitcode»_.

Чтобы решить эту проблему, сделайте следующее:

1. В Visual Studio для Mac, щелкните правой кнопкой мыши на файле проекта Xamarin.tvOS в **обозревателе решений** и выберите **параметры**.
2. Выберите **сборка tvOS** и убедитесь, что вы находитесь на **выпуска** конфигурации: 

    [![](troubleshooting-images/ts01.png "Выберите параметры построения tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Добавить `--bitcode=asmonly` для **дополнительные аргументы mtouch** поле и нажмите кнопку **ОК** кнопки.
4. Перестройте приложение в **выпуска** конфигурации.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Проверьте, что приложение содержит Bitcode tvOS

Чтобы убедиться, что сборки приложения Xamarin.tvOS Bitcode, откройте приложение "Терминал" и введите следующее:

```csharp
otool -l /path/to/your/tv.app/tv
```

В выходных данных Обратите внимание на следующее:

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` и `size` будет отличаться, но остальные поля должны быть идентичными.

Необходимо убедиться, что статические третьим лицам (`.a`) вы используете библиотеки были построены tvOS библиотеки (не iOS) и что они также включает сведения о bitcode.

Для приложений или библиотек, которые включают допустимые bitcode `size` будет больше единицы. Существуют ситуации, где библиотеки может иметь маркер bitcode, но не содержат допустимые bitcode. Пример:

**Недопустимый Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Допустимый Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Обратите внимание на разницу в `size` между две библиотеки в данном списке выполняется выше. Библиотеки должен быть создан из архива сборки Xcode с поддержкой bitcode (параметр Xcode `ENABLE_BITCODE`) в качестве решения этой проблемы размер.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Приложения, которые содержат только срез arm64 должен также иметь «arm64» в список UIRequiredDeviceCapabilities в файле Info.plist.

При отправке приложения для Apple TV App Store для публикации, в форме может появиться ошибка:

_«Приложения, которые содержат только срез arm64 также должны содержать «arm64» в списке UIRequiredDeviceCapabilities в файле Info.plist.»_

В этом случае изменение вашего `Info.plist` файл и убедитесь, что он имеет следующие ключи:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Повторная компиляция приложения для выпуска и повторно отправить в iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>«MTouch» сбой выполнения задачи--

Если используется библиотека стороннего производителя (например, MonoGame) и сбой компиляции вашего выпуска с серию сообщения об ошибках, заканчивающиеся на `Task "MTouch" execution -- FAILED`, попробуйте добавить `-gcc_flags="-framework OpenAL"` для вашей **сенсорного ввода дополнительных аргументов**:

[![](troubleshooting-images/mtouch01.png "Выполнение задач MTouch")](troubleshooting-images/mtouch01.png#lightbox)

Следует также добавить `--bitcode=asmonly` в **сенсорного ввода дополнительных аргументов**, иметь параметры компоновщика со значением **компоновать все** и чистая компиляция.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Ошибка ITMS 90471. Крупный значок отсутствует

Если вы получите сообщение в виде «ITMS 90471 ошибка. Крупный значок отсутствует» при попытке отправить на Xamarin.tvOS приложение для Apple TV App Store для выпуска, выполните следующие действия:

1. Убедитесь, что включены ресурсы больших значков в вашей `Assets.car` файла, созданного с помощью [значки приложений](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) документации.
2. Убедитесь, что вы включили `Assets.car` файла из [работа со значками и изображениями](~/ios/tvos/app-fundamentals/icons-images.md) документации в ваш пакет конечного приложения.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Недопустимый пакет — приложение, которое поддерживает игровые устройства управления также должен поддерживать удаленного Apple TV

или 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Недопустимый пакет — приложений Apple TV с помощью платформы игровое должны содержать ключ GCSupportedGameControllers в файле Info.plist приложения

Игровые устройства управления можно использовать для улучшения игровой процесс и атмосферу погружения в игре. Они могут также использоваться для управления стандартный интерфейс Apple TV, так что пользователю не нужно переключаться между удаленного, так и контроллер.

Если вы отправляете в Xamarin.tvOS приложение с поддержкой контроллера игры в магазине приложений Apple TV, и вы получаете сообщение об ошибке в виде:


_Была обнаружена одна или несколько проблем с последней доставки для «имя приложения». Доставки прошла успешно, но вы можете исправить следующие проблемы в доставка на следующий:_

_Недопустимый пакет — приложение, которое поддерживает игровые устройства управления также должен поддерживать удаленного Apple TV._

или 

_Недопустимый пакет — приложений Apple TV с помощью платформы игровое должны содержать ключ GCSupportedGameControllers в файле Info.plist приложения._

Решением является добавление поддержки для удаленного Siri (`GCMicroGamepad`) в приложение `Info.plist` файл. Профиль контроллера Micro игру было добавлено компанией Apple, предназначенных для Siri Remote. Например содержат следующие ключи:

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> Игровые устройства Bluetooth — это необязательная Покупка, конечные пользователи могут сделать, приложение не может заставить пользователя приобрести. Если приложение поддерживает игровые устройства управления он должен также поддерживать удаленное Siri таким образом, игра готовый к применению всеми пользователями Apple TV.

Дополнительные сведения см. в разделе наших [работа с игровые](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) части нашей [контроллеры Siri Remote и контроллеры Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) документации.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Несовместимая целевая платформа:. NetPortable, Version = версии 4.5 профиль = Profile78

При попытке включения в Xamarin.tvOS проект переносимой библиотеки классов (PCL) может появиться сообщение для формирования:

_Несовместимая целевая платформа:. NetPortable, Version = версии 4.5 профиль = Profile78_

Чтобы решить эту проблему, добавьте XML-файл называется ` Xamarin.TVOS.xml` со следующим содержимым:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

По следующему пути:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Обратите внимание на то, что номер профиль в пути должен соответствовать номеру профиль из библиотеки PCL.

С помощью этого файла на месте можно будет успешно добавить файл переносимой библиотеки Классов в проект Xamarin.tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
