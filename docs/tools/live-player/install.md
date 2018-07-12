---
title: Установка Xamarin Live Player
description: В этом документе описывается, как установить Xamarin Live Player и использовать его для динамического изменения к запущенному приложению.
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: topgenorth
ms.author: toopge
ms.date: 05/14/2018
ms.openlocfilehash: 40c03e978cd9ce4666089f1b2a1e2ee8f47dbd81
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831677"
---
# <a name="xamarin-live-player-setup"></a>Установка Xamarin Live Player

Xamarin Live Player позволяет вносить изменения в реальном времени в приложении и воспроизводятся динамическую на устройстве. Ваш код выполняется внутри приложения Xamarin Live Player – здесь не нужно настроить эмуляторы или развертывание с помощью кабелей! В этой статье описывается, как настроить Xamarin Live Player.

![Функция предварительного просмотра](~/media/shared/preview.png)

## <a name="1-get-the-app"></a>1. Получить приложение

# <a name="androidtabandroid"></a>[Android](#tab/android)

Xamarin Live Player для Android в Google Play:

[ ![Доступное в Google Play](install-images/google-play-badge.png)](https://play.google.com/store/apps/details?id=com.xamarin.live)

Для устройств Android без Google Play Xamarin Live Player доступна через [HockeyApp](https://aka.ms/xlp-hockeyapp) распространения. Кроме того, предварительная версия сборки для Android можно установить непосредственно из Google Play с включением защиты для [общедоступной бета-версии программы](https://play.google.com/apps/testing/com.xamarin.live)

# <a name="iostabios"></a>[iOS](#tab/ios)

Мы рекомендуем пользователям присоединяться к iOS приложение Xamarin Live Player предварительного просмотра, чтобы оценить преимущества быстрого доступа к последним усовершенствованиям в TestFlight. Получая доступ к Xamarin Live Player, вы соглашаетесь в корпорации Майкрософт [условия использования](https://www.microsoft.com/en-us/legal/intellectualproperty/copyright/default.aspx) & [заявление о конфиденциальности](https://privacy.microsoft.com/en-us/privacystatement). Корпорация Майкрософт может использовать ваши контактные данные для предоставления обновлений и специальных предложений по Xamarin и других продуктов и служб Майкрософт. Вы можете отменить подписку в любое время.

Чтобы получить доступ к предварительной версии iOS Xamarin Live Player, заполните [сведения о регистрации TestFlight](https://fastring.xamarinliveplayer.com/), после чего вы получите сообщение электронной почты от TestFlight по установке iOS Xamarin Live Player предварительной версии.

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="2-get-visual-studio-2017"></a>2. Получить Visual Studio 2017

Требуется Xamarin Live Player:

- Visual Studio 2017 15.4 или более поздней версии.
- Компьютер с Visual Studio и устройство в той же сети Wi-Fi.

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. С помощью Xamarin Live Player в первый раз

1. Откройте **Visual Studio 2017**.
2. Перейдите к **Сервис > Параметры...**  и выберите **Xamarin > другие** вкладки.
3. Такт **включить Xamarin Live Player**:

  ![Установите флажок "Включить Xamarin Live Player" в окне «Параметры»](install-images/vs2017-options.png)

2. Создайте или откройте проект Xamarin (или [пример](~/tools/live-player/samples.md)).
3. Выберите **Live Player** в списке устройств:

  ![Список устройств включает параметр Xamarin Live Player](install-images/devices-empty-windows.png)

  * Если уже иметь пару устройство, он будет доступен в качестве параметра.
  * В противном случае вам будет предложено связать устройство, при необходимости.
4. Нажмите клавишу **запуска** кнопки, или выберите один из следующих параметров из **запуска** или контекстном меню:

  - **Запуск без отладки** — можно изменить приложение и изменениях на устройстве см. (приложение перезапускается при внесении изменений и сохранить файл).
  - **Запустите отладку** — можно установить точки останова и проверять значения переменных, но нельзя изменять код.

  Можно также выбрать **средства > Xamarin Live Player > Live текущего представления запуска**, который позволяет позволяет изменить приложения и изменениях на устройстве см. в разделе. Текущее представление отображается (вместо основной экран приложения).

5. Если устройство уже связан, и приложение Xamarin Live Player выполняется на устройстве, код будет выполняться немедленно!

  Если устройство не является объединяется, QR-код будет отображаться с инструкциями о том, как связывая устройство:

  ![Пара окно устройства](install-images/manage-empty-windows.png)

  Если устройства будут недоступны для связывания, может возникнуть ошибка.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

## <a name="2-get-visual-studio-for-mac"></a>2. Получить Visual Studio для Mac

Требуется Xamarin Live Player:

- OS X 10.11 macOS версии 10.12 или более поздней версии
- Visual Studio для Mac
- Mac и устройство в той же сети Wi-Fi

## <a name="3-using-xamarin-live-player-for-the-first-time"></a>3. С помощью Xamarin Live Player в первый раз

1. Откройте **Visual Studio для Mac**.
2. Перейдите к **Visual Studio > Параметры...**  и выберите **проектов > Xamarin Live Player (Предварительная версия)** вкладки.
3. Такт **включить Xamarin Live Player**:

  [![Установите флажок "Включить Xamarin Live Player" в окне «Параметры»](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

2. Создайте или откройте проект Xamarin (или [пример](~/tools/live-player/samples.md)).
3. Выберите **Live Player** в списке устройств.

  ![Список устройств включает параметр Xamarin Live Player](install-images/devices.png)

  * Если уже иметь пару устройство, он будет доступен в качестве параметра.
  * В противном случае вам будет предложено связать устройство, при необходимости.
  * Выберите **устройствами Xamarin Live Player...**  для управления устройствами, вы хотите использовать с Xamarin Live Player.

4. Нажмите клавишу **запуска** кнопки, или выберите один из следующих параметров из **запуска** или контекстном меню:

  ![Параметры меню запуска](install-images/run-menu.png)

  - **Запуск без отладки** — можно изменить приложение и изменениях на устройстве см. (приложение перезапускается при внесении изменений и сохранить файл).
  - **Запустите отладку** — можно установить точки останова и проверять значения переменных, но нельзя изменять код.
  - **Live текущего представления запуска** — можно изменить приложение и посмотрим, происходят изменения на устройстве. Текущее представление отображается (вместо основной экран приложения).

5. Если устройство уже связан, и приложение Xamarin Live Player выполняется на устройстве, код будет выполняться немедленно!

  Если устройство не объединяется, QR-код будет отображаться с инструкциями о том, как связать устройство:

  ![Пара окно устройства](install-images/manage-empty.png)

  Если устройства будут недоступны для связывания, появится сообщение об ошибке:

  ![Не удается подключиться к сообщение об ошибке устройства](install-images/error-cannot-connect.png)


-----

Если возникнут проблемы или не удается подключиться, см. в разделе [ограничения и устранение неполадок](~/tools/live-player/troubleshooting.md).


## <a name="related-links"></a>Связанные ссылки

- [Ограничения](~/tools/live-player/limitations.md)
- [Устранение неполадок](~/tools/live-player/troubleshooting.md)
- [Образцы Xamarin Live Player](~/tools/live-player/samples.md)
