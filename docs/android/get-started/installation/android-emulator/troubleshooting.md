---
title: Устранение неполадок в Android Emulator
description: В этой статье рассказывается, как диагностировать и устранять неполадки, которые могут возникать при использовании Android Emulator.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 1d13a3dae509fea4a2e955c4ad206a81a57e75ed
ms.sourcegitcommit: 6433b424410a850f504e0f934bbb5baf8f093e49
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2018
ms.locfileid: "39067338"
---
# <a name="android-emulator-troubleshooting"></a>Устранение неполадок в Android Emulator

_В этой статье описаны наиболее типичные ошибки и предупреждения, возникающие при настройке и запуске Android Emulator, а также способы их устранения._

<a name="perfwarn" />

## <a name="performance-warnings"></a>предупреждения производительности

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Начиная с Visual Studio 2017 версии 15.4, могут появляться предупреждения о производительности при первом развертывании приложения в Android Emulator. Причины появления этих предупреждений описываются ниже.

### <a name="computer-does-not-contain-an-intel-procesor"></a>В компьютере не установлен процессор Intel

![В компьютере не установлен процессор Intel](troubleshooting-images/01-no-intel-processor.png)

Это диалоговое окно появляется, если в компьютере не установлен процессор Intel, который необходим для ускорения в Android SDK Emulator. Если в вашем компьютере не установлен процессор Intel, мы рекомендуем для разработки использовать физическое устройство с Android.

### <a name="hyper-v-is-installed-or-active"></a>Установлен или активен Hyper-V

![Установлен или активен Hyper-V](troubleshooting-images/02-hyper-v-active.png)

Это диалоговое окно появляется, если установлен или активен Hyper-V, и он должен быть отключен. В разделе [Отключение Hyper-V](#disable-hyperv) описывается решение этой проблемы.

### <a name="haxm-is-not-installed"></a>HAXM не установлен

![HAXM не установлен](troubleshooting-images/03-haxm-not-installed.png)

Это сообщение говорит о том, что в компьютере установлен процессор Intel, Hyper-V отключен, но HAXM не установлен.
В разделе [Установка HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) описывается процедура установки HAXM.

### <a name="haxm-process-not-running"></a>Процесс HAXM не запущен

![Процесс HAXM не запущен](troubleshooting-images/04-haxm-process-not-running.png)

Это диалоговое окно появляется, если в компьютере установлен процессор Intel, Hyper-V отключен, HAXM установлен, но процесс HAXM не запущен. Для решения этой проблемы откройте командную строку и выполните следующие команды:

```cmd
sc query intelhaxm
```

Если процесс HAXM запущен, то вы увидите примерно следующий вывод команды:

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```

Если параметр `STATE` не равен `RUNNING`, то для решения этой проблемы см. раздел [Использование Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator).


### <a name="other-failures"></a>Неизвестная ошибка

![Неизвестная ошибка](troubleshooting-images/05-other-failure.png)

Это диалоговое окно появляется, если в компьютере установлен процессор Intel, Hyper-V отключен, HAXM установлен, процесс HAXM запущен, но эмулятор не удалось запустить по неизвестной причине.
Сведения о решении этой проблемы см. в разделе [Использование Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator).

### <a name="disabling-performance-warnings"></a>Отключение предупреждений о производительности

При необходимости предупреждения о производительности можно отключить. В Visual Studio нажмите **Инструменты > Параметры > Xamarin > Параметры Android** и отключите параметр **Предупреждать, если ускорение виртуальных устройств Android не поддерживается (HAXM)**:

[![Отключение предупреждений о проблемах с ускорением виртуальных устройств Android](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Начиная с Visual Studio для Mac версии 7.2 (сборка 559) могут появляться предупреждения о производительности при первом развертывании приложения в Android Emulator. Причины появления этих предупреждений описываются ниже.

### <a name="haxm-is-not-installed"></a>HAXM не установлен

![HAXM не установлен](troubleshooting-images/03-haxm-not-installed.png)

Это сообщение говорит о том, что HAXM не установлен.
В разделе [Установка HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) описывается процедура установки HAXM.

### <a name="haxm-process-not-running"></a>Процесс HAXM не запущен

![Процесс HAXM не запущен](troubleshooting-images/04-haxm-process-not-running.png)

Это сообщение появляется, если процесс HAXM не запущен. Подробные сведения о решении этой проблемы см. в разделе [Использование Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator).

### <a name="other-failures"></a>Неизвестная ошибка

![Неизвестная ошибка](troubleshooting-images/05-other-failure.png)

Это сообщение появляется, если эмулятор не запускается по неизвестной причине. Сведения о решении этой проблемы см. в разделе [Использование Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator).

-----

## <a name="deployment-issues"></a>Проблемы развертывания

Если отображается ошибка о сбое при установке APK на ваш компьютер или при запуске Android Debug Bridge (**adb**), убедитесь, что Android SDK может подключиться к эмулятору. Для этого выполните следующее:

1. Запустите эмулятор из **Android Device Manager** (выберите ваше виртуальное устройство и зажмите **Запустить**).

2. Откройте командную строку и перейдите в папку, в которой установлен **adb**. Например, в Windows это может быть путь **C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**.

3. Введите следующую команду:

   ```shell
   adb devices
   ```

4. Если эмулятор доступен из Android SDK, то он отобразится в списке подключенных устройств. Пример:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Если эмулятор не появился в этом списке, запустите **Диспетчер пакетов SDK для Android**, примените все обновления и запустите эмулятор еще раз.


## <a name="haxm-issues"></a>Проблемы с HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Если Android Emulator не запускается, обычно это вызвано проблемами с HAXM. Проблемы с HAXM часто вызваны конфликтом с другими технологиями виртуализации, неправильной конфигурацией или устаревшим драйвером HAXM.

<a name="virt-conflicts" />

### <a name="haxm-virtualization-conflicts"></a>Конфликты HAXM с другими технологиями виртуализации

HAXM может конфликтовать с другими технологиями, использующими виртуализацию, такими как Hyper-V, Windows Device Guard и некоторые антивирусы:

- **Hyper-V** &ndash; если вы используете версию Windows младше **версии Windows 10 с обновлением за апрель 2018 г. (сборка 1803)**, а Hyper-V включен, см. раздел [Отключение Hyper-V](#disable-hyperv).

- **Device Guard** &ndash; Device Guard и Credential Guard могут препятствовать отключению Hyper-V на компьютерах с Windows. Порядок отключения Device Guard и Credential Guard см. в разделе [Отключение Device Guard](#disable-devguard).

- **Антивирусное ПО** &ndash; если на вашем компьютере запущенно антивирусное ПО, использующее аппаратную виртуализацию (например, Avast), отключите или удалите его, перезагрузите компьютер и снова запустите Android SDK Emulator.


### <a name="incorrect-bios-settings"></a>Неправильные настройки BIOS

Если вы используете HAXM на компьютере с Windows, HAXM не заработает, пока технология виртуализации (Intel VT-x) не будет включена в BIOS. Если VT-x отключен, то при попытке запуска Android Emulator вы получите следующую ошибку.

**Компьютер удовлетворяет требованиям для запуска HAXM, но технология виртуализации Intel (VT-x) отключена.**

Для исправления этой ошибки перезагрузите компьютер в BIOS, включите VT-x и SLAT (трансляция адресов второго уровня) и перезагрузите компьютер обратно в Windows.

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>Отключение Hyper-V

Если вы используете версию Windows младше **версии Windows 10 с обновлением за апрель 2018 г. (сборка 1803)**, а Hyper-V включен, нужно отключить Hyper-V и перезапустить компьютер для установки и использования HAXM. Если вы используете **версию Windows 10 с обновлением за апрель 2018 г. (сборка 1803)** или более позднюю, Android Emulator версии 27.2.7 или более поздней может использовать Hyper-V (вместо HAXM) для аппаратного ускорения, поэтому отключать Hyper-V не требуется.

Технологию Hyper-V можно отключить из панели управления, выполнив следующие действия:

1. В поле поиска Windows введите **Программы и**, после чего выберите элемент **Программы и компоненты**.

2. В диалоговом окне **Программы и компоненты** панели управления щелкните **Включение или отключение компонентов Windows**:

    ![Включение или отключение компонентов Windows](troubleshooting-images/win/07-turn-windows-features.png)

3. Снимите флажок **Hyper-V** и перезагрузите компьютер:

    ![Отключение технологии Hyper-V в диалоговом окне "Компоненты Windows"](troubleshooting-images/win/08-uncheck-hyper-v.png)

Также для отключения технологии Hyper-V можно использовать следующий командлет Powershell:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

Intel HAXM и Microsoft Hyper-V не могут быть активны одновременно. К сожалению, переключение между технологиями Hyper-V и HAXM без перезагрузки компьютера невозможно. 

В некоторых случаях выполнение описываемых выше действий не позволяет отключить технологию Hyper-V, если включены функции Device Guard и Credential Guard. Если вам не удается отключить Hyper-V (или по всем признакам эта технология отключена, но установка HAXM все равно завершается сбоем), отключите функции Device Guard и Credential Guard, выполнив действия, описываемые в следующем разделе.

<a name="disable-devguard" />

### <a name="disabling-device-guard"></a>Отключение функции Device Guard

Функции Device Guard и Credential Guard могут препятствовать отключению технологии Hyper-V на компьютерах под управлением ОС Windows. Это часто происходит на компьютерах, присоединенных к доменам, которые настраиваются и контролируются управляющей ими организацией.
Чтобы проверить, выполняется ли функция **Device Guard** в ОС Windows 10, выполните следующие действия:

1. В **поле поиска Windows** введите **Сведения о системе**, чтобы запустить приложение **Сведения о системе**.

2. В разделе **Сведения о системе** проверьте наличие службы **Безопасность на основе виртуализации Device Guard** и убедитесь, что она имеет состояние **Выполняется**:

   [![Функция Device Guard установлена и выполняется](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

Если функция Device Guard включена, выполните следующие действия для ее отключения:

1. Убедитесь, что технология **Hyper-V** отключена в окне **Включение или отключение компонентов Windows**, как описывается в предыдущих разделах.

2. В поле поиска Windows введите **gpedit** и выберите элемент **Изменение групповой политики**. Будет запущен **редактор локальных групповых политик**.

3. В **редакторе локальных групповых политик** выберите **Конфигурация компьютера > Административные шаблоны > Система > Device Guard**:

   [![Функция Device Guard в редакторе локальных групповых политик](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

4. Измените значение параметра **Включить средство обеспечения безопасности на основе виртуализации** на **Отключено** (как показано выше) и закройте **редактор локальных групповых политик**.

5. В поле поиска Windows введите **cmd**. Когда в результатах поиска появится элемент **Командная строка**, щелкните пункт **Командная строка** правой кнопкой мыши и выберите **Запустить от имени администратора**.

6. Скопируйте и вставьте следующие команды в окно командной строки (если диск **Z:** используется, выберите вместо него букву свободного диска):

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. Перезапустите компьютер. На экране загрузки должен появиться запрос следующего вида:

   **Вы действительно хотите отключить Credential Guard?**

   Нажмите указанную в запросе клавишу, чтобы отключить Credential Guard.

8. После перезагрузки компьютера еще раз убедитесь, что технология Hyper-V отключена (см. ранее описываемые действия).

Если технология Hyper-V по-прежнему не отключена, значит, отключение функций Device Guard или Credential Guard запрещено политиками на вашем присоединенном к домену компьютере. В таком случае вы можете запросить у администратора домена исключение, которое позволит отключить Credential Guard. Кроме того, вы можете использовать для работы с HAXM компьютер, который не присоединен к домену.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Если Android Emulator не запускается, обычно это вызвано проблемами с HAXM. Проблемы с HAXM часто вызваны конфликтом с другими технологиями виртуализации, неправильной конфигурацией или устаревшим драйвером HAXM. Переустановите драйвер HAXM при помощи процедуры, описанной в разделе [Установка HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm).

-----

