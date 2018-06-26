---
title: Изменения в Android SDK Tools
description: Изменения в управлении Android SDK установлен уровни API и Avd.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: 4e808736fd92fa40ecbf0c24938c0fedd7afcff9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935455"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Изменения в Android SDK Tools

_Изменения в управлении Android SDK установлен уровни API и Avd._

## <a name="changes-to-android-sdk-tooling"></a>Изменения средств пакета SDK для Android

В последних версиях средств пакета SDK для Android Google был удален из существующих диспетчеров AVD и SDK им следует предпочесть новые инструменты CLI (интерфейс командной строки). **Android** программа была удалена и диспетчеры Google GUI (графический интерфейс пользователя) в Visual Studio для Mac и старые версии набора средств Visual Studio для Xamarin больше не будет работать в более ранней версией 25.2.5 средств пакета SDK для Android. Например, при попытке использовать **android** программы командной строки приведет к сообщение об ошибке следующего вида:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Следующие разделы описывают управления пакета SDK для Android и Android виртуальные устройства, с помощью пакета SDK для Android 25.3.0 и более поздних версий.

### <a name="ui-tools"></a>Средства пользовательского интерфейса

Visual Studio и Visual Studio для Mac теперь предоставляют Xamarin на замену неподдерживаемые диспетчеры Google Графическим пользовательским интерфейсом:

-   Чтобы загрузить средства пакета SDK для Android, платформы и другие компоненты, которые необходимы для разработки приложений Xamarin.Android, используйте [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) вместо прежних версий пакета SDK Manager Google.

-   Для создания и настройки виртуальных устройств Android, используйте [диспетчер устройств Android](~/android/get-started/installation/android-emulator/device-manager.md) вместо устаревших Google диспетчера эмуляторов.

Эти средства функционально эквивалентны на основе графического интерфейса пользователя Google диспетчеры, они заменяют.

### <a name="cli-tools"></a>Средства интерфейса командной строки

Кроме того можно использовать средства интерфейса командной строки для управления и обновления эмуляторов и пакета SDK для Android. Следующие программы теперь составляют интерфейс командной строки для средства пакета SDK для Android:

#### <a name="sdkmanager"></a>sdkmanager

**Добавлено в:** пакет инструментов SDK Android 25.2.3 (ноябрь 2016 г.) и более поздних версий.

Имеется новая программа, которая называется **sdkmanager** в **Сервис/bin** папку пакета SDK для Android. Это средство используется для сохранения пакета SDK для Android в командной строке. Дополнительные сведения об использовании этого инструмента см. в разделе [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Добавлено в:** пакет инструментов SDK Android 25.3.0 (марта 2017 г.) и более поздних версий.

Имеется новая программа, которая называется **avdmanager** в **Сервис/bin** папку пакета SDK для Android. Это средство используется для поддержания для эмулятора Android Avd. Дополнительные сведения об использовании этого инструмента см. в разделе [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Понижение уровня

Вы можете перейти к **пакет инструментов SDK Android** версии путем установки предыдущей версии пакета SDK для Android из [веб-сайте разработчика Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>С помощью старого графического пользовательского интерфейса

Можно по-прежнему использовать исходный графического пользовательского интерфейса, выполнив **android** программы внутри вашей **средства** при условии, что находятся в папке **пакет инструментов SDK Android** версии **25.2.5**  или ниже.


## <a name="related-links"></a>Связанные ссылки

- [Установка пакета SDK для Android](~/android/get-started/installation/android-sdk.md)
- [Диспетчер устройств Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Общие сведения об уровнях API Android](~/android/app-fundamentals/android-api-levels.md)
- [Заметки о выпуске пакета SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
