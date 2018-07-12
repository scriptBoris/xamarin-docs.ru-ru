---
title: Введение в tvOS 12
description: Этот документ предоставляет высокоуровневый обзор новых и обновленных функций в tvOS 12 какие Xamarin предварительной версии в настоящее время предоставляет привязки C#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38847565"
---
# <a name="introduction-to-tvos-12"></a>Введение в tvOS 12

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Поддержка 12 tvOS Xamarin в настоящее время в предварительной версии, что означает, что он может содержать ошибки, не все компоненты, и могут измениться. Используйте его только для экспериментов.

> [!NOTE]
> - Просмотрите [Приступая к работе](~/ios/platform/introduction-to-ios12/get-started.md) инструкции о том, как приступить к созданию приложения tvOS 12 с помощью Xamarin iOS 12 и руководстве.
> - Дополнительные сведения в статье [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) предварительной версии выпуска для Xamarin.

Этот документ содержит общий обзор новых и обновленных tvOS 12 функции для предварительного просмотра какой Xamarin выпуск в настоящее время предоставляет привязки C#.

## <a name="password-autofill"></a>Автозаполнение пароль

С помощью tvOS 12 пользователи могут использовать свои устройства с iOS для входа в приложение tvOS одним касанием. Это обеспечивается за счет сочетания `UITextContentType` поля использования, чтобы указать имя пользователя и пароль, связанные домены для установления отношений между приложения iOS и приложение tvOS и предпочтительным фокус среды, выберите элемент, чтобы получать фокус после выполнения Указывает имя пользователя и пароль.

## <a name="focus-engine-enhancements"></a>Усовершенствования системы фокус

tvOS 12 позволяет всем приложениям, независимо от того, как они будут отображаться, взаимодействовать с ядром фокус. При взаимодействии пользователя с удаленной Siri ядро фокус можно с помощью любого приложения выберите элемент и некоторые изменения фокуса можно естественным образом обновить фокус. Эта функция включена в пользовательских приложениях через UIKit `IUIFocusItemContainer` интерфейс, `UIFocusMovementHint` класс, `IUIFocusItemScrollableContainer` интерфейс и другие связанные классы и методы.

## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS — разработчика Apple (Apple)](https://developer.apple.com/tvos/)
- [Новые возможности в tvOS 12 (Apple) (видео)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [ТВ (Apple)](https://www.apple.com/tv/)
- Предварительная версия Xamarin [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/)
