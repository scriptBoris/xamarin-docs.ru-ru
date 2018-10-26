---
title: 'Установка и настройка Wear onXamarin.Android ОС '
description: В этой статье рассматриваются действия по установке и сведения о конфигурации, необходимые для подготовки ваши компьютеры и устройства для разработки приложений Android Wear. К концу этой статьи у вас будет рабочая Установка износа Xamarin.Android будет интегрирована в Visual Studio для Mac и/или Microsoft Visual Studio и вы будете готовы приступить к созданию своего первого приложения Xamarin.Android одежды.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ed6d3c1cfc14bd0dece5ffe494cc02fd8f608b1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117282"
---
# <a name="setup-and-installation"></a>Настройка и установка

_В этой статье рассматриваются действия по установке и сведения о конфигурации, необходимые для подготовки ваши компьютеры и устройства для разработки приложений Android Wear. К концу этой статьи у вас будет рабочая Установка износа Xamarin.Android будет интегрирована в Visual Studio для Mac и/или Microsoft Visual Studio и вы будете готовы приступить к созданию своего первого приложения Xamarin.Android одежды._

## <a name="requirements"></a>Требования

Для создания приложений на базе Xamarin Android Wear требуется следующее:

-   **Visual Studio или Visual Studio для Mac** &ndash; вы Если вы используете Visual Studio, Visual Studio 2015 Professional или более позднюю версию.

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 или более поздней версии необходимо установить и настроить Visual Studio или Visual Studio для Mac.

-   **Пакет SDK для Android** -пакета SDK для Android 5.0.1 (API 21) или более поздней версии необходимо установить через диспетчер пакетов SDK для Android.

-   **Java Developer Kit** &ndash; требует разработки Xamarin Android [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) при разработке для API уровня 24 или более поздней версии (JDK 1.8 также поддерживает уровни API ниже 24).

Вы можете продолжать использовать [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) при разработке специально для уровень API 23 или более ранней версии.

> [!IMPORTANT]
> Xamarin.Android не поддерживает пакет JDK 9.

## <a name="installation"></a>Установка

После установки Xamarin.Android, выполните следующие действия, чтобы вы будете готовы сборку и тестирование приложений Android Wear: 

1.  Установка необходимых средств и пакета SDK для Android.
2.  Настройте тестовое устройство.
3.  Создание первого приложения для Android Wear.

Эти действия описаны в следующих разделах.


### <a name="install-android-sdk-and-tools"></a>Установка средств и пакета SDK для Android 

Запустите **диспетчер Android SDK**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Как запустить диспетчер пакетов SDK для Android в Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Как запустить диспетчер пакетов SDK для Android в Visual Studio для Mac](installation-images/xs/sdk-menu.png)

-----


Убедитесь, что следующий пакет SDK для Android и установить средства:

* Android SDK Tools v 24.0.0 или более поздней версии, и
* Android 4.4W (API20), или
* Android 5.0.1 (API21) или более поздней версии.

Если у вас нет последних SDK и средства, установленные, скачивание необходимых средств SDK *и* биты API (вам может потребоваться прокрутить некоторое время, чтобы найти их &ndash; ниже показан выбор API): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Снимок экрана: диспетчер пакетов SDK пример включения Android 5.0.1 компонентов](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Снимок экрана примере диспетчер пакетов SDK для Android 4.4 и 5.0.1 компонентов](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Конфигурация

Перед использованием протестировать приложение, необходимо настроить эмулятор Android Wear либо само устройство Android Wear. 


### <a name="android-wear-emulator"></a>Эмулятор Android Wear

Прежде чем использовать эмулятор Android Wear, необходимо настроить Android Wear виртуальных устройств Android (AVD) с помощью **диспетчер эмуляторов Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Как запустить диспетчер эмуляторов Android из Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Как запустить диспетчер эмуляторов Android из Visual Studio для Mac](installation-images/xs/emulator-menu.png)

-----

Дополнительные сведения о настройке эмулятора Android Wear см. в разделе [отладки Android Wear на эмуляторе](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Устройство Android Wear

При наличии на устройстве Android Wear, таких как Android Wear Smartwatch, можно отлаживать приложение на этом устройстве, вместо того чтобы использовать симулятор. Сведения о разработке с устройством одежды, см. в разделе [отладка на устройстве Wear](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Создание первого приложения для Android Wear

Выполните [Wear Hello,](~/android/wear/get-started/hello-wear.md) инструкциям, чтобы создать свое первое приложение контрольных значений.


## <a name="packaging-your-app"></a>Упаковка приложения

Приложения Android wear всегда распространяются с помощью дополнительного приложения для телефона Android. 

При добавлении приложения Android Wear как ссылка на главное приложение Android, он автоматически предполагается, что в проект Android Wear и создаст все необходимые XML и метаданные для вас. Кроме того он будет проверьте соответствие пакета и номера версий, можно легко переводить приложения в Google Play. 

Дополнительные сведения об упаковке приложений одежды, см. в разделе [работа с упаковкой](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Связанные ссылки

- [SkeletonWear (пример)](https://developer.xamarin.com/samples/SkeletonWear/)
