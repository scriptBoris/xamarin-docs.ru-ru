---
title: Начало работы с iOS 12, tvOS 12 и watchOS 5
description: В этом документе описывается процедура настройки до построения iOS 12, tvOS 12 и watchOS 5 приложений с помощью Xamarin. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2018
ms.openlocfilehash: 77589d0d644c366fc0feacd874929c7456b4ae30
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615178"
---
# <a name="get-started-with-ios-12-tvos-12-and-watchos-5"></a>Начало работы с iOS 12, tvOS 12 и watchOS 5

В этом документе описывается, как приступить к созданию приложения Xamarin, которые вызывают выпущенных интерфейсах API с Xcode 10 для iOS 12, tvOS 12 и watchOS 5.

## <a name="download-and-install"></a>Загрузка и установка

1. **Установить Xcode 10** — разработчики регистрации Apple можно загрузить и установить последнюю версию Xcode 10 из [портал разработчиков Apple](https://developer.apple.com/download/) или **App Store**.

2. **Запустите Xcode 10** — необходимо выполнить до обновления и работе с Visual Studio для Mac или Visual Studio 2017, так как он устанавливает некоторые средства, Xamarin 10 Xcode.

3. **Обновление Visual Studio для Mac и Visual Studio 2017** — убедитесь, что у вас есть последняя стабильная версия Xamarin.

4. _(необязательно)_  **Установите iOS 12 на устройствах iOS** —

   Для приложений, которые используют API, появившихся с Xcode 10 тестирование устройств, зарегистрированных разработчиков Apple можно [загрузить](https://developer.apple.com/download) и установите операционную систему на своих устройствах.

   > [!TIP]
   > Даже если приложение не использует все новые интерфейсы API, убедитесь, что для сборки с пакетами SDK последней Xcode 10, а также протестировать его, чтобы убедиться в том, что все работает правильно. Если приложение не вызывает все новые интерфейсы API, его можно перекомпилировать ее с этих новых пакетов SDK и тестирования на устройствах, которые еще не были обновлены до новой операционной системы.
   >
   > Прежде чем обновление устройств до последнюю операционную систему, освобождает от Apple для тестирования приложений Xamarin, обязательно сделайте следующее:
   >
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-6/).

## <a name="related-links"></a>Связанные ссылки

- [Скачать Xcode 10](https://developer.apple.com/download/)
