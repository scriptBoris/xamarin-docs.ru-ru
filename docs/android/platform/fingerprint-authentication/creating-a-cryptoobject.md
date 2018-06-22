---
title: Создание CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f7a8ab7a43c0a3258cf6e737b0d235cbe7a1c747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765718"
---
# <a name="creating-a-cryptoobject"></a>Создание CryptoObject

Целостность результаты проверки подлинности отпечатков пальцев важно приложения &ndash; является, как приложение знало, удостоверение пользователя. Теоретически можно на наличие вредоносных программ сторонних перехватывать и изменять результаты, возвращенные сканеров отпечатков пальцев. В этом разделе обсуждается, один из способов сохранения допустимости результатов отпечатков пальцев. 

`FingerprintManager.CryptoObject` Является оболочкой вокруг API-интерфейсов криптографии Java и используется `FingerprintManager` для защиты целостности запроса проверки подлинности. Как правило `Javax.Crypto.Cipher` объект — это механизм для шифрования результатов сканеров отпечатков пальцев. `Cipher` Ключ, создаваемый с помощью интерфейсов API Android ключей будет использоваться сам объект.

Чтобы понять, как взаимодействуют все эти классы, сначала рассмотрим следующий код, который демонстрирует создание `CryptoObject`, а затем объясняется более подробно:

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

Образец кода будет создан новый `Cipher` для каждого `CryptoObject`, с помощью ключа, который был создан для приложения. Ключ определяется `KEY_NAME` переменной, заданной в начале `CryptoObjectHelper` класса. Метод `GetKey` будет попробуйте и получить ключ с помощью Android API хранилища ключей. Если ключ отсутствует, то метод `CreateKey` будет создан новый ключ для приложения.

Шифр создается с помощью вызова `Cipher.GetInstance`, принимающий _преобразования_ (строковое значение, указывающее шифрования как для шифрования и расшифровки данных). Вызов `Cipher.Init` завершит инициализацию шифрования с помощью ключа из приложения. 

Важно помнить, что существуют ситуации, где Android может сделать недействительными ключ. 

* Новый отпечаток пальца был зарегистрирован с устройством.
* Нет не отпечатков пальцев, зарегистрированных с устройством.
* Пользователь отключил Экран блокировки.
* Пользователь изменил экран блокировки (тип screenlock или использовать шаблон или ПИН-код).

В этом случае `Cipher.Init` вызовет [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Приведенный выше образец кода будет перехватывать это исключение, удалить ключ и затем создать новую.

В следующем разделе обсудим создать ключ и сохранить его на устройстве.

## <a name="creating-a-secret-key"></a>Создание секретного ключа

`CryptoObjectHelper` Класс использует Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) создайте ключ и сохранить его на устройстве. `KeyGenerator` Класс можно создать ключ, но при этом требуется некоторые метаданные о типе создаваемого ключа. Эти сведения предоставляются с помощью экземпляра [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) класса. 

Объект `KeyGenerator` создается с помощью `GetInstance` фабричный метод. В примере кода используется [ _стандарта шифрования AES_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) в качестве алгоритма шифрования. AES будет разбить данные на блоки фиксированного размера и шифрование, каждый из этих блоков.

Далее, `KeyGenParameterSpec` создается с помощью `KeyGenParameterSpec.Builder`. `KeyGenParameterSpec.Builder` Включает следующие сведения о клавише, которая будет создана:

* Имя ключа.
* Ключ должен быть допустимым для шифрования и расшифровки.
* В образце кода `BLOCK_MODE` равно _сцепления блоков шифра_ (`KeyProperties.BlockModeCbc`), это означает, что каждый блок является операция Xor с предыдущего блока (Создание зависимостей между каждый блок). 
* `CryptoObjectHelper` Использует [ _открытый ключ шифрования стандартные #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) для создания байтов, которые будет заполнить out блоков, чтобы убедиться, что они имеют тот же размер .
* `SetUserAuthenticationRequired(true)` означает, что для проверки подлинности пользователя не требуется, чтобы можно было использовать ключ.

Один раз `KeyGenParameterSpec` будет создан, он используется для инициализации `KeyGenerator`, которой будет создавать ключ и сохраните его на устройстве. 

## <a name="using-the-cryptoobjecthelper"></a>С помощью CryptoObjectHelper

Теперь, когда большая часть логики для создания, содержащийся в образце кода `CryptoWrapper` в `CryptoObjectHelper` класса, давайте повторное использование кода с начала этого руководства и использовать `CryptoObjectHelper` для создания шифр и запуска сканеров отпечатков пальцев: 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

Теперь, когда мы увидели, как создать `CryptoObject`, позволяет перейти к статье как `FingerprintManager.AuthenticationCallbacks` используются для передачи результатов работы службы сканер отпечатков пальцев для приложения.



## <a name="related-links"></a>Связанные ссылки

- [Cipher](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
