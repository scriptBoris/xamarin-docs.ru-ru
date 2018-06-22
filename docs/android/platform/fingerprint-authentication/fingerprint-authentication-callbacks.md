---
title: Ответ на обратные вызовы проверки подлинности
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: b8a3ed64e66cd97faeff78b4d0b008a1a0b14477
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765773"
---
# <a name="responding-to-authentication-callbacks"></a>Ответ на обратные вызовы проверки подлинности

Сканер отпечатков пальцев выполняется в фоновом режиме в отдельном потоке, а после завершения будет показана результаты сканирования, вызвав один из методов `FingerprintManager.AuthenticationCallback` в потоке пользовательского интерфейса. Приложения должны предоставить свой собственный обработчик, который расширяет этот абстрактный класс реализации любого из следующих методов:

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; Вызывается при неустранимой ошибки. Нет ничего более приложением или пользователем можно сделать для исправления ситуации, за исключением того, возможно, повторите попытку.
* **`OnAuthenticationFailed()`** &ndash; Этот метод вызывается, когда отпечаток пальца обнаружен, но не удается распознать устройство.
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; Вызывается при устранимую ошибку, например время перехода для быстрого через сканер палец.
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; Вызывается, когда был распознан отпечаток пальца.

Если `CryptoObject` использовался при вызове `Authenticate`, рекомендуется вызывать `Cipher.DoFinal` в `OnAuthenticationSuccessful`.
`DoFinal` будет вызывать исключение, если был подделан или неверно инициализирован шифр, указывающее на то, что результат сканеров отпечатков пальцев может был изменен вне приложения.


> [!NOTE]
> Рекомендуется хранить сравнительно небольшая вес класс обратного вызова и освобождения от логики, специфичной для приложения. Обратные вызовы должен работать как «трафика копиями» между приложения Android и результаты из сканеров отпечатков пальцев.

## <a name="a-sample-authentication-callback-handler"></a>Пример проверки подлинности обратного вызова обработчика

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

`OnAuthenticationSucceeded` проверяет, если `Cipher` , предоставленная `FingerprintManager` при `Authentication` был вызван. В этом случае `DoFinal` метод вызывается для шифрования. Закрывает это `Cipher`, восстанавливая ее из копии в исходное состояние. Если возникла проблема с шифр, затем `DoFinal` вызовет исключение и попытка проверки подлинности считается неудачной.

`OnAuthenticationError` И `OnAuthenticationHelp` целое число, указывающее, проблема вызвана получать обратные вызовы каждого. В следующем разделе описываются возможные справки или коды ошибок. Две обратные вызовы выполняют аналогичные функции &ndash; для оповещения приложения, сбой проверки подлинности отпечатков пальцев. Описывает их отличие — степени серьезности. `OnAuthenticationHelp` Представляет устранимую ошибку пользователя, например проведение пальцем по экрану отпечаток слишком быстро; `OnAuthenticationError` является более серьезная ошибка, например поврежденного отпечатков пальцев.

Обратите внимание, что `OnAuthenticationError` будет вызываться при отмене сканирования отпечатков пальцев через `CancellationSignal.Cancel()` сообщения. `errMsgId` Параметр имеет значение 5 (`FingerprintState.ErrorCanceled`). В зависимости от требований, реализация `AuthenticationCallbacks` обработки этой ситуации иначе, чем остальные ошибки. 

`OnAuthenticationFailed` вызывается, когда отпечаток успешно проверены, но не соответствует любой отпечатков пальцев, зарегистрированных с устройством. 

## <a name="help-codes-and-error-message-ids"></a>Коды справки и сообщение ошибки 

Список и описание кодов ошибок и коды справки можно найти в [документации по пакету SDK для Android](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD) FingerprintManager класса. Эти значения с представляет Xamarin.Android `Android.Hardware.Fingerprints.FingerprintState` перечисления:


-   **`AcquiredGood`** &ndash; (значение 0) Получить изображение было хорошим.


-   **`AcquiredImagerDirty`** &ndash; (значение 3) Рисунок отпечатков пальцев был слишком часто срабатывает из-за потенциально или обнаруженных грязь на датчик. Например, имеет смысл возвращать это после нескольких `AcquiredInsufficient` или фактические обнаружение грязь на датчик (постоянный пикселей, отрезки, и т. д.). Пользователь должен выполнить действие для очистки датчика, если это значение возвращается.


-   **`AcquiredInsufficient`** &ndash; (значение 2) Изображение отпечатка был слишком часто срабатывает для обработки из-за обнаруженных условие (т. е. тонера обложки) или датчик возможно «грязных» (в разделе `AcquiredImagerDirty`.



-   **`AcquiredPartial`** &ndash; (значение 1) Обнаружен образ частичного отпечатков пальцев. Во время регистрации, пользователь должен знать о том, что нужно сделать для решения этой проблемы, например, &ldquo;надежно нажмите на датчик.&rdquo;



-   **`AcquiredTooFast`** &ndash; (значение 5) Изображение отпечатка не была завершена из-за быстрого перемещения. Хотя главным образом подходит для линейной массива датчиков, это также может произойти, если палец был перемещен во время приобретения. Пользователь должен появиться запрос на перемещайте его медленнее (линейная) или оставьте пальца на датчик больше времени.




-   **`AcquiredToSlow`** &ndash; (значение 4) Изображение отпечатка не удается прочитать из-за отсутствия движения. Это лучше всего подходит для линейной массива датчиков, требующих движения считывание.



-   **`ErrorCanceled`** &ndash; (значение 5) Операция была отменена, так как недоступен датчика отпечатков пальцев. Например это может произойти при переключился пользователь устройство заблокировано, или другой ожидающей операцией предотвращает или отключает его.



-   **`ErrorHwUnavailable`** &ndash; (значение 1) Оборудования недоступна. Повторите попытку позже.




-   **`ErrorLockout`** &ndash; (значение 7) Операция была отменена, так как API блокируется из-за слишком большого числа попыток.




-   **`ErrorNoSpace`** &ndash; (значение 4) Состояние ошибки, возвращаемые для операций, таких как регистрации; Невозможно выполнить операцию, так как недостаточно места, оставшийся до завершения операции.



-   **`ErrorTimeout`** &ndash; (значение 3) Состояние ошибки, возвращается, если текущий запрос выполняется слишком много времени. Этот способ предназначен для предотвращения программами неограниченное время ожидания датчика отпечатков пальцев. Время ожидания платформы и зависящие от датчика, но обычно около 30 секунд.



-   **`ErrorUnableToProcess`** &ndash; (значение 2) Состояние ошибки, возвращается, если не удалось обработать текущее изображение датчика.



## <a name="related-links"></a>Связанные ссылки

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
