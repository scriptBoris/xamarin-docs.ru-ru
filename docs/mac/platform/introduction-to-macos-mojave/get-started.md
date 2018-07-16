---
title: Приступая к работе с macOS Mojave
description: В этом документе описывается, как получить настройки предусматривают сборки macOS Mojave приложений с помощью Xamarin.Mac. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: cf2725eafa18330a07a08db4235bad1a1ecd47b6
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030630"
---
# <a name="getting-started-with-macos-mojave"></a>Приступая к работе с macOS Mojave

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Xamarin в macOS Mojave поддержка доступна в предварительной версии, это означает, что его могут содержаться ошибки, не является готовой функцией и может измениться.
> Используйте его только для экспериментов.

> [!NOTE]
> Дополнительные сведения см. предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

В этом документе описывается, как получить настройки предусматривают сборки macOS Mojave приложений с помощью Xamarin.Mac. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac.

## <a name="download-and-install"></a>Загрузка и установка

1. **Установить последнюю бета-версию Xcode 10** — разработчики регистрации Apple можно загрузить и установить последнюю версию Xcode 10 из [портал разработчиков Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > MacOS Mojave SDK входит в состав Xcode 10.

2. **Запустите Xcode 10** — запустить Xcode 10 до обновления и запуска Visual Studio для Mac; он устанавливает некоторые средства, необходимые для Xamarin.

3. **Обновление Visual Studio для Mac** — следуйте инструкциям на [выпуска блог](https://releases.xamarin.com/preview-release-xcode-10-beta-3/) Установка предварительной версии Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию macOS Mojave на компьютере Mac** — для тестирования приложений Xamarin.Mac, которые впервые представлена macOS Mojave API, зарегистрированных Apple разработчики могут использовать [загрузить](https://developer.apple.com/download/) и установить последнюю macOS Mojave разработчика бета-версию.

   > [!TIP]
   > Даже если приложение использует любой новый macOS Mojave API-интерфейсы, убедитесь, что для сборки с помощью macOS Mojave пакет SDK (с Xcode 10), а также протестировать его, чтобы убедиться в том, что все работает правильно. Если приложение не вызывает все новые интерфейсы API, можно перекомпилировать ее с macOS Mojave SDK и проверить его без обновить операционную систему компьютера Mac.

   > [!IMPORTANT]
   > Перед обновлением компьютера Mac для macOS Mojave, позволяющие создавать и тестировать приложения Xamarin.Mac, которые вызывают новый macOS Mojave API-интерфейсы:
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

## <a name="related-links"></a>Связанные ссылки

- [Скачать Xcode 10](https://developer.apple.com/download/)
- Предварительная версия Xamarin [выпуска записи блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
