---
title: Проверка подлинности по отпечаткам
description: В этом руководстве описывается добавление проверки подлинности по отпечатку пальца, представленные в Android 6.0 в приложение Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: cdb18cd916ddd5daab7db9839bb15ebb098d0c09
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123320"
---
# <a name="fingerprint-authentication"></a>Проверка подлинности по отпечаткам

_В этом руководстве описывается добавление проверки подлинности по отпечатку пальца, представленные в Android 6.0 в приложение Xamarin.Android._


## <a name="fingerprint-authentication-overview"></a>Обзор проверки подлинности по отпечатку пальца

Поступление сканеров отпечатков пальцев на устройствах Android предоставляет приложениям вместо традиционных имени пользователя и пароля метод проверки подлинности пользователя. Использование отпечатков для аутентификации пользователя делает приложение использование безопасности, обеспечивающий меньшее вмешательство по сравнению имени пользователя и пароля.

API-интерфейсы FingerprintManager ориентироваться на устройства с сканер отпечатков пальцев, и вы выполняете уровень API 23 (Android 6.0) или более поздней версии. API-интерфейсы находятся в `Android.Hardware.Fingerprints` пространства имен. Библиотека поддержки Android v4 предоставляет версии отпечатка API-интерфейсы, предназначенные для более старых версиях Android. Совместимость API-интерфейсы находятся в `Android.Support.v4.Hardware.Fingerprint` пространства имен, распространяются через [пакет Xamarin.Android.Support.v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).

[FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (и его аналог библиотека поддержки [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) является основным классом для с помощью отпечатков пальцев, сканирование оборудования. Этот класс представляет собой оболочку пакета SDK для Android, вокруг служба уровня системы, которая управляет взаимодействиями с оборудования. Он отвечает за запуск сканер отпечатков пальцев, а также для ответов на отзывы от сканера. Этот класс имеет довольно простой интерфейс с помощью только три члена:

* **`Authenticate`** &ndash; Этот метод будет инициализировать средство проверки оборудования и запустить службу в фоновом режиме, Ожидание пользователю просканировать отпечатки пальцев.
* **`EnrolledFingerprints`** &ndash; Это свойство будет возвращать `true` . Если пользователь зарегистрировал один или несколько отпечатков с устройством.
* **`HardwareDetected`** &ndash; Это свойство используется, чтобы определить, поддерживает ли устройство сканирования отпечатков пальцев.

`FingerprintManager.Authenticate` Метод используется в приложении Android для запуска сканер отпечатков пальцев. Ниже приведен пример того, как вызывать его с помощью библиотеки поддержки совместимости API-интерфейсы:

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

В этом руководстве описывается, как использовать `FingerprintManager` API-интерфейсы для улучшения приложений Android с проверкой подлинности по отпечатку пальца. Его мы рассмотрим, как создать экземпляр и создать `CryptoObject` для обеспечения безопасности в результате сканер отпечатков пальцев. Мы рассмотрим, как приложения должны подкласс `FingerprintManager.AuthenticationCallback` и реакция на отзывы от сканер отпечатков пальцев. Наконец, мы увидим, как зарегистрировать пальца на устройстве или эмуляторе Android и способы использования **adb** для имитации сканирования отпечатков пальцев.

## <a name="requirements"></a>Требования

Требуется при проверке подлинности по отпечатку пальца Android 6.0 (уровень API 23) или выше, а также устройства с помощью сканер отпечатков пальцев. 

Отпечаток пальца уже должны быть зарегистрированы с устройством для каждого пользователя, который должен пройти проверку подлинности. Это включает в себя настройку блокировки экрана, который использует пароля, ПИН-код, проведите по экрану или шаблон распознавание лиц. Существует возможность имитировать некоторые функциональные возможности проверки подлинности по отпечатку пальца в эмуляторе Android.  Дополнительные сведения об этих двух темах см. в разделе [регистрация отпечатка пальца](enrolling-fingerprint.md) раздел. 






## <a name="related-links"></a>Связанные ссылки

- [Отпечаток пальца руководстве пример приложения](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [Пример диалогового окна по отпечатку пальца](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Запрос разрешений во время выполнения](http://developer.android.com/training/permissions/requesting.html)
- [Android.Hardware.Fingerprint](http://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [Android.support.v4.Hardware.Fingerprint](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [API отпечатков пальцев и платежей (видео)](https://youtu.be/VOn7VrTRlA4)
