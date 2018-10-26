---
title: Какие пакеты Android SDK следует установить?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F136AAE0-C6D2-4B0F-8F8C-7A6A94877266
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 04a07d5b7f37222515136e5592f31a4583b02fe3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107538"
---
# <a name="which-android-sdk-packages-should-i-install"></a>Какие пакеты Android SDK следует установить?

Установка пакета SDK для Android не включает автоматически все минимальный набор обязательных пакетов для разработки. Хотя индивидуальный разработчик должен изменяться, следующие пакеты обычно будут необходимы для разработки с помощью Xamarin.Android:

## <a name="tools"></a>Инструменты

Установите новейшие средства из папки Tools в диспетчер пакетов SDK:

- Android SDK Tools
- Пакет инструментов платформы Android SDK
- Средства сборки пакета SDK для Android

## <a name="android-platforms"></a>Платформы Android

Установите «Платформа SDK» для Android версий, которые заданы как минимум & целевой объект. 

Примеры

- Целевой API 23
- Минимальное API 23

Необходимо установить пакет SDK для платформы для API 23 только

- Целевой API 23
- Минимальное API 15

Необходимо установить пакет SDK для платформы для API 15 и 23. Обратите внимание, что не нужно установить уровни API между минимальным и целевой объект (даже если вы являетесь backporting на этих уровнях API).

## <a name="system-images"></a>Образы системы

Это только требуется, если вы хотите использовать out of box эмуляторов Android от Google. Дополнительные сведения см. в разделе [Настройка эмулятора Android](~/android/get-started/installation/android-emulator/index.md)

## <a name="extras"></a>Дополнения
Android SDK дополнения обычно не являются обязательными; но он полезен, которые следует учитывать их, так как они могут потребоваться в зависимости от ваш вариант использования.

## <a name="further-reading"></a>Дополнительные сведения
В этом руководстве рассматриваются эти возможности и содержит более подробные сведения о различных пакетах SDK manager имеет доступного: [руководство по установке диспетчера пакетов SDK для Android](http://www.themethodology.net/2015/02/android-sdk-manager-setup-for.html?m=1)

