---
title: Профили подготовки для приложений Xamarin.Mac
description: В этом руководстве описывается создание необходимых профилей подготовки, которые потребуются для публикации приложения Xamarin.Mac.
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: 9660d5373cc5213f648b145ef38ddbf25c7633ce
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792155"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Профили подготовки для приложений Xamarin.Mac

С помощью профилей подготовки разработчик может добавлять в свои приложения Xamarin.Mac несколько определенных возможностей macOS (прежнее название — Mac OS X), таких как iCloud и push-уведомления. Разработчик должен создать, скачать и установить профиль подготовки Mac для каждого создаваемого приложения, в котором используются указанные функции.

[![](profiles-images/certif13.png "Портал подготовки Apple")](profiles-images/certif13.png#lightbox)

<a name="Development_Provisioning_Profile" />

## <a name="development-provisioning-profile"></a>Профиль подготовки для разработки

Профиль подготовки для разработки позволяет тестировать приложения, предназначенные для Mac App Store, на определенных компьютерах, которые были настроены в профиле. Это особенно важно при использовании таких функций macOS, как iCloud и push-уведомления.

> [!NOTE]
> Перед созданием профиля подготовки для разработки необходимо иметь уже созданный сертификат разработки Mac. Чтобы сформировать **профиль подготовки для разработки**, используемый для создания сборок, выполните действия, показанные на снимке экрана. Должен существовать допустимый сертификат разработки Mac, доступный для выбора в поле **Сертификат**, и, по меньшей мере, одна система, зарегистрированная для тестирования.

Выполните следующие действия:

1. Выберите тип создаваемого профиля подготовки и нажмите кнопку **Продолжить**: 

     [![](profiles-images/certif14.png "Выбор типа профиля")](profiles-images/certif14.png#lightbox)
2. Выберите идентификатор приложения, для которого создается профиль, и нажмите кнопку **Продолжить**: 

     [![](profiles-images/certif15.png "Выбор идентификатора приложения")](profiles-images/certif15.png#lightbox)
3. Выберите идентификатор разработчика, используемый для подписывания профиля, и нажмите кнопку **Продолжить**: 

     [![](profiles-images/certif16.png "Выбор идентификатора разработчика")](profiles-images/certif16.png#lightbox)
4. Выберите компьютеры, на которых можно использовать этот профиль, и нажмите кнопку **Продолжить**: 

     [![](profiles-images/certif17.png "Выбор разрешенных компьютеров")](profiles-images/certif17.png#lightbox)
5. Введите значение в поле **Имя профиля** и нажмите кнопку **Создать**: 

     [![](profiles-images/certif18.png "Создание профиля")](profiles-images/certif18.png#lightbox)
6. Нажмите кнопку **Скачать**, чтобы скачать новый профиль: 

     [![](profiles-images/certif19.png "Скачивание профиля")](profiles-images/certif19.png#lightbox)
7. Профили подготовки для разработки устанавливаются в области "Параметры профилей" в **системных настройках** приложения Mac: 

     [![](profiles-images/certif20.png "Установка профиля")](profiles-images/certif20.png#lightbox)
8. Все установленные профили будут отображены в области "Параметры профилей": 

     [![](profiles-images/image47.png "Отображение всех установленных профилей")](profiles-images/image47.png#lightbox)
9. Если профиль необходимо скачать повторно, он также появится в **служебной программе для сертификата разработчика**: 

     [![](profiles-images/image48.png "Служебная программа для сертификата разработчика")](profiles-images/image48.png#lightbox)

Профиль подготовки для разработки требуется создавать для каждого нового приложения или при добавлении нового компьютера для тестирования.

<a name="Production_Provisioning_Profile" />

## <a name="production-provisioning-profile"></a>Профиль подготовки для производства

Профили подготовки для производства необходимы для создания пакета для отправки в Mac App Store.

Выполните следующие действия:

1. Выберите тип создаваемого профиля и нажмите кнопку **Продолжить**: 

    [![](profiles-images/certif21.png "Выбор типа профиля")](profiles-images/certif21.png#lightbox)
2. Выберите идентификатор приложения, для которого создается профиль, и нажмите кнопку **Продолжить**: 

    [![](profiles-images/certif15.png "Выбор идентификатора приложения")](profiles-images/certif15.png#lightbox)
3. Выберите идентификатор компании, используемый для подписывания профиля, и нажмите кнопку **Продолжить**: 

    [![](profiles-images/certif23.png "Выбор идентификатора компании")](profiles-images/certif23.png#lightbox)
4. Введите значение в поле **Имя профиля** и нажмите кнопку **Создать**: 

    [![](profiles-images/certif24.png "Создание профиля")](profiles-images/certif24.png#lightbox)
5. Нажмите кнопку **Скачать**, чтобы получить файл профиля подготовки (расширение `.provisionprofile`): 

    [![](profiles-images/certif25.png "Скачивание профиля")](profiles-images/certif25.png#lightbox)
6. Перетащите его в **организатор Xcode** или дважды щелкните его, чтобы установить. Затем профиль появится в организаторе Xcode: 

    [![](profiles-images/image51.png "Установка профиля")](profiles-images/image51.png#lightbox)
7. Профиль подготовки также появится в списке: 

    [![](profiles-images/certif26.png "Отображение установленных профилей")](profiles-images/certif26.png#lightbox)


В случае изменения функций, используемых идентификатором приложения (например, включение iCloud или push-уведомлений), следует повторно создать профили подготовки для этого идентификатора приложения.

## <a name="related-links"></a>Связанные ссылки

- [Установка](~//mac/get-started/installation.md)
- [Пример кода приложения "Привет, Mac"](~//mac/get-started/hello-mac.md)
- [Распространение приложений в Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Руководство по работе с инструментами. Подписывание кода приложения](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [Идентификатор разработчика и привратник](https://developer.apple.com/resources/developer-id/)
