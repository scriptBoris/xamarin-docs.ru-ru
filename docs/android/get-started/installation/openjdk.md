---
title: Предварительная версия дистрибутива OpenJDK от Майкрософт
description: Пошаговое руководство по настройке дистрибутива OpenJDK от Майкрософт.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 6c1346918ca6881e551f6c5b89ab16ad13d3f804
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242532"
---
# <a name="microsofts-openjdk-distribution-preview"></a>Предварительная версия дистрибутива OpenJDK от Майкрософт

_В этом руководстве описаны шаги по переходу на предварительную версию дистрибутива OpenJDK от Майкрософт._

![Предварительная версия](~/media/shared/preview.png)

## <a name="overview"></a>Обзор

Начиная с версий Visual Studio 15.9 и Visual Studio для Mac 7.7 средство "Инструменты Visual Studio для Xamarin" откажется от пакета JDK от Oracle и перейдет на упрощенную версию OpenJDK, предназначенную исключительно для разработки приложений для Android.

![Новый рабочий процесс, в котором предлагается предварительная веб-версия пакета OpenJDK в Visual Studio 15.8 (предварительная версия 5)](openjdk-images/openjdk.png)

Такой переход предлагает следующие преимущества:

- У вас всегда будет версия OpenJDK, которая подходит для разработки приложений Android.

- Загрузка пакета JDK версии 9 или 10 не влияет на процесс разработки.

- Значительно уменьшены размер скачиваемого файла и объем используемой памяти.

- Отсутствие потенциальных проблем со сторонними серверами и установщиками.

Если вы хотите побыстрее ощутить все преимущества, воспользуйтесь тестовой сборкой OpenJDK от Майкрософт для Windows или Mac. Процесс установки описан ниже. Вернуться на JDK от Oracle можно в любое время.

## <a name="download"></a>Скачивание

Для начала скачайте правильную сборку для своей системы.

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>Настройка

Распакуйте файл в правильное расположение:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> В этом примере используется сборка 1.8.0.9, но на момент скачивания может быть доступна более новая версия.

Укажите в IDE новый пакет JDK:

- **Mac** &ndash; щелкните **Tools > SDK Manager > Locations** (Инструменты > Диспетчер пакетов SDK > Расположения) и в поле **Java SDK (JDK) Location** (Расположение пакета SDK для Java) укажите полный путь установки OpenJDK. В следующем примере указан такой путь: **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Настройка пути к пакету JDK для дистрибутива OpenJDK от Майкрософт в ОС Mac](openjdk-images/vsm.png)

- **Windows** &ndash; щелкните **Инструменты > Параметры > Xamarin > Параметры Android** и в поле **Расположение пакета Java Development Kit** укажите полный путь установки OpenJDK. В следующем примере указан такой путь: **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**.

![Настройка пути к пакету JDK для дистрибутива OpenJDK от Майкрософт в ОС Windows](openjdk-images/vs.png)

## <a name="revert"></a>Переход к предыдущей версии

Чтобы вернуться к Oracle JDK, измените расположение пакета SDK для Java, указав предыдущий путь к пакету JDK от Oracle, и повторно скомпилируйте решение. На компьютере Mac восстановить путь к Oracle JDK можно нажатием кнопки **Reset to Defaults** (Сбросить к значениям по умолчанию).

Если с дистрибутивом OpenJDK от Майкрософт возникают какие-либо проблемы, сообщайте о них с помощью средства обратной связи в среде IDE. Так мы сможем оперативно их отследить и исправить.

## <a name="known-issues--planned-fix-dates"></a>Известные проблемы и даты плановых исправлений

Переменная среды `JAVA_HOME` может неправильно экспортироваться в пакет SDK и диспетчер устройств. В качестве временного решения для этой переменной можно задать расположение пакета OpenJDK на компьютере. Эта проблема будет исправлена в предварительной версии 15.9.

## <a name="summary"></a>Итоги

Из этой статьи вы узнали, как настроить в IDE предварительную версию дистрибутива OpenJDK от Майкрософт. Стабильная версия дистрибутива появится ближе к концу 2018 года.
