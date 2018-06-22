---
title: Сканирование отпечатков пальцев
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/23/2016
ms.openlocfilehash: 2b20402fd2cd6782247fb2c62513d7aff43a95a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774165"
---
# <a name="scanning-for-fingerprints"></a>Сканирование отпечатков пальцев

Теперь, когда мы рассмотрели подготовить приложение для использования проверки подлинности отпечатков пальцев Xamarin.Android, давайте вернемся к `FingerprintManager.Authenticate` метода и обсудить его место в проверке подлинности Android 6.0 отпечатков пальцев. Краткий обзор рабочего процесса для проверки подлинности отпечатков пальцев, перечисленными в этом списке:

1. Вызвать `FingerprintManager.Authenticate`, передав `CryptoObject` и `FingerprintManager.AuthenticationCallback` экземпляра. `CryptoObject` Позволяет убедиться, что результат проверки подлинности отпечатков пальцев не было подделано. 
2. Подкласс [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) класса. Экземпляр этого класса будут передаваться в `FingerprintManager` при отпечаток пальца начинается с проверки подлинности. По окончании сканеров отпечатков пальцев вызывается один из методов обратного вызова для данного класса.
3. Напишите код для обновления пользовательского интерфейса, позволяющего пользователю, что устройство запущен сканер отпечатков пальцев и находится в состоянии ожидания для взаимодействия с пользователем. 
4. По завершении сканеров отпечатков пальцев Android возвращают результаты в приложение путем вызова метода на `FingerprintManager.AuthenticationCallback` экземпляра, представленного в предыдущем шаге.
5. Приложение будет информировать пользователей о результатах проверки подлинности отпечатков пальцев и реагировать на результатах соответствующим образом. 

В следующем фрагменте кода приведен пример метода в действии, которое начинает проверять отпечатков пальцев:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Давайте рассмотрим каждую из этих параметров в `Authenticate` метод немного более подробно:

* Первый параметр — _шифрования_ , сканеров отпечатков пальцев будет использовать для проверки подлинности результаты сканирования отпечатков пальцев. Этот объект может быть `null`, в этом случае приложение должно вслепую доверяют ни изменял результаты отпечатков пальцев. Рекомендуется `CryptoObject` создано и передаваемых `FingerprintManager` вместо null. [Создание CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) будут подробно описаны в для создания экземпляра `CryptoObject` на основе `Cipher`.
* Второй параметр всегда равно нулю. Документации по Android определяет это как набор флагов и скорее всего зарезервировано для будущего использования. 
* Третий параметр `cancellationSignal` — это объект, который используется для отключения сканеров отпечатков пальцев и отменить текущий запрос. Это [Android CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)и не является типом .NET framework.
* Четвертый параметр является обязательным и — это класс, который наследуется от класса `AuthenticationCallback` абстрактного класса. Методы класса будет вызываться для сообщения клиентам при `FingerprintManager` завершена, и результаты будут. Как много в понимании реализации `AuthenticationCallback`, который будет рассматриваться в [это собственный раздел](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* Пятый аргумент является необязательным `Handler` экземпляра. Если `Handler` объект предоставляется `FingerprintManager` будет использовать `Looper` из этого объекта, при обработке сообщений от оборудования отпечатков пальцев. Как правило, один не требуется предоставить `Handler`, будет использоваться FingerprintManager `Looper` из приложения.

## <a name="cancelling-a-fingerprint-scan"></a>Отмена сканирования отпечатков пальцев

Он может быть необходим для пользователя (или приложением), отменить сканирования отпечатков пальцев после его инициализации. В этом случае вызывать [ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) метод [ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) , предоставленная `FingerprintManager.Authenticate` при ее вызове для проверки отпечатка.

Теперь, когда мы видели `Authenticate` метод, давайте рассмотрим некоторые более важные параметры более подробно. Во-первых, мы рассмотрим [отвечать на запросы проверки подлинности обратные вызовы](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), который будут рассматриваться как подкласс [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), включение приложения для реагирования на результаты, предоставляемые сканеров отпечатков пальцев.




## <a name="related-links"></a>Связанные ссылки

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
