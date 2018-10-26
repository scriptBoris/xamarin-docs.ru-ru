---
title: Какие драйверы USB, мне нужно отладки Android в Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8b996a1cc89acedc47c7169ec579dfb99dae788f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114539"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Какие драйверы USB, мне нужно отладки Android в Windows?

## <a name="finding-usb-drivers"></a>Поиск драйверов USB

Отладка на устройстве Android, при разработке в Windows; необходимо установить драйвер USB. Диспетчер Android SDK включает в себя «Драйвер Google USB» по умолчанию, который добавляет поддержку для устройств хранилища, как описано здесь: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Другие устройства требуют драйверов USB, в частности опубликованных производителем устройства. Некоторые ссылки для наиболее распространенных производителей включаются в этом руководстве: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Альтернативные варианты

В зависимости от manfacturer бывает сложно отследить точное требуется USB-драйвера. Некоторые альтернативные решения для тестирования приложений Android разработан в Windows, в том числе с помощью эмулятора Android или использование внешних служб тестирования. Некоторые из них указаны ниже:

- [Тестов центра приложений](https://docs.microsoft.com/appcenter/test-cloud/) - облако тестирование служб, запускаемых на сотнях реальных устройств Android.

- [Эмулятор Visual Studio для Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Отладка приложений в Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

