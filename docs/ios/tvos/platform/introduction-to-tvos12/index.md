---
title: Общие сведения о tvOS 12
description: Данный документ содержит общие сведения о новых и обновленных функциях в tvOS 12 какие Xamarin предварительной версии в настоящее время предоставляет привязки C#.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067380"
---
# <a name="introduction-to-tvos-12"></a>Общие сведения о tvOS 12

![Предварительный просмотр](~/media/shared/preview.png)

> [!WARNING]
> Поддержка 12 tvOS Xamarin в настоящее время в предварительной версии, что означает, что она может содержать ошибки, не все компоненты, и может измениться. Используйте для экспериментов только.

> [!NOTE]
> - Просмотрите [Приступая к работе](~/ios/platform/introduction-to-ios12/get-started.md) руководстве инструкции о том, как приступить к созданию iOS 12 и tvOS 12 приложений с помощью Xamarin.
> - Дополнительные сведения см. в статье [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/) для Xamarin предварительного выпуска.

Этот документ содержит высокоуровневые сведения о новых и обновленных tvOS 12 возможности для предварительного просмотра какой Xamarin выпуска в настоящее время привязки C#.

## <a name="password-autofill"></a>Автозаполнение паролей

С tvOS 12 пользователи могут использовать свои устройства iOS для входа в приложение tvOS одним касанием. Это осуществляется за счет сочетания `UITextContentType` полей использования для указания имени пользователя и пароля, связанных доменов для установления связи между приложения iOS и приложение tvOS и предпочтительный фокус среды, выберите элемент, чтобы получить фокус после пользователем содержит имя пользователя и пароль.

## <a name="focus-engine-enhancements"></a>Усовершенствования механизма фокуса

tvOS 12 позволяет всем приложениям, независимо от того, как они отображаются, для взаимодействия с подсистемой фокус. При взаимодействии пользователя с удаленной Siri подсистема фокус можно в любом приложении выберите элемент и некоторые изменения фокуса возможных естественным образом обновить фокус. Этот параметр доступен в пользовательских приложениях через его UIKit `IUIFocusItemContainer` интерфейс, `UIFocusMovementHint` класса, `IUIFocusItemScrollableContainer` интерфейс и другие связанные классы и методы.

## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS — разработчиков Apple (Apple)](https://developer.apple.com/tvos/)
- [Новые возможности tvOS 12 (Apple) (видео)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [ТВ (Apple)](https://www.apple.com/tv/)
- Предварительный просмотр Xamarin [заметки о выпуске](https://releases.xamarin.com/preview-release-xcode-10-beta/)
