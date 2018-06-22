---
title: Настройка вашего tvOS приложения в iTunes Connect
description: В этой статье содержится дополнительное руководство по iOS Настройка приложения в iTunes Connect для определенных конфигураций tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30779830"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Настройка вашего tvOS приложения в iTunes Connect

_В этой статье содержится дополнительное руководство по iOS Настройка приложения в iTunes Connect для определенных конфигураций tvOS._


В дополнение к конфигурации и параметры, необходимо будет сделать, выполнив iOS [настройки приложения в iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) руководство, в этом документе рассматриваются определенные конфигурации, которые должны будут выпуска Xamarin.tvOS приложение в магазине приложений Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Добавление tvOS окончательной версии

Создается новое приложение в магазине приложений Apple TV снятия или добавление поддержки Apple TV для существующего приложения iOS, вам потребуется создания iTunes Connect записи и настройки его с помощью следующих iOS проводит относящиеся.

- [Создание записи iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Управление видео и снимками экрана приложения](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Управление именем, описанием, сведениями о новых возможностях, ключевыми словами и URL-адресами](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Общие сведения](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Кроме того может также потребоваться:

- [Сведения Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Сведения о покупках из приложения](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Все указанные выше шаги завершена откройте iTunes Connect запись приложения и установите, чтобы добавить поддержку tvOS, с помощью левой боковой панели:

[![](itunes-connect-images/connect01.png "Добавление поддержки tvOS, с помощью левой боковой панели")](itunes-connect-images/connect01.png#lightbox)

Экраны tvOS определенные сведения будут доступны для данного iTunes Connect записи:

[![](itunes-connect-images/connect02.png "На экране tvOS подробные сведения")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS сведения о версии

В боковой панели слева выберите **1.0 Подготовка к отправке** tvOS приложения в разделе:

[![](itunes-connect-images/connect03.png "tvOS сведения о версии")](itunes-connect-images/connect03.png#lightbox)

На этом экране укажите следующие сведения:

- Требуется снимки экрана, описание, ключевые слова и URL-адреса.
- Приложения Общие сведения, такие как номер версии, авторские права и возрастной оценки.
- Необязательный покупки из приложений.
- Поддержка необязательно Game Center с списки лидеров и достижения.
- Необходимые сведения проверки приложения например контакта, демонстрация учетные записи и заметки.

После ввода необходимых сведений нажмите кнопку **Сохранить** кнопку в правом верхнем углу экрана, чтобы сохранить изменения:

[![](itunes-connect-images/connect04.png "tvOS готов для отправки сведений о версии")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Подготовка для отправки на рассмотрение

Когда все готово для отправки приложения в магазине приложений Apple TV для просмотра Xamarin.tvOS вернуться в приложение iTunes Connect записи и нажмите кнопку **отправьте на рассмотрение** кнопку в правом верхнем углу экрана:

[![](itunes-connect-images/connect05.png "Отправьте на рассмотрение")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье предлагает обзор конкретный параметр tvOS, требуемые в iTunes Connect для выпуска tvOS приложения для Apple TV App Store.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS человека направляющие интерфейса](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
