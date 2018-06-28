---
title: Приступая к работе с macOS Mojave
description: В этом документе описывается получение настроили macOS построения Mojave приложений с помощью Xamarin.Mac. Обсуждается Xcode 10 и обновите Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067395"
---
# <a name="getting-started-with-macos-mojave"></a>Приступая к работе с macOS Mojave

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Xamarin macOS поддержки Mojave сейчас в предварительной версии, поэтому он может содержать ошибки, не все компоненты и может измениться.
> Используйте для экспериментов только.

> [!NOTE]
> Дополнительные сведения см. в статье [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для Xamarin предварительного выпуска.

В этом документе описывается получение настроили macOS построения Mojave приложений с помощью Xamarin.Mac. Обсуждается Xcode 10 и обновите Visual Studio для Mac.

## <a name="download-and-install"></a>Загрузите и установите

1. **Установите последнюю бета-версию Xcode 10** — разработчики зарегистрированные Apple можно загрузить и установить последнюю версию Xcode 10 из [портала разработчиков Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS Mojave SDK поставляется вместе с Xcode 10.

2. **Запустить Xcode 10** — запустить Xcode 10 до обновления и запуска Visual Studio для Mac; он устанавливает некоторые средства, необходимые для Xamarin.

3. **Обновление Visual Studio для Mac** — следуйте инструкциям в [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для установки на предварительную версию Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию macOS Mojave на компьютере Mac** — для тестирования Xamarin.Mac приложений, использующих впервые представлена macOS Mojave API, зарегистрированных Apple разработчики могут [загрузки](https://developer.apple.com/download/) и установка последние macOS Mojave разработчика бета-версии.

   > [!TIP]
   > Даже если приложение не использует все новые macOS Mojave API-интерфейсы, нужно убедиться, что при создании macOS Mojave пакет SDK (в Xcode 10) и проверить его, чтобы убедиться, что все работает правильно. Если приложение не вызывает любые новые интерфейсы API, можно перекомпилировать с macOS Mojave SDK и проверить его без обновление операционной системы компьютер Mac.

   > [!IMPORTANT]
   > Перед установкой Mac macOS Mojave для построения и тестирования приложения Xamarin.Mac, вызывающие новый macOS Mojave API-интерфейсы:
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для Xamarin предварительного выпуска. Обратите внимание, это первая предварительная версия не содержит привязок для macOS новые интерфейсы API AppKit Mojave (например, Темная режим).

## <a name="related-links"></a>Связанные ссылки

- [Загрузить Xcode 10](https://developer.apple.com/download/)
- Предварительный просмотр Xamarin [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/)
