---
title: 'Ошибка при отправке в магазин приложений: «Недопустимый пакет - параметры, не может быть внедрен в bitcode обнаруживаются отправки»'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: cacb9040ddc8582490c68bcfd24e80c4c4679eb4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777708"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Ошибка при отправке в магазин приложений: «Недопустимый пакет - параметры, не может быть внедрен в bitcode обнаруживаются отправки»

watchOS приложений и приложений, tvOS _требуют_ bitcode при подаче App Store. При создании и отправке watchOS и tvOS приложения с помощью Xcode 8.3 или более ранней версии, (с помощью уведомлений по электронной почте) может возникнуть следующая ошибка при попытке отправить в магазин приложений:

>Недопустимый пакет - не удается обработать приложение, поскольку параметры не может быть внедрен в bitcode обнаруживаются отправки. Вполне вероятно, что приложение не строится с помощью инструментов, предоставляемых в Xcode.

Решением этой проблемы является построение приложений с помощью Xcode 9 и последнюю версию Xamarin.iOS.
