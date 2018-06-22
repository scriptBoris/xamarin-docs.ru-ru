---
title: 'Android.Support.v7.AppCompat - ресурс не найден, соответствующий заданным именем: attr «android: actionModeShareDrawable»'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 07655587642c3e1aa94d035e76f6f6758340546d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774900"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - ресурс не найден, соответствующий заданным именем: attr «android: actionModeShareDrawable»

1. Убедитесь, что загрузить последние дополнения, а также Android 5.0 SDK через диспетчер Android SDK (API 21).

2. Убедитесь, что при компиляции приложения с compileSdkVersion значение 21. При необходимости можно задать targetSdkVersion также 21.

3. Если требуется предыдущей версии, такие как API 19, загрузите соответствующую версию найти на странице Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Примечание*: Если вручную установить через консоль диспетчера пакетов, убедитесь, что также установить ту же версию Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Справочные материалы переполнения стека. [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>См. также

- [Какие пакеты SDK Android следует установить?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

