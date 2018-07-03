---
title: Аппаратное ускорение для производительной работы эмулятора
description: В этой статье рассказывается, как использовать функции аппаратного ускорения компьютера для максимизации производительности эмулятора Android Emulator.
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: c2bef2c614d4cc0655deb9732ccefec223a8318a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066498"
---
# <a name="hardware-acceleration-for-emulator-performance"></a>Аппаратное ускорение для производительной работы эмулятора

_В этой статье рассказывается, как использовать функции аппаратного ускорения компьютера для обеспечения максимальной производительности эмулятора Android Emulator._

## <a name="overview"></a>Обзор

Visual Studio упрощает тестирование и отладку приложений Xamarin.Android с помощью Android Emulator в ситуациях, когда использовать устройство с Android невозможно или неудобно.
Однако если на компьютере недоступно аппаратное ускорение, Android Emulator работает слишком медленно. Вы можете значительно повысить производительность эмулятора Android Emulator, используя специальные образы виртуальных устройств на платформе x86 в сочетании с одной из двух технологий виртуализации:

1. **Microsoft Hyper-V и платформа гипервизора**. Hyper-V — это функция виртуализации в Windows, которая позволяет запускать виртуализированные компьютерные системы на физическом главном компьютере. Это рекомендуемая технология виртуализации для ускорения Android Emulator. Дополнительные сведения о Hyper-V см. в статье [Hyper-V в Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/).

2. **Intel Hardware Accelerated Execution Manager (HAXM)**. 
   HAXM — это механизм виртуализации для компьютеров на базе процессоров Intel.
   Это рекомендованный механизм виртуализации для компьютеров, где невозможно использовать Hyper-V.

Android Emulator автоматически использует аппаратное ускорение, если соблюдены следующие условия.

-   Аппаратное ускорение доступно и включено на компьютере разработчика.

-   Эмулятор работает с образом эмулятора, созданным специально для виртуального устройства на платформе **x86**.

Сведения о запуске и отладке с помощью Android Emulator см. в статье [Отладка в Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="hyper-v"></a>Hyper-V

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> Hyper-V в настоящее время поддерживается в предварительной версии.

Мы настоятельно рекомендуем разработчикам, работающим в Windows 10 (обновление за апрель 2018 г. или более поздней версии), использовать Microsoft Hyper-V для ускорения Android Emulator. Использование Android Emulator с Hyper-V

1. **Обновите Windows 10 до версии за апрель 2018 года (сборка 1803) или более поздней**.
   Чтобы проверить версию Windows, в Кортане щелкните строку поиска и введите **Сведения**. В результатах поиска выберите **Сведения о компьютере**. Прокрутите диалоговое окно **Сведения** до раздела **Характеристики Windows**. **Версия** должна быть не ранее 1803:

    [![Спецификации Windows](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **Включите платформу гипервизора Windows**.
   В строке поиска Кортаны введите **Включение и отключение компонентов Windows**.
   Прокрутите диалоговое окно **Компоненты Windows** и убедитесь, что **Платформа гипервизора Windows** включена.

    [![Платформа гипервизора Windows включена](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

   При включении **платформы гипервизора Windows** автоматически включается Hyper-V. После внесения этого изменения рекомендуется перезагрузить Windows.

3. **Установите [предварительную версию 1 Visual Studio 15.8 или более позднюю](https://visualstudio.microsoft.com/vs/preview/)**.
   В этой версии Visual Studio интегрированная среда разработки поддерживает запуск Android Emulator с Hyper-V.
 
4. **Установите пакет Android Emulator версии 27.2.7 или более поздней**. Для установки пакета последовательно выберите **Сервис > Android > Диспетчер пакетов SDK для Android** в Visual Studio. Откройте вкладку **Инструменты** и убедитесь, что установлена версия Android Emulator не ниже 27.2.7. Кроме того, убедитесь, что установлен компонент Android SDK Tools версии 26.1.1 или более поздней:

    [![Диалоговое окно "Пакеты SDK и инструменты для Android"](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. В версиях эмулятора от 27.2.7 до 27.3.1 применяется следующий обходной путь для использования Hyper-V:

    1.  В папке **C:\\Users\\_имя_пользователя_\\.android** создайте файл **advancedFeatures.ini**, если он еще не существует.

    2.  Добавьте следующую строку в файл **advancedFeatures.ini**:
        ```
        WindowsHypervisorPlatform = on
        ```


### <a name="known-issues"></a>Известные проблемы

-   Если после обновления до предварительной версии Visual Studio не удается обновить эмулятор до версии 27.2.7 или более поздней, может потребоваться установить [установщик предварительной версии](http://aka.ms/hyperv-emulator-dl) напрямую, чтобы обеспечить поддержку более новых версий эмулятора.

-   Производительность может снизиться на некоторых процессорах Intel и AMD.

-   Загрузка и развертывание приложения Android может занимать слишком много времени.

-   Ошибка доступа к MMIO может периодически мешать загрузке эмулятора Android. Перезапустите эмулятор для решения этой проблемы.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Hyper-V поддерживается только в Windows 10. Дополнительные сведения см. в разделе [Требования к Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements).

-----

## <a name="haxm"></a>HAXM

HAXM представляет собой аппаратную подсистему виртуализации (гипервизор), которая использует технологию виртуализации Intel (VT) для ускорения эмуляции приложения Android на хост-компьютере. Использование HAXM в сочетании с образами эмулятора Android x86, предоставляемыми компанией Intel, позволяет повысить производительность эмуляции Android в системах, поддерживающих технологию виртуализации.

Если вы ведете разработку на компьютере с ЦП Intel, поддерживающим технологии виртуализации, применение HAXM позволит существенно ускорить работу Android Emulator (если вы не уверены, поддерживает ли ваш ЦП технологию виртуализации, ознакомьтесь с разделом [Поддерживает ли процессор технологию виртуализации Intel?](https://www.intel.com/content/www/us/en/support/processors/000005486.html)).

> [!NOTE]
> Вы не можете запускать эмулятор на базе ускоренной виртуальной машины внутри другой виртуальной машины, например ВМ под управлением VirtualBox, VMWare или Docker. Эмулятор Android следует запускать [непосредственно на системном оборудовании](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Прежде чем использовать Android Emulator в первый раз, рекомендуется проверить наличие установленного решения HAXM и его доступность для Android Emulator.

### <a name="verifying-haxm-installation"></a>Проверка установки HAXM

Сведения о доступности решения HAXM приводятся в окне **Запуск эмулятора Android**, которое отображается во время запуска эмулятора. Чтобы запустить Android Emulator, выполните следующие действия.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Запустите Android Device Manager, выбрав **Сервис > Android > Диспетчер устройств с Android**:

    [![Расположение элементов меню Android Device Manager](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Если отображается аналогичное приведенному ниже диалоговое окно **Предупреждение о производительности**, значит, на вашем компьютере решение HAXM еще не установлено или настроено неправильно:

    ![Диалоговое окно "Предупреждение о производительности" с информацией об отсутствии готового решения HAXM](hardware-acceleration-images/win/11-perf-warn.png)

   Если выводится аналогичное показанному диалоговое окно **Предупреждение о производительности**, ознакомьтесь с разделом [Предупреждения о производительности](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn), чтобы выявить возможную проблему и устранить ее причину.

3. Выберите образ **x86** (например, **VisualStudio\_android-23\_x86\_phone**) и нажмите кнопку **Пуск**:

    ![Запуск Android Emulator с использованием образа виртуального устройства по умолчанию](hardware-acceleration-images/win/02-start-default-avd.png)

4. Во время запуска эмулятора следите за содержимым диалогового окна **Запуск эмулятора Android**. Если решение HAXM установлено, появится сообщение **Решение HAXM установлено, и эмулятор работает в быстром режиме виртуализации**, как показано на следующем снимке экрана:

    ![Диалоговое окно "Запуск эмулятора Android" при наличии доступного решения HAXM](hardware-acceleration-images/win/03-haxm-detected.png)

   Если это сообщение не отображается, возможно, решение HAXM не установлено. Например, о такой ситуации может свидетельствовать сообщение, показанное на следующем снимке экрана:

    ![На этом компьютере решение HAXM недоступно](hardware-acceleration-images/win/04-haxm-error.png)

   Если решение HAXM на вашем компьютере недоступно, установите его, выполнив действия, приведенные в следующем разделе.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Запустите Android Device Manager, выбрав **Сервис > Диспетчер устройств**:

    [![Расположение элементов меню Android Device Manager](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. Если отображается аналогичное приведенному ниже диалоговое окно **Предупреждение о производительности**, значит, на вашем компьютере решение HAXM еще не установлено или настроено неправильно:

    ![Диалоговое окно "Предупреждение о производительности" с информацией об отсутствии готового решения HAXM](hardware-acceleration-images/mac/04-avd-warning.png)

   Если выводится аналогичное показанному диалоговое окно **Предупреждение о производительности**, ознакомьтесь с разделом [Предупреждения о производительности](~/android/get-started/installation/android-emulator/troubleshooting.md#perfwarn), чтобы выявить возможную проблему и устранить ее причину.

3. Выберите образ **x86** (например, **Android\_Accelerated\_x86**) и нажмите кнопку **Воспроизвести**:

    [![Запуск Android Emulator с использованием образа виртуального устройства по умолчанию](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Во время запуска эмулятора следите за содержимым диалогового окна **Запуск эмулятора Android**. Если решение HAXM установлено, появится сообщение **Решение HAXM установлено, и эмулятор работает в быстром режиме виртуализации**, как показано на следующем снимке экрана:

    ![Диалоговое окно "Запуск эмулятора Android" при наличии доступного решения HAXM](hardware-acceleration-images/mac/03-haxm-detected.png)

   Если на вашем компьютере решение HAXM недоступно (например, если отображается сообщение об ошибке вида _Проверьте правильность установки и работоспособность решения Intel HAXM_), установите его, выполнив действия, приведенные в следующем разделе.

-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>Установка HAXM

Если эмулятор не запускается, возможно, решение HAXM придется установить вручную. Пакеты установки HAXM для ОС Windows и macOS можно найти на странице [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager). Чтобы вручную скачать и установить решение HAXM, выполните следующие действия:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Скачайте с веб-сайта Intel последнюю версию установщика [подсистемы виртуализации HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) для ОС Windows. Скачивая установщик HAXM с веб-сайта Intel, вы гарантированно получаете последнюю версию этой программы.

   Также можно скачать установщик HAXM с помощью диспетчера пакетов SDK (в диспетчере пакетов SDK выберите **Сервис > Дополнения > Ускоритель эмулятора Intel x86 (установщик HAXM)**). Как правило, SDK для Android скачивает установщик HAXM в следующий каталог:

   **C:\\Program Files (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Обратите внимание, что диспетчер пакетов SDK не устанавливает решение HAXM, а просто скачивает его установщик в указанный каталог. Запускать программу установки по-прежнему необходимо вручную.

2. Чтобы начать работу с установщиком HAXM, запустите файл **intelhaxm-android.exe**. Примите значения по умолчанию, предлагаемые в диалоговых окнах установщика:

   ![Окно программы установки Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>Аппаратное ускорение и процессоры AMD

Так как Android Emulator сейчас поддерживает аппаратное ускорение AMD [только в Linux](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies), аппаратное ускорение недоступно для компьютеров на платформе AMD под управлением Windows.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Скачайте с веб-сайта Intel последнюю версию установщика [подсистемы виртуализации HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) для ОС macOS.

2. Запустите установщик HAXM. Примите значения по умолчанию, предлагаемые в диалоговых окнах установщика:

   [![Окно программы установки Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>Связанные ссылки

* [Запуск приложений в эмуляторе Android](https://developer.android.com/studio/run/emulator)
