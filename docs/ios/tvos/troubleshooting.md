---
title: Устранение неполадок tvOS приложений с использованием Xamarin
description: Эта статья содержит различные советы для помощи в устранении во время разработки приложения tvOS, созданных с помощью Xamarin. Описываются известные проблемы и ошибки.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: e69157cf9c8a9b9405e31edb2906754328653ccb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789207"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Устранение неполадок tvOS приложений с использованием Xamarin

_В этой статье описаны известные проблемы. могут возникнуть при работе с поддержкой tvOS Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Известные проблемы

Текущая версия поддержки tvOS Xamarin содержит следующие проблемы:

- **Моно Framework** — Cryptography.ProtectedData 4.3 моно не удастся расшифровать данные из моно 4.2. В результате пакетов NuGet не удастся восстановить с ошибкой `Data unprotection failed` после настройки защищенного источника NuGet.
    - **Инструкции по решению** — в Visual Studio для Mac, необходимо добавить этот пароль использовать проверку подлинности перед попыткой снова восстановите пакеты, источники какого-либо пакета NuGet.
- **Visual Studio для Mac с F # надстройки** — ошибка при создании шаблона Android на F # в Windows. Это по-прежнему будет правильно работать на компьютере Mac.
- **Xamarin.Mac** — Если значение целевой версии платформы, выполнив единой шаблона проекта Xamarin.Mac `Unsupported`, всплывающее окно `Could not connect to the debugger` может появиться.
    - **Возможных решений** — понизить доступной в наш канал стабильной версии framework, моно.
- **Visual Studio с Xamarin & Xamarin.iOS** — при развертывании приложений WatchKit в Visual studio, ошибка `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` может появиться.

Можно найти любой ошибки, то отчет для [Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

## <a name="troubleshooting"></a>Устранение неполадок

В следующих разделах перечислены некоторые известные проблемы, которые могут возникнуть при использовании tvOS 9 с Xamarin.tvOS и решения этих проблем:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Недопустимый исполняемый файл - исполняемый файл не содержит bitcode

При попытке отправить Xamarin.tvOS приложения в магазине приложений Apple TV, может появиться сообщение об ошибке в форме _«Недопустимый исполняемый файл - исполняемый файл не содержит bitcode»_.

Чтобы решить эту проблему, выполните следующие действия.

1. В Visual Studio для Mac, щелкните правой кнопкой мыши файл проекта Xamarin.tvOS в **обозревателе решений** и выберите **параметры**.
2. Выберите **tvOS построения** и убедитесь, что вы находитесь в **выпуска** конфигурации: 

    [![](troubleshooting-images/ts01.png "Выберите tvOS параметры построения")](troubleshooting-images/ts01.png#lightbox)
3. Добавить `--bitcode=asmonly` для **mtouch дополнительные аргументы** поле и нажмите кнопку **ОК** кнопки.
4. Перестройте приложение в **выпуска** конфигурации.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Проверьте, что ваш tvOS Bitcode содержит приложение

Чтобы убедиться, что сборки приложения Xamarin.tvOS Bitcode, откройте приложение "Терминал" и введите следующую команду:

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

`addr` и `size` будет отличаться, но другие поля должны быть идентичными.

Необходимо убедиться, что статический третьим лицам (`.a`) вы используете библиотеки были построены tvOS библиотеки (не операций ввода-вывода) и также включает сведения о bitcode.

Для приложений или библиотек, которые включают допустимые bitcode `size` будет больше единицы. Существуют ситуации, где библиотеки можно bitcode маркера, еще не содержит допустимый bitcode. Пример:

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

Обратите внимание на разницу в `size` между двух библиотек в перечисленных примере запускается. Библиотеки должен быть создан из архива сборки Xcode с bitcode включен (параметр Xcode `ENABLE_BITCODE`) в качестве решения этой проблемы размер.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Приложения, которые содержат только срез arm64 должен также иметь «arm64» в списке UIRequiredDeviceCapabilities в файле Info.plist.

При отправке приложения в магазине приложений Apple TV для публикации, в форме может появиться ошибка:

_«Приложений, которые содержат только срез arm64 должен иметь «arm64» в списке UIRequiredDeviceCapabilities в файле Info.plist.»_

В этом случае измените вашей `Info.plist` файл и убедитесь, что установлены следующие ключи:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Перекомпилируйте приложение для выпуска и повторно отправьте в iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Задача «MTouch» — Ошибка выполнения

Если вы используете сторонних производителей библиотеки (например, MonoGame) и вашей версии компиляции завершилась серию сообщения об ошибках, заканчивающиеся на `Task "MTouch" execution -- FAILED`, попробуйте добавить `-gcc_flags="-framework OpenAL"` для вашей **касания дополнительные аргументы**:

[![](troubleshooting-images/mtouch01.png "Выполнение задачи MTouch")](troubleshooting-images/mtouch01.png#lightbox)

Следует также добавить `--bitcode=asmonly` в **сенсорный ввод дополнительных аргументов**, установлено в качестве параметры компоновщика **все связи** и выполните чистую компиляции.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Ошибка ITMS 90471. Большой значок отсутствует

Если вы получите сообщение в виде «ITMS 90471 ошибка. Большой значок отсутствует» при попытке отправить Xamarin.tvOS приложения в магазине приложений Apple TV для выпуска, выполните следующие действия:

1. Убедитесь, что включены средств крупных значков в вашей `Assets.car` файл, созданный с помощью [значки приложений](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) документации.
2. Убедитесь, что включены `Assets.car` файл из [работы со значками и образы](~/ios/tvos/app-fundamentals/icons-images.md) документации в ваш пакет конечного приложения.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Недопустимый пакет — приложение, которое поддерживает игровые устройства должно также поддерживать удаленного Apple TV

или 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Недопустимый пакет — Apple TV приложений с помощью платформы игровое необходимо включить GCSupportedGameControllers ключ в файле Info.plist приложения

Игровые устройства можно использовать для улучшения игрового процесса и обеспечивают смысле погружения в игру. Они могут также использоваться для управления стандартный интерфейс Apple TV, поэтому пользователю не нужно переключаться между удаленной и контроллера.

Если вы собираетесь отправить Xamarin.tvOS приложения с поддержкой контроллера игры в магазине приложений Apple TV, и вы получаете сообщение об ошибке в форме:


_Обнаружены одна или несколько проблем с вашей последней доставки для «имя приложения». Ваш доставка завершилась успешно, но вы можете устранить следующие проблемы в доставка на следующий:_

_Недопустимый пакет — приложение, которое поддерживает игровые устройства должно также поддерживать Apple TV удаленной._

или 

_Недопустимый пакет — Apple TV приложений с помощью платформы игровое необходимо включить GCSupportedGameControllers ключ в файле Info.plist приложения._

Решение: добавьте поддержка удаленного Siri (`GCMicroGamepad`) в свое приложение `Info.plist` файл. Профиль контроллера игры Micro был добавлен пользователем Apple целевой удаленного Siri. Примерами являются следующие ключи:

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
> Игровые устройства Bluetooth — это необязательный покупки, конечные пользователи могут выполнить, приложение не может заставить пользователя приобрести. Если приложение поддерживает игровые устройства оно должно также поддерживать удаленного Siri таким образом, готовый к применению всеми пользователями Apple TV игры.

Дополнительные сведения см. в разделе нашей [работа с игровые устройства](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) часть наших [контроллеров Bluetooth и Siri удаленного](~/ios/tvos/platform/remote-bluetooth.md) документации.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Несовместимые требуемая версия .NET framework:. NetPortable, версия = версии 4.5, профиль = Profile78

При попытке включить в проект Xamarin.tvOS Переносимая библиотека классов (PCL) может появиться сообщение для формирования:

_Несовместимые требуемая версия .NET framework:. NetPortable, версия = версии 4.5, профиль = Profile78_

Чтобы решить эту проблему, добавьте в XML-файл с именем ` Xamarin.TVOS.xml` со следующим содержимым:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

По следующему пути:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Обратите внимание, что номер профиля в пути должно соответствовать профиля число PCL.

Этот файл на месте можно для успешного добавления файла PCL Xamarin.tvOS проекта.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS человека направляющие интерфейса](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
