---
title: "Android.Support.v7.AppCompat — не найти ресурс, соответствующий заданному имени: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: fea681ac3b99abed09d3d3e745bd4bf6015970df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112420"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat — не найти ресурс, соответствующий заданному имени: attr 'android: actionModeShareDrawable'

1. Убедитесь, что можно загрузить последние дополнения, а также Android 5.0 SDK через диспетчер пакетов SDK Android (API 21).

2. Убедитесь, что при компиляции приложения с compileSdkVersion, равным 21. При необходимости можно задать targetSdkVersion также 21.

3. Если требуется более предыдущей версии, например API 19, загрузите соответствующую версию, см. на странице Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Примечание*: Если вручную установить с помощью консоли диспетчера пакетов, убедитесь, что вы также установить ту же версию Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Справочные материалы переполнения стека. [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>См. также

- [Какие пакеты SDK Android следует установить?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

