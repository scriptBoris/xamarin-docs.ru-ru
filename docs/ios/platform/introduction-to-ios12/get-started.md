---
title: Начало работы с iOS 12, tvOS 12 и watchOS 5
description: В этом документе описывается, как получить настройки предусматривают сборки iOS 12 и приложений tvOS 12 с помощью Xamarin. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 70f67f934c2503e6f6fa0d3bae1f37bcc1f6f0a4
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030656"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>Начало работы с iOS 12, tvOS 12 и watchOS 5

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Xamarin iOS 12, tvOS 12, и поддержка watchOS 5 в настоящее время в предварительной версии, что означает, что он может содержать ошибки, не все компоненты, и может измениться. Используйте его только для экспериментов.

> [!NOTE]
> Дополнительные сведения см. предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

В этом документе описывается процедура настройки до построения iOS 12, tvOS 12 и watchOS 5 приложений с помощью Xamarin. Он описывает, как загрузка Xcode 10 и обновление Visual Studio для Mac и Visual Studio 2017.

## <a name="download-and-install"></a>Загрузка и установка

1. **Установить последнюю бета-версию Xcode 10** — разработчики регистрации Apple можно загрузить и установить последнюю версию Xcode 10 из [портал разработчиков Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > IOS 12, tvOS 12 и watchOS 5 SDK распределяются с Xcode 10.

2. **Запустите Xcode 10** — запустить Xcode 10 до обновления и запуска Visual Studio для Mac или Visual Studio 2017; он устанавливает некоторые средства, необходимые для Xamarin.

3. **Обновление Visual Studio для Mac и Visual Studio 2017** — следуйте инструкциям на [выпуска блог](https://releases.xamarin.com/preview-release-xcode-10-beta-3/) Установка предварительной версии Xamarin.

4. _(необязательно)_  **Установить последнюю бета-версию iOS на устройствах iOS** — для тестирования приложений устройства, использование впервые представлена iOS 12, tvOS 12 или watchOS 5 API-интерфейсы, зарегистрированные разработчики Apple можно [загрузить](https://developer.apple.com/download) и Установка последней версии iOS 12, tvOS 12 или бета-версии для watchOS 5 разработчиков на своих устройствах.

   > [!TIP]
   > Даже если приложение использует любой новый iOS 12, tvOS 12 или watchOS 5 API-интерфейсы, убедитесь, что оно создано с помощью iOS 12, tvOS 12 или watchOS 5 пакет SDK (с Xcode 10) и тестирования его, чтобы убедиться в том, что все работает как ожидалось. Если приложение не вызывает все новые интерфейсы API, можно выполнить перекомпиляцию с iOS 12, tvOS 12 или watchOS 5 SDK и протестировать его на устройствах, которые еще не были обновлены до новых операционных систем.

   > [!IMPORTANT]
   > Перед обновлением устройства iOS 12, tvOS 12 или watchOS 5 для тестирования приложений Xamarin, которые вызывают новый iOS 12, tvOS 12 или watchOS 5 API-интерфейсы:
   > - Чтение [заметки о выпуске Apple](https://developer.apple.com/download/) для обновления операционной системы.
   > - Чтение предварительной версии Xamarin [выпуске блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

## <a name="related-links"></a>Связанные ссылки

- [Скачать Xcode 10](https://developer.apple.com/download/)
- Предварительная версия Xamarin [выпуска записи блога](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
