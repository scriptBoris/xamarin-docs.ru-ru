---
title: Приступая к работе с macOS Mojave
description: В этом документе описывается, как получить настройки предусматривают сборки macOS Mojave приложений с помощью Xamarin.Mac. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831349"
---
# <a name="getting-started-with-macos-mojave"></a>Приступая к работе с macOS Mojave

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Xamarin в macOS Mojave поддержка доступна в предварительной версии, это означает, что его могут содержаться ошибки, не является готовой функцией и может измениться.
> Используйте его только для экспериментов.

> [!NOTE]
> Дополнительные сведения в статье [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) предварительной версии выпуска для Xamarin.

В этом документе описывается, как получить настройки предусматривают сборки macOS Mojave приложений с помощью Xamarin.Mac. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac.

## <a name="download-and-install"></a>Загрузка и установка

1. **Установить последнюю бета-версию Xcode 10** — разработчики регистрации Apple можно загрузить и установить последнюю версию Xcode 10 из [портал разработчиков Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS Mojave SDK входит в состав Xcode 10.

2. **Запустите Xcode 10** — запустить Xcode 10 до обновления и запуска Visual Studio для Mac; он устанавливает некоторые средства, необходимые для Xamarin.

3. **Обновление Visual Studio для Mac** — следуйте инструкциям в [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) Установка предварительной версии Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию macOS Mojave на компьютере Mac** — для тестирования приложений Xamarin.Mac, которые впервые представлена macOS Mojave API, зарегистрированных Apple разработчики могут использовать [загрузить](https://developer.apple.com/download/) и установить последнюю macOS Mojave разработчика бета-версию.

   > [!TIP]
   > Даже если приложение использует любой новый macOS Mojave API-интерфейсы, убедитесь, что для сборки с помощью macOS Mojave пакет SDK (с Xcode 10), а также протестировать его, чтобы убедиться в том, что все работает правильно. Если приложение не вызывает все новые интерфейсы API, можно перекомпилировать ее с macOS Mojave SDK и проверить его без обновить операционную систему компьютера Mac.

   > [!IMPORTANT]
   > Перед обновлением компьютера Mac для macOS Mojave, позволяющие создавать и тестировать приложения Xamarin.Mac, которые вызывают новый macOS Mojave API-интерфейсы:
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) предварительной версии выпуска для Xamarin. Обратите внимание на то, что это первая предварительная версия не поддерживает привязки для новых macOS Mojave AppKit API (например, темно-режим).

## <a name="related-links"></a>Связанные ссылки

- [Скачать Xcode 10](https://developer.apple.com/download/)
- Предварительная версия Xamarin [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/)
