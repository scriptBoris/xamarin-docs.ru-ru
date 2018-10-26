---
title: Ответов на обратные вызовы проверки подлинности
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 17a1c94ad3b9bde67537ea7113352f0fc10d2a08
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110125"
---
# <a name="responding-to-authentication-callbacks"></a>Ответов на обратные вызовы проверки подлинности

Сканер отпечатков пальцев выполняется в фоновом режиме в отдельном потоке, и после завершения он выведет результаты сканирования, вызвав один из методов `FingerprintManager.AuthenticationCallback` в потоке пользовательского интерфейса. Приложение Android необходимо предоставить свой собственный обработчик, который расширяет этот абстрактный класс, реализующий любого из следующих методов:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Вызывается при возникновении неустранимой ошибки. Больше нечего, приложением или пользователем можно сделать, чтобы исправить ситуацию, за исключением того, возможно, повторите попытку.
* **`OnAuthenticationFailed()`** &ndash; Этот метод вызывается, когда пальца обнаружено, но не распознается средой устройства.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Вызывается при наличии устранимая ошибка, например через терминал для быстрого через сканер палец.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Вызывается, если был распознан отпечаток пальца.

Если `CryptoObject` был использован при вызове `Authenticate`, рекомендуется вызывать `Cipher.DoFinal` в `OnAuthenticationSuccessful`.
`DoFinal` будет выдано исключение, если был подделан или неправильно инициализирован шифр, указывающее, что результат сканер отпечатков пальцев может были изменены вне приложения.


> [!NOTE]
> Рекомендуется сохранять относительно небольшого размера класса обратного вызова и освобождения от логики приложения. Обратные вызовы должны выступать в роли «трафика cop» между приложения Android и результаты из сканер отпечатков пальцев.

## <a name="a-sample-authentication-callback-handler"></a>Пример обработчика обратного вызова проверки подлинности

Следующий класс является примером минимальным `FingerprintManager.AuthenticationCallback` реализации: 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded` проверяет, если `Cipher` была предоставлена `FingerprintManager` при `Authentication` была вызвана. Если Да, `DoFinal` вызывается метод шифрования. Закрывает это `Cipher`, восстановив его в исходное состояние. Если возникла проблема с шифр, затем `DoFinal` приведет к возникновению исключения и попытки проверки подлинности должен считаться завершившимся сбоем.

`OnAuthenticationError` И `OnAuthenticationHelp` целое число, указывающее, что проблема вызвана получать обратные вызовы каждый. В следующем разделе объясняется всех возможных справки или коды ошибок. Две обратные вызовы служат для похожих целей &ndash; для оповещения приложения, сбой проверки подлинности по отпечатку пальца. Как они отличаются тем, по степени серьезности. `OnAuthenticationHelp` пользователя произошла устранимая ошибка, например считывания отпечатка слишком высокая; `OnAuthenticationError` является более серьезная ошибка, например средство поиска поврежденных отпечатков пальцев.

Обратите внимание, что `OnAuthenticationError` будет вызываться при отмене сканирования отпечатков пальцев через `CancellationSignal.Cancel()` сообщения. `errMsgId` Параметр будет иметь значение 5 (`FingerprintState.ErrorCanceled`). В зависимости от требований, реализация `AuthenticationCallbacks` считают это иначе, чем остальные ошибки. 

`OnAuthenticationFailed` вызывается, когда отпечаток успешно проверены, но не соответствует любой отпечатков пальцев, зарегистрировано устройство. 

## <a name="help-codes-and-error-message-ids"></a>Коды справки и сообщение ошибки 

Список и описание кодов ошибок и коды справки можно найти в [документации пакета SDK для Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) FingerprintManager класса. Xamarin.Android предоставляет эти значения с `Android.Hardware.Fingerprints.FingerprintState` перечисления:


-   **`AcquiredGood`** &ndash; (значение 0) Получить изображение было хорошим.


-   **`AcquiredImagerDirty`** &ndash; (значение 3) Изображение отпечатка был слишком часто срабатывает из-за потенциально или обнаруженные грязи в датчике. Например, имеет смысл возвращать это после нескольких `AcquiredInsufficient` или фактический обнаружение грязи в датчике (заблокированной пикселей, отрезки т. д.). Пользователь должен предпринимать действия для очистки датчика, если это значение возвращается.


-   **`AcquiredInsufficient`** &ndash; (значение 2) Изображение отпечатка был слишком часто срабатывает для обработки из-за обнаруженных условия (т. е. "не повторяйся" обложки) или возможно «грязных» данных датчика (см. в разделе `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (значение 1) Обнаружен только образ частичного отпечатков пальцев. Во время регистрации, пользователь должен основываться на то, что необходимо для решения этой проблемы, например, произойти &ldquo;твердо нажмите на датчик.&rdquo;



-   **`AcquiredTooFast`** &ndash; (значение 5) Изображение отпечаток пальца не была завершена из-за быстрого перемещения. Хотя главным образом подходит для датчиков линейной массива, это также может произойти, если палец был перемещен во время приобретения. Переместите палец медленнее необходимо попросить пользователя (линейную) или оставить палец в датчике больше времени.




-   **`AcquiredToSlow`** &ndash; (значение 4) Отпечаток пальца изображение не удается прочитать из-за отсутствия движения. Это лучше всего подходит для датчиков линейной массива, требующих движения проведите по экрану.



-   **`ErrorCanceled`** &ndash; (значение 5) Операция была отменена, так как недоступен датчика отпечатков пальцев. Например это может произойти, когда пользователь переключается, устройство заблокировано, или другой ожидающей операцией предотвращает отключает его.



-   **`ErrorHwUnavailable`** &ndash; (значение 1) Оборудование будет недоступен. Повторите попытку позже.




-   **`ErrorLockout`** &ndash; (значение 7) Операция была отменена, так как API блокируется из-за слишком большого числа попыток.




-   **`ErrorNoSpace`** &ndash; (значение 4) Состояние ошибки, возвращаемые для операций, таких как регистрации; Невозможно выполнить операцию, так как не достаточный объем хранилища, оставшихся до завершения операции.



-   **`ErrorTimeout`** &ndash; (значение 3) Состояние ошибки, возвращается, если текущий запрос выполнялся слишком много времени. Это предназначено для предотвращения программы без ограничения времени ожидания для датчика отпечатков пальцев. Время ожидания платформы и зависящих от датчика, но обычно около 30 секунд.



-   **`ErrorUnableToProcess`** &ndash; (значение 2) Состояние ошибки, возвращается, если датчик не удалось обработать текущее изображение.



## <a name="related-links"></a>Связанные ссылки

- [Шифра](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
