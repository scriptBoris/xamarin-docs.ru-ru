---
title: Начало работы с iOS 12 и tvOS 12
description: В этом документе описывается получение задано до построения iOS 12 и tvOS 12 приложений с помощью Xamarin. Он описывается загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017 г.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 887a5cc72b951b2f115cdc6998a6cf6ca7a94df0
ms.sourcegitcommit: 4939748d537cbb1934a3b5b22add39ef3d9aa64d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37091562"
---
# <a name="getting-started-with-ios-12-and-tvos-12"></a>Начало работы с iOS 12 и tvOS 12

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 12 и tvOS 12 поддержки iOS в настоящее время в предварительной версии, что означает, что она может содержать ошибки, не все компоненты, и может измениться. Используйте для экспериментов только.

> [!NOTE]
> Дополнительные сведения см. в статье [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для Xamarin предварительного выпуска.

В этом документе описывается получение задано до построения iOS 12 и tvOS 12 приложений с помощью Xamarin. Он описывается загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017 г.

## <a name="download-and-install"></a>Загрузите и установите

1. **Установите последнюю бета-версию Xcode 10** — разработчики зарегистрированные Apple можно загрузить и установить последнюю версию Xcode 10 из [портала разработчиков Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > IOS 12 и пакеты SDK tvOS 12 распространяются с помощью Xcode 10.

2. **Запустить Xcode 10** — запустить Xcode 10 до обновления и запуска Visual Studio для Mac или Visual Studio 2017 г.; он устанавливает некоторые средства, необходимые для Xamarin.

3. **Обновление Visual Studio для Mac и Visual Studio 2017 г** — следуйте инструкциям в [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для установки на предварительную версию Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию iOS на устройствах iOS** — для тестирования приложений устройства, используйте впервые представлена iOS 12 или tvOS 12 API-интерфейсы, зарегистрированные разработчики Apple можно [загрузки](https://developer.apple.com/download) и установка последние iOS 12 или пробные tvOS 12 разработчика на устройствах iOS и tvOS.

   > [!TIP]
   > Даже если приложение не использует все новые iOS 12 или tvOS 12 API-интерфейсы, убедитесь, что при создании iOS 12 или tvOS 12 пакет SDK (в Xcode 10) и тест его, чтобы убедиться в том, что все работает как ожидалось. Если приложение не вызывает любые новые интерфейсы API, можно выполнить перекомпиляцию с iOS 12 или tvOS 12 SDK и проверить его на устройствах, которые еще не были обновлены до новых операционных систем.

   > [!IMPORTANT]
   > Перед обновлением устройства iOS 12 или tvOS 12 для тестирования приложений Xamarin, которые вызывают новый iOS 12 или tvOS 12 API-интерфейсы:
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для Xamarin предварительного выпуска.

## <a name="related-links"></a>Связанные ссылки

- [Загрузить Xcode 10](https://developer.apple.com/download/)
- Предварительный просмотр Xamarin [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/)
