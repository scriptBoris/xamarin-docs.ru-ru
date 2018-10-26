---
title: Изменения в Android SDK Tools
description: Изменения в отделе пакета SDK для Android установлены уровни API и Avd.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112810"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Изменения в Android SDK Tools

_Изменения в отделе пакета SDK для Android установлены уровни API и Avd._

## <a name="changes-to-android-sdk-tooling"></a>Изменения в средства пакета SDK для Android

В последних версиях SDK Tools для Android Google исключила существующих диспетчеров AVD и SDK, заменив новый инструментарий CLI (интерфейса командной строки). **Android** программа была удалена и диспетчеры Google GUI (графический интерфейс пользователя) в Visual Studio для Mac и старые версии средств Visual Studio для Xamarin больше не будет работать после Android SDK Tools версии 25.2.5. Например, при попытке использовать **android** приведет к программе через командную строку в сообщении об ошибке следующего вида:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Ниже описаны способы управления пакета SDK для Android и виртуальных устройств Android, с помощью пакета SDK для Android 25.3.0 и более поздних версий.

### <a name="ui-tools"></a>Средства пользовательского интерфейса

Visual Studio и Visual Studio для Mac теперь предоставляют неподдерживаемые диспетчеры Google Графическим пользовательским интерфейсом на замену Xamarin:

-   Чтобы скачать средства пакета SDK для Android, платформ и других компонентов, которые необходимы для разработки приложений Xamarin.Android, используйте [диспетчер пакетов SDK Xamarin Android](~/android/get-started/installation/android-sdk.md) вместо устаревших Google диспетчер пакетов SDK.

-   Для создания и настройки виртуальных устройств Android, используйте [диспетчер устройств Android](~/android/get-started/installation/android-emulator/device-manager.md) вместо устаревшего диспетчера эмуляторов Google.

Эти средства функционально эквивалентны Google Графическим пользовательским интерфейсом диспетчеры, они заменяют.

### <a name="cli-tools"></a>Средства интерфейса командной строки

Кроме того можно использовать средства интерфейса командной строки для управления и обновления эмуляторов и пакета SDK для Android. Следующие программы теперь составляют интерфейс командной строки для средств пакета SDK для Android:

#### <a name="sdkmanager"></a>sdkmanager

**Добавлено в:** Android SDK Tools 25.2.3 (ноябрь 2016 г.) и более поздних версий.

Имеется новый программу под названием **sdkmanager** в **средства/bin** папке Android SDK. Это средство используется для поддержки пакета SDK для Android из командной строки. Дополнительные сведения об использовании этого инструмента см. в разделе [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Добавлено в:** Android SDK Tools 25.3.0 (март 2017 г.) и более поздних версий.

Имеется новый программу под названием **avdmanager** в **средства/bin** папке Android SDK. Это средство используется для поддержания Avd для эмулятора Android. Дополнительные сведения об использовании этого инструмента см. в разделе [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Понижение уровня

Возможность возврата к **Android SDK Tools** версии путем установки предыдущей версии пакета SDK для Android из [веб-сайт разработчиков под Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Используя старый графический интерфейс пользователя

Вы можете использовать исходный графический пользовательский Интерфейс, выполнив **android** программы внутри вашей **средства** , пока вы находитесь в папке **Android SDK Tools** версии **25.2.5**  или ниже.


## <a name="related-links"></a>Связанные ссылки

- [Установка пакета SDK для Android](~/android/get-started/installation/android-sdk.md)
- [Диспетчер устройств Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Общие сведения об уровнях API Android](~/android/app-fundamentals/android-api-levels.md)
- [Заметки о выпуске пакета SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
