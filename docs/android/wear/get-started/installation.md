---
title: 'Установка и настройка носят onXamarin.Android ОС '
description: В этой статье рассматриваются действия по установке и сведения о конфигурации, необходимые для подготовки компьютера и устройств для разработки с Android. В конце этой статьи вы получите рабочую установку Xamarin.Android одежды, интегрированные в Visual Studio для Mac и/или Microsoft Visual Studio и вы будете готовы приступить к созданию первого приложения Xamarin.Android одежды.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 14162663c518fdd1324f2b0340592fbae491d112
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436509"
---
# <a name="setup-and-installation"></a>Настройка и установка

_В этой статье рассматриваются действия по установке и сведения о конфигурации, необходимые для подготовки компьютера и устройств для разработки с Android. В конце этой статьи вы получите рабочую установку Xamarin.Android одежды, интегрированные в Visual Studio для Mac и/или Microsoft Visual Studio и вы будете готовы приступить к созданию первого приложения Xamarin.Android одежды._

## <a name="requirements"></a>Требования

Для создания приложений на основе Xamarin Android с требуется следующее:

-   **Visual Studio или Visual Studio для Mac** &ndash; вы Если вы используете Visual Studio, Visual Studio 2015 Professional или более поздней версии.

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 или более поздней версии необходимо установить и настроить с помощью Visual Studio или Visual Studio для Mac.

-   **Пакет SDK для Android** -Android SDK 5.0.1 (API 21) должен быть установлен через диспетчер Android SDK.

-   **Java Developer Kit** &ndash; требует разработки Xamarin Android [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) при разработке для API уровня 24 или выше (JDK 1.8 также поддерживает уровни API более ранних, чем 24).

Можно продолжать использовать [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) при разработке специально для API уровня 23 или более ранней версии.

> [!IMPORTANT]
> Xamarin.Android не поддерживает пакет JDK 9.

## <a name="installation"></a>Установка

После установки Xamarin.Android, выполните следующие действия, чтобы вы будете готовы для построения и тестирования приложений Android с: 

1.  Установка необходимых средств и пакета SDK для Android.
2.  Настройте устройство теста.
3.  Создание первого приложения Android с.

Эти действия описаны в следующих разделах.


### <a name="install-android-sdk-and-tools"></a>Установка средств и пакета SDK для Android 

Запустите **Android SDK Manager**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Как запустить диспетчер Android SDK в Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Как запустить диспетчер Android SDK в Visual Studio для Mac](installation-images/xs/sdk-menu.png)

-----


Убедитесь, что следующие Android SDK и установить средства:

* Android SDK Tools версии 24.0.0 или более поздней версии, и
* Android 4.4W (API20) или
* Android 5.0.1 (API21) или более поздней версии.

Если у вас последние версии пакета SDK и установить средства, загрузить необходимые средства пакета SDK *и* биты API (может потребоваться выполнить прокрутку бит для их поиска &ndash; выбора API, показано ниже): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Снимок экрана диспетчера пример пакета SDK Android 5.0.1 включения компонентов](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Снимок экрана диспетчера пакета SDK пример включения Android 4.4 и 5.0.1 компонентов](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Конфигурация

Прежде чем использовать протестировать приложение, необходимо настроить эмулятор Android носят либо само устройство с Android. 


### <a name="android-wear-emulator"></a>Эмулятор Android износ

Прежде чем использовать эмулятор Android носят, необходимо настроить с Android виртуального устройства Android (AVD) с помощью **диспетчера эмуляторов Google**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Запуск диспетчера эмуляторов Android из Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Запуск диспетчера эмуляторов Android из Visual Studio для Mac](installation-images/xs/emulator-menu.png)

-----

Дополнительные сведения о настройке эмулятор Android носят см. в разделе [отладки Android носить в эмуляторе,](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Устройства Android износ

Если у вас устройство Android носят, например Android Smartwatch, носят, выполнить отладку приложения на этом устройстве, а не с помощью эмулятора. Сведения о разработке с устройством износ. в разделе [отладка на устройстве с](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Создание первого приложения Android износ

Выполните [носят Здравствуйте,](~/android/wear/get-started/hello-wear.md) инструкции для создания первого приложения контрольных значений.


## <a name="packaging-your-app"></a>Упаковка приложения

Приложения Android износ всегда распространяются с помощью приложения для телефона Android помощник по поиску. 

При добавлении приложения Android носят как ссылка на основного приложения Android, он автоматически считается проект Android носят и создать все XML и метаданные, необходимые для вас. Кроме того он будет проверьте соответствие пакета и номера версий, можно легко отправить приложения Google Play. 

Дополнительные сведения об упаковке износ приложений см. в разделе [работа с упаковкой](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Связанные ссылки

- [SkeletonWear (пример)](https://developer.xamarin.com/samples/SkeletonWear/)
