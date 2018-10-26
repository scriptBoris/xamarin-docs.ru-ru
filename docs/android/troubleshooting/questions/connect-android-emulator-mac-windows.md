---
title: Можно ли подключиться к эмуляторам Android выполняется на компьютере Mac из виртуальной Машины Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 6e66bf4edb4269aa0f3b765df4a08b78c128f763
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115644"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>Можно ли подключиться к эмуляторам Android выполняется на компьютере Mac из виртуальной Машины Windows?

Чтобы подключиться к эмулятору Android под управлением Mac из виртуальной машины Windows, следуйте инструкциям ниже:

1.  Запуск эмулятора на компьютере Mac.

2.  KILL `adb` сервера на компьютере Mac:

    ```bash
    adb kill-server
    ```

3.  Обратите внимание на то, что эмулятор прослушивает 2 TCP-порта в сетевом интерфейсе замыкания на себя:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    Нечетные порт будет использоваться для подключения к `adb`. См. также [ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Вариант 1_: использование [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    пересылать входящие пакеты TCP выслано извне на порту 5555 (или любой другой порт, вам нравится) к порту нечетных на интерфейсе замыкания на себя (**127.0.0.1 5555** в этом примере), и пересылку исходящих пакетов другим способом:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Поскольку `nc` команды оставаться запуск в окне терминала, пакеты будут перенаправляться должным образом. CTRL + C можно ввести в окне терминала, чтобы выйти из программы `nc` команды после завершения работы с помощью эмулятора.

    (Вариант 1 обычно проще, чем вариант 2, особенно в том случае, если **системные настройки > Безопасность и конфиденциальность > брандмауэр** включено.) 

    _Вариант 2_: использование [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    для перенаправления пакеты TCP из порта `5555` (или любой другой порт, вам нравится) на [общего доступа к сети](http://kb.parallels.com/en/4948) интерфейс к порту нечетных на интерфейсе замыкания на себя (`127.0.0.1:5555` в этом примере):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Эта команда устанавливает перенаправления с помощью портов `pf packet filter` системной службы. Разрывы важны. Не забудьте сохранить их без изменений при вставке копирования. Также необходимо будет изменить имя интерфейса из *vmnet8* при использовании Parallels. `vmnet8` Это имя специального *устройством NAT* для *общего доступа к сети* режим в VMWare Fusion. Вероятнее всего, соответствующий сетевой интерфейс в Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Подключение к эмулятору с компьютера Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Замените «ip адрес---mac» IP-адрес компьютера Mac, например как перечислены командлетом `ifconfig vmnet8 | grep 'inet '`. При необходимости замените `5555` с портом, например из шага 4\. (Примечание: один из способов получить доступ из командной строки для `adb` — через [ **Сервис > Android > командной строки Adb Android** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) в Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Альтернативный способ, с помощью `ssh`

Если вы включили _удаленного входа_ на компьютере Mac, можно использовать `ssh` перенаправление для подключения к эмулятору портов.

1.  Установите клиент SSH в Windows. Один из вариантов — установить [Git для Windows](https://git-for-windows.github.io/). `ssh` Команды, становятся доступными в **Git Bash** командной строки.

2.  Выполните шаги 1 – 3 выше, чтобы запустить эмулятор, kill `adb` сервера на компьютере Mac и определить порты эмулятора.

3.  Запустите `ssh` на Windows, настроить перенаправление на двустороннем порте между локальный порт на Windows (`localhost:15555` в этом примере) и порт нечетных эмулятора на интерфейсе замыкания на себя Mac (`127.0.0.1:5555` в этом примере):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Замените `mac-username` на свое имя пользователя Mac, перечисленные по `whoami`. Замените `ip-address-of-the-mac` с IP-адрес компьютеру Mac.

4.  Подключиться к эмулятору, используя локальный порт на Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Примечание: один простой способ получить доступ из командной строки для `adb` — через [ **Сервис > Android > командной строки Adb Android** в Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Небольшой осторожность: при использовании порта `5555` для данного локального порта `adb` будет считать, что эмулятор работает локально на Windows. Это не вызывает каких-либо осложнений в Visual Studio, но в Visual Studio для Mac, он вызывает приложение завершить работу сразу после запуска.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Альтернативный способ, с помощью `adb -H` еще не поддерживается

В теории, можно использовать другой подход `adb`встроенные возможности для подключения к `adb` сервера, работающего на удаленном компьютере (например см. в разделе [ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325)).
Но расширения Xamarin.Android IDE в настоящее время не предоставляют способ настроить этот параметр.

## <a name="contact-information"></a>Контактные данные

В этом документе рассматриваются текущее поведение с марта 2016 г. Метод, описанный в этом документе не входит стабильной тестирования набора для Xamarin, поэтому он может привести к нарушению в будущем.

Если вы заметили, что метод больше не работает или если вы заметили все ошибки в документе, вы можете добавить на обсуждение, на следующем обсуждение форума: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Спасибо!

