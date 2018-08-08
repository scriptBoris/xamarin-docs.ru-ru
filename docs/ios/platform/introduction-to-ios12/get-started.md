---
title: Начало работы с iOS 12, tvOS 12 и watchOS 5
description: В этом документе описывается процедура настройки до построения iOS 12, tvOS 12 и watchOS 5 приложений с помощью Xamarin. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/07/2018
ms.openlocfilehash: cb84ddc646933d253ca72fe8f9f581364aab698b
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615178"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>Начало работы с iOS 12, tvOS 12 и watchOS 5

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Поддержка Xamarin для iOS 12, tvOS 12 и watchOS 5 пакетов SDK, распространяемые с Xcode 10 доступна в предварительной версии, который означает, что он может содержать ошибки, не функцию завершения, которое может измениться. Используйте его только для экспериментов.

В этом документе описывается, как выполнить настройки для создания приложений Xamarin, которые вызывают выпущенных интерфейсах API с Xcode 10. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017.

## <a name="download-and-install"></a>Загрузка и установка

1. **Установить последнюю бета-версию Xcode 10** — разработчики регистрации Apple можно загрузить и установить последнюю версию Xcode 10 из [портал разработчиков Apple](https://developer.apple.com/download/).

2. **Запустите Xcode 10** — необходимо выполнить до обновления и работе с Visual Studio для Mac или Visual Studio 2017, так как он устанавливает некоторые средства, Xamarin 10 Xcode.

3. **Обновление Visual Studio для Mac и Visual Studio 2017** — следуйте инструкциям на [выпуска блог](https://releases.xamarin.com/preview-release-xcode-10-beta-5/) Установка предварительной версии Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию iOS на устройствах iOS** — для тестирования приложений, использующих API, появившихся с Xcode 10, зарегистрированных can разработчиков Apple устройства [загрузить](https://developer.apple.com/download) и установите последнюю версию Разработчик бета-версии на своих устройствах.

   > [!TIP]
   > Даже если приложение не использует все новые интерфейсы API, убедитесь, что для сборки с пакетами SDK последней Xcode 10, а также протестировать его, чтобы убедиться в том, что все работает правильно. Если приложение не вызывает все новые интерфейсы API, его можно перекомпилировать ее с этих новых пакетов SDK и тестирования на устройствах, которые еще не были обновлены до новой операционной системы.
   >
   > Прежде чем обновление устройств до последнюю операционную систему, освобождает от Apple для тестирования приложений Xamarin, обязательно сделайте следующее:
   >
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-5/).

## <a name="related-links"></a>Связанные ссылки

- [Скачать Xcode 10](https://developer.apple.com/download/)
- Предварительная версия Xamarin [выпуска записи блога](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)
