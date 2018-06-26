---
title: Можно ли подключиться к эмуляторы Android выполняется на компьютере Mac с виртуальной Машины Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: f94c0966dd67940fc201df84a311db422d77b542
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935205"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>Можно ли подключиться к эмуляторы Android выполняется на компьютере Mac с виртуальной Машины Windows?

Чтобы подключиться к эмулятору Android, выполняемое на Mac с виртуальной машины Windows, выполните следующие действия:

1.  Запуск эмулятора на компьютере Mac.

2.  KILL `adb` сервера на компьютере Mac:

    ```bash
    adb kill-server
    ```

3.  Обратите внимание, что эмулятор прослушивает 2 порта TCP в сетевом интерфейсе замыкания на себя.

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    Порт нечетных — используется для подключения к `adb`. См. также [ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Вариант 1_: использование [ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html) для вперед входящих пакетов TCP полученных извне через порт 5555 (или любой другой порт по своему усмотрению) к порту нечетных интерфейс замыкания на себя (**127.0.0.1 5555** в этом примере), и на пересылку исходящих пакетов другим способом:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    При условии, что `nc` команды оставаться работает в окне терминала, пакеты будут пересылаться должным образом. Можно ввести управления C в окне терминала, чтобы выйти из программы `nc` команд после завершения операции с помощью эмулятора.

    (Вариант 1 обычно проще, чем вариант 2, особенно в том случае, если **системных настройках > Безопасность и конфиденциальность > брандмауэра** включено.) 

    _Вариант 2_: использование [ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html) для перенаправления TCP-пакетов из порта `5555` (или любой другой порт, вам нравится) на [общего доступа к сети](http://kb.parallels.com/en/4948) интерфейс к порту нечетных интерфейс замыкания на себя (`127.0.0.1:5555` в этом примере):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Эта команда устанавливает перенаправления с помощью портов `pf packet filter` системной службы. Важны разрывы строки. Не забудьте сохранить их без изменений при вставке копирования. Необходимо также изменить имя интерфейса из *vmnet8* с помощью Parallels. `vmnet8` Имя специальные *устройства NAT* для *общего доступа к сети* режим в VMWare Fusion. Скорее всего, соответствующий сетевой интерфейс в Parallels [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Подключиться к эмулятору с компьютера Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Замените «ip адрес из mac» с IP-адрес Mac, например как перечислены командлетом `ifconfig vmnet8 | grep 'inet '`. При необходимости замените `5555` на порт, например из шага 4\. (Примечание: один из способов получить доступ из командной строки для `adb` можно с помощью [ **Сервис > Android > Android Adb командной строки** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) в Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Альтернативный способ, с помощью `ssh`

Если вы включили _удаленного входа_ на Mac, то вы можете использовать `ssh` перенаправление для подключения к эмулятору портов.

1.  Установите клиент SSH в Windows. Один из вариантов — установка [Git для Windows](https://git-for-windows.github.io/). `ssh` Команды будут доступны в **Git Bash** командной строки.

2.  Выполните шаги 1 – 3 выше, чтобы запустить эмулятор, kill `adb` сервера на компьютере Mac и определить, какие порты эмулятора.

3.  Запустите `ssh` на Windows, чтобы настроить перенаправление портов двустороннее между локальных портов в Windows (`localhost:15555` в этом примере) и номер порта нечетных эмулятор Mac интерфейс замыкания на себя (`127.0.0.1:5555` в этом примере):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Замените `mac-username` с вашим именем пользователя Mac, упорядоченные по `whoami`. Замените `ip-address-of-the-mac` с IP-адрес Mac.

4.  Подключиться к эмулятору, с помощью локальных портов в Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Примечание: один простой способ получить доступ из командной строки для `adb` можно с помощью [ **Сервис > Android > Android Adb командной строки** в Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Небольшой осторожность: при использовании порта `5555` для локального порта `adb` считаете, что эмулятор работает локально в Windows. Это не вызывает любые проблемы в Visual Studio, но в Visual Studio для Mac он приведет к завершает работу сразу после запуска приложения.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Второй подход с использованием `adb -H` еще не поддерживается

В теории, можно использовать другой подход `adb`встроенные возможности для подключения к `adb` сервера, работающего на удаленном компьютере (например в разделе [ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325)).
Но расширения Xamarin.Android интегрированной среды разработки в настоящее время не предоставляют способ настройки этого параметра.

## <a name="contact-information"></a>Контактные данные

В этом документе рассматриваются текущее поведение на марта 2016 г. Метода, описанного в этом документе не является частью стабильный проверочный набор для Xamarin, поэтому он может быть поврежден.

Если вы заметили, что метод больше не работает, или при наличии каких-либо ошибок в документе, вы можете добавить в следующем потоке форум обсуждения: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Спасибо!

