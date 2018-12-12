---
title: Сканирование отпечатков
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 843a3a8a347e3de48efbbbba3a1088c617e9e7cb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898736"
---
# <a name="scanning-for-fingerprints"></a>Сканирование отпечатков

Теперь, когда мы увидели, как подготовить приложение Xamarin.Android, чтобы использовать проверку подлинности по отпечатку пальца, давайте вернемся к `FingerprintManager.Authenticate` метод и обсудим его место в проверке подлинности по отпечатку пальца Android 6.0. Краткий обзор рабочего процесса для проверки подлинности по отпечатку пальца описан в этом списке:

1. Вызвать `FingerprintManager.Authenticate`, передав `CryptoObject` и `FingerprintManager.AuthenticationCallback` экземпляра. `CryptoObject` Позволяют гарантировать, что результат проверки подлинности по отпечатку пальца не было подделано. 
2. Подкласс [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html) класса. Экземпляр этого класса будет предоставляться `FingerprintManager` при отпечатков пальцев запуска проверки подлинности. По завершении сканер отпечатков пальцев вызывается один из методов обратного вызова для данного класса.
3. Напишите код для обновления пользовательского интерфейса, позволяющего пользователю, что устройство запущен сканер отпечатков пальцев и ожидает взаимодействия с пользователем. 
4. По завершении сканер отпечатков пальцев Android вернет результаты в приложение путем вызова метода на `FingerprintManager.AuthenticationCallback` экземпляр, который был указан на предыдущем шаге.
5. Приложение будет информировать пользователей об результаты проверки подлинности по отпечатку пальца и реагировать на них результаты соответствующим образом. 

В следующем фрагменте кода приведен пример метода в действии, которое начнет сканирование отпечатков:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Давайте рассмотрим каждую из этих параметров в `Authenticate` метод немного более подробно:

* Первый параметр — _crypto_ который сканер отпечатков пальцев будет использовать для проверки подлинности результаты сканирования отпечатков пальцев. Этот объект может быть `null`, в этом случае приложение должно слепо доверяют ни изменял результаты отпечатков пальцев. Рекомендуется `CryptoObject` создано и предоставлены `FingerprintManager` вместо null. [Создание CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md) будет также подробно объясняется, как создать экземпляр `CryptoObject` на основе `Cipher`.
* Второй параметр всегда равно нулю. Документации по Android определяет это как набор флагов и скорее зарезервирован для использования в будущем. 
* Третий параметр `cancellationSignal` используется, чтобы завершить работу сканер отпечатков пальцев и отменить текущий запрос. Это [Android CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html), а не типа из .NET framework.
* Четвертый параметр является обязательным и представляет собой класс, который наследуется от класса `AuthenticationCallback` абстрактного класса. Методы этого класса будет вызываться для сообщения клиентам при `FingerprintManager` завершения и каковы результаты. Как много знать о реализации `AuthenticationCallback`, он будет рассматриваться в [это собственный раздел](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md).
* Пятый параметр не является обязательной `Handler` экземпляра. Если `Handler` объект предоставляется, `FingerprintManager` будет использовать `Looper` из этого объекта, при обработке сообщения от оборудования для отпечатков пальцев. Как правило, один не требуется ли предоставлять `Handler`, будет использоваться FingerprintManager `Looper` из приложения.

## <a name="cancelling-a-fingerprint-scan"></a>Отмена сканирования отпечатков пальцев

Возможно, необходимые для пользователя (или приложения), отмены сканирования отпечатков пальцев, после его инициализации. В этом случае вызвать [ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled()) метод [ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) , переданный для `FingerprintManager.Authenticate` при ее вызове для проверки отпечатков пальцев.

Теперь, когда мы рассмотрели `Authenticate` метод, давайте рассмотрим некоторые более важные параметры более подробно. Во-первых, мы рассмотрим [ответов на обратные вызовы проверки подлинности](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), которой будут рассматриваться как подкласс [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html), что позволяет приложению реагировать на Android результаты, предоставляемые сканер отпечатков пальцев.




## <a name="related-links"></a>Связанные ссылки

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
