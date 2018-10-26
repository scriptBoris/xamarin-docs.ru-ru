---
title: Настройка приложения tvOS в iTunes Connect
description: Эта статья содержит дополнительное руководство по iOS Настройка приложения в iTunes Connect для конкретных конфигураций tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108825"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Настройка приложения tvOS в iTunes Connect

_Эта статья содержит дополнительное руководство по iOS Настройка приложения в iTunes Connect для конкретных конфигураций tvOS._


В дополнение к конфигурации и параметр, который необходимо сделать, следуя iOS [Настройка приложения в iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) руководство, в этом документе рассматриваются определенные конфигурации, которые нужно будет освободить Xamarin.tvOS приложение в App Store Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Добавление tvOS окончательной версии

Вы создаете новое приложение к освобождению на Apple TV App Store или добавление поддержки Apple TV в существующее приложение iOS, вам потребуется после создания iTunes Connect записи и настройки его с помощью следующих iOS проводит конкретных.

- [Создание записи iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Управление видео и снимками экрана приложения](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Управление именем, описанием, сведениями о новых возможностях, ключевыми словами и URL-адресами](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Общие сведения о](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

При необходимости может также потребоваться:

- [Сведения Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Сведения о покупках из приложения](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Все описанные выше действия завершения откройте запись iTunes Connect и выберите, чтобы добавить поддержку tvOS, использование левой боковой панели приложения:

[![](itunes-connect-images/connect01.png "Добавлена поддержка tvOS, с помощью боковой панели слева")](itunes-connect-images/connect01.png#lightbox)

Экраны конкретные сведения tvOS, становятся доступными для данной записи iTunes Connect:

[![](itunes-connect-images/connect02.png "На экране сведения tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>сведения о версии tvOS

В боковой панели слева выберите **1.0 Подготовка к отправке** приложения tvOS в разделе:

[![](itunes-connect-images/connect03.png "сведения о версии tvOS")](itunes-connect-images/connect03.png#lightbox)

На этом экране укажите следующие сведения:

- Необходимые снимки экрана, описание, ключевые слова и URL-адреса.
- Общие сведения о приложении, такие как номер версии, авторские права и возрастную категорию.
- Необязательный покупки из приложений.
- Поддержка необязательно Game Center с списки лидеров и достижений.
- Необходимые сведения о проверке приложения, такие как контакт, демонстрационные учетные данные и примечания.

После ввода необходимых сведений, щелкните **Сохранить** кнопки в правом верхнем углу экрана, чтобы сохранить изменения:

[![](itunes-connect-images/connect04.png "сведения о версии, которые готовы к отправке tvOS")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Подготовка к отправке на проверку

Теперь вы можете отправить приложение Xamarin.tvOS Apple TV App Store для проверки, вернитесь к приложения запись iTunes Connect и нажмите кнопку **отправить на проверку** кнопки в правом верхнем углу экрана:

[![](itunes-connect-images/connect05.png "Отправить на проверку")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье представлен обзор конкретный параметр tvOS в iTunes Connect, необходимого для выпуска на tvOS приложение для Apple TV App Store.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
