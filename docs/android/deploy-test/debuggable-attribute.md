---
title: Атрибут Debuggable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 85b2462605f76e3be8bae589e9fe6cf655741746
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762693"
---
# <a name="debuggable-attribute"></a>Атрибут Debuggable



Для отладки Android поддерживает протокол JDWP (Java Debug Wire Protocol). Эта технология позволяет некоторым средствам, например ADB, взаимодействовать с виртуальной машиной Java. Протокол JDWP очень важен на этапе разработки, но не забывайте отключить его перед публикацией приложения.

JDWP можно задать значением атрибута `android:debuggable` в приложении Android. Xamarin.Android предоставляет следующие методы для установки этого атрибута.

1.  Создайте файл `AndroidManifext.xml` и задайте в нем атрибут `android:debuggable`.
2.  Включите `ApplicationAttribute` в файл `.CS`, например так: `[assembly: Application(Debuggable=false)]` .


Если присутствуют одновременно `AndroidManifest.xml` и `ApplicationAttribute`, содержимое `AndroidManifest.xml` имеет более высокий приоритет, чем `ApplicationAttribute`.

Если не указано ни `AndroidManifest.xml`, ни `ApplicationAttribute`, то значение по умолчанию для атрибута `android:debuggable` зависит от того, создаются ли отладочные символы. Если символы отладки присутствуют, Xamarin.Android устанавливает для атрибута `android:debuggable` значение `true`.

Обратите внимание, что значение атрибута `android:debuggable` НЕ ВСЕГДА зависит от конфигурации сборки. Может случиться так, что для сборки выпуска атрибут `android:debuggable` имеет значение true.


## <a name="related-links"></a>Связанные ссылки

- [Приложения с атрибутом Debuggable на Android Market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/)
