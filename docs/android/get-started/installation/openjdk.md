---
title: Дистрибутив OpenJDK от Майкрософт для разработки мобильных приложений
description: Пошаговое руководство по настройке дистрибутива OpenJDK от Майкрософт для разработки мобильных приложений и устранению неполадок с ним.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: aad88ad883dea1e0430a047a71a2c191195efffe
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459906"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Дистрибутив OpenJDK от Майкрософт для разработки мобильных приложений

_В этом руководстве описываются шаги по переходу на внутренний дистрибутив OpenJDK. Этот дистрибутив предназначен для разработки мобильных приложений._

## <a name="overview"></a>Обзор

Начиная с версий Visual Studio 15.9 и Visual Studio для Mac 7.7 решение "Инструменты Visual Studio для Xamarin" более не использует пакет JDK от Oracle и перешло на **облегченную версию OpenJDK, предназначенную исключительно для разработки на Android**. Это обязательная миграция, так как Oracle прекращает поддержку коммерческих дистрибутивов JDK 8 в 2019 г., а JDK 8 является обязательной зависимостью для разработки приложений Android.

Такой переход предлагает следующие преимущества:

- У вас всегда будет версия OpenJDK, которая подходит для разработки приложений Android.

- Скачивание JDK 9 или более поздней версии от Oracle не повлияет на интерфейс разработки.

- Уменьшены размер скачиваемого файла и объем занимаемой памяти.

- Отсутствие потенциальных проблем со сторонними серверами и установщиками.

Если вы хотите быстрее перейти на обновленную версию, доступны сборки OpenJDK от Майкрософт для разработки мобильных приложений, которые вы можете опробовать на Windows и Mac. Процесс установки описан ниже. Вернуться на JDK от Oracle можно в любое время.

## <a name="download"></a>Скачать

Дистрибутив OpenJDK для мобильных приложений установится автоматически, если вы выберете пакеты SDK для Android в Visual Studio Installer в Windows. Он будет включен в обновление 15.9 для пользователей, у которых выбран пакет SDK для Android.

На Mac пакет OpenJDK для мобильных приложений будет установлен в рамках рабочей нагрузки Android для новых установок. Существующие пользователи Visual Studio для Mac получат приглашение установить его в составе обновления до версии 7.7. Среда IDE предложит вам перейти на новый пакет JDK и станет использовать его после перезапуска.

## <a name="troubleshooting"></a>Устранение неполадок

Если возникли проблемы с установкой на Mac или Windows, вы можете выполнить установку вручную.

Для этого проверьте, установлен ли пакет OpenJDK на компьютере в правильном расположении:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

Если этот путь пустой, вы можете скачать один из следующих пакетов:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

Укажите в IDE новый пакет JDK:

- **Mac** &ndash; щелкните **Tools > SDK Manager > Locations** (Инструменты > Диспетчер пакетов SDK > Расположения) и в поле **Java SDK (JDK) Location** (Расположение пакета SDK для Java) укажите полный путь установки OpenJDK. В следующем примере указан такой путь: **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Настройка пути к пакету JDK под дистрибутив OpenJDK от Майкрософт для разработки мобильных приложений на Mac](openjdk-images/vsm.png)

- **Windows** &ndash; щелкните **Инструменты > Параметры > Xamarin > Параметры Android** и в поле **Расположение пакета Java Development Kit** укажите полный путь установки OpenJDK. В следующем примере указан такой путь: **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**.

![Настройка пути к пакету JDK под дистрибутив OpenJDK от Майкрософт для разработки мобильных приложений в ОС Windows](openjdk-images/vs.png)

## <a name="known-issues"></a>Известные проблемы

### <a name="package-openjdkv1regkeyversion1809chipx64-failed-to-install"></a>Не удалось установить пакет "OpenJDKV1.RegKey,version=1.8.0.9,chip=x64".

Возможно, возникла проблема в некоторых корпоративных средах. Пакет OpenJDK уже установлен на компьютере. Следуйте [шагам по устранению неполадок выше](#troubleshooting), чтобы указать в IDE правильное расположение. Следить за состоянием проблем можно [здесь](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html).

### <a name="unknown-update-type-zip-when-upgrading-visual-studio-for-mac-to-77"></a>Произошла ошибка "неизвестный тип обновления: zip" при обновлении Visual Studio для Mac до версии 7.7.

Эта проблема может возникать при попытке обновления более старой версии Visual Studio для Mac до 7.7. Чтобы обойти это, снимите флажок OpenJDK в средстве обновления, установите все остальные компоненты и снова проверьте наличие обновлений, чтобы скачать пакет OpenJDK. Если у вас все еще возникают проблемы, следуйте [шагам по устранению неполадок выше](#troubleshooting), чтобы установить пакет OpenJDK вручную. Если у вас возникает эта ошибка, отправьте запрос, прикрепив журналы.

## <a name="summary"></a>Сводка

В этой статье вы узнали, как настроить в IDE использование дистрибутива OpenJDK от Майкрософт для разработки мобильных приложений и как устранять неполадки.
