---
title: Настройка приложений Mac
description: Этот документ описывает настройку приложения Xamarin.Mac для публикации. В нем рассмотрены параметры приложения, подписи и сборки.
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 1f88a429d58a09b54bb2383dbb77a3768ce64152
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115098"
---
# <a name="mac-app-configuration"></a>Настройка приложений Mac

## <a name="mac-app-configuration"></a>Настройка приложений Mac

Щелкните правой кнопкой мыши проект приложения Mac в Visual Studio для Mac и выберите пункт **Параметры**.

### <a name="application-settings"></a>Параметры приложения

Чтобы изменить параметры приложения в приложении Xamarin.Mac, дважды щелкните файл **Info.plist** на **Панели решения**:

![Выбор файла info.plist](app-configuration-images/config04.png "Selecting the Info.plist file")

Появятся доступные для приложения параметры:

 [![Редактирование файла info.plist](app-configuration-images/config01.png "Editing the Info.plist file")](app-configuration-images/config01-large.png#lightbox)

Для запуска приложений Mac, созданных с помощью Xamarin.Mac, необходимо выполнить следующие системные требования:

- наличие компьютера Mac под управлением Mac OS X 10.7 или более поздней версии.

### <a name="signing-settings"></a>Параметры подписывания

В разделе **Подписывание Mac** в диалоговом окне **Параметры проекта** можно подписать приложение Xamarin.Mac для тестирования, самостоятельного выпуска или выпуска через магазин Apple App Store:

[![Редактор подписывания Mac](app-configuration-images/config02.png "The Mac Signing window")](app-configuration-images/config02-large.png#lightbox)

Здесь выбираются значения для удостоверения, профиля подготовки и любых настраиваемых назначений, используемые для подписывания приложения после компиляции. При необходимости можно подписать установщик, используемый для установки приложения на других компьютерах Mac.

### <a name="build-settings"></a>Параметры сборки

В разделе **Сборка Mac** диалогового окна **Параметры проекта** можно выбирать архитектуру для приложения Xamarin.Mac, администрировать версии, поддерживаемые приложением macOS, и при необходимости создавать пакеты установки после успешной компиляции приложения.

 [![Изменение параметров сборки](app-configuration-images/config03.png "Editing the build options")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>Связанные ссылки

- [Установка](/visualstudio/mac/installation/)
- [Пример кода приложения "Привет, Mac"](~/mac/get-started/hello-mac.md)
- [Распространение приложений в Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Идентификатор разработчика и привратник](https://developer.apple.com/resources/developer-id/)
