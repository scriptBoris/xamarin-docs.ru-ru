---
title: Проверка подлинности отпечатков пальцев
description: В этом руководстве описывается добавление проверки подлинности отпечатков пальцев, представленные в Android 6.0 Xamarin.Android приложению.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1b28b16dfd92ef3a31201ef2e86681a425a58ab8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764122"
---
# <a name="fingerprint-authentication"></a>Проверка подлинности отпечатков пальцев

_В этом руководстве описывается добавление проверки подлинности отпечатков пальцев, представленные в Android 6.0 Xamarin.Android приложению._


## <a name="fingerprint-authentication-overview"></a>Обзор проверки подлинности отпечатков пальцев

Наступление сканеров отпечатков пальцев на устройствах Android предоставляет приложениям вместо традиционных имени пользователя и пароля метод проверки подлинности пользователя. Использование отпечатки для проверки подлинности пользователь делает приложение на использование безопасности, обеспечивающий меньшее вмешательство по сравнению имени пользователя и пароля.

API-интерфейсы FingerprintManager сканеров отпечатков пальцев целевых устройств и управлением API уровня 23 (Android 6.0) или более поздней версии. API-интерфейсы находятся в `Android.Hardware.Fingerprints` пространства имен. Библиотеку поддержки Android версии 4 предоставляет версии API-интерфейсы, предназначенные для более старых версий Android отпечатков пальцев. Совместимость API-интерфейсов находятся в `Android.Support.v4.Hardware.Fingerprint` пространства имен, распространяемые через [пакет Xamarin.Android.Support.v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (и ее аналог библиотека поддержки [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) является основным классом с помощью отпечатков пальцев, сканирование оборудования. Этот класс является оболочка служба уровня системы, которая управляет взаимодействия с оборудованием, сам пакет SDK для Android. Оно отвечает, для запуска сканеров отпечатков пальцев, а для ответов на отзывы от сканера. Этот класс имеет достаточно простой интерфейс с только три члена:

* **`Authenticate`** &ndash; Этот метод будет инициализировать средство проверки оборудования и запустите службу в фоновом режиме, Ожидание пользователя для проверки их отпечатков пальцев.
* **`EnrolledFingerprints`** &ndash; Это свойство будет возвращать `true` Если пользователь зарегистрировал один или несколько отпечатки пальцев с устройством.
* **`HardwareDetected`** &ndash; Это свойство позволяет определить, поддерживает ли устройство сканирования отпечатков пальцев.

`FingerprintManager.Authenticate` Метод используется приложение для запуска сканеров отпечатков пальцев. Ниже приведен пример вызова неуправляемого кода с помощью совместимости библиотека поддержки API-интерфейсы:

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

В этом руководстве описывается, как использовать `FingerprintManager` API-интерфейсы для повышения уровня приложения с проверкой подлинности отпечатков пальцев. Будет описано, как создать экземпляр и создать `CryptoObject` для обеспечения безопасности в результате сканеров отпечатков пальцев. Мы рассмотрим, как приложение должно подкласс `FingerprintManager.AuthenticationCallback` и ответ на отзывы от сканеров отпечатков пальцев. Наконец, мы рассмотрим, как зарегистрировать отпечаток пальца на устройстве или эмуляторе Android и способы использования **adb** для имитации сканирования отпечатков пальцев.

## <a name="requirements"></a>Требования

Требуется при проверке подлинности отпечатков пальцев Android 6.0 (API уровня 23) или более поздней версии и устройство с сканеров отпечатков пальцев. 

Отпечаток пальца уже должны быть зарегистрированы в устройство для каждого пользователя, который должен пройти проверку подлинности. Это включает в себя настройку блокировка экрана, который использует пароля, ПИН-код, проведите шаблон или распознавание лиц. Можно имитировать некоторые функциональные возможности проверки подлинности отпечатков пальцев, в эмуляторе Android.  Дополнительные сведения об этих двух темах см. в разделе [регистрации отпечаток пальца](enrolling-fingerprint.md) раздела. 






## <a name="related-links"></a>Связанные ссылки

- [Отпечаток пальца Руководство примера приложения](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Пример диалогового окна отпечатков пальцев](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Запрос разрешений во время выполнения](http://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Параметры Fingerprint и платежей API (видео)](https://youtu.be/VOn7VrTRlA4)
