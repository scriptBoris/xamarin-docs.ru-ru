---
title: Создание CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1658934bedce11a42701eb023a42fc9e617b654d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113772"
---
# <a name="creating-a-cryptoobject"></a>Создание CryptoObject

Целостность результатов проверки подлинности по отпечатку пальца важно приложения &ndash; это, как приложение знает, удостоверение пользователя. Это теоретически возможно наличие вредоносных программ сторонних перехватывать и изменять результаты, возвращенные сканер отпечатков пальцев. В этом разделе будут рассмотрены один из способов сохранения допустимости результатов отпечатков пальцев. 

`FingerprintManager.CryptoObject` Является оболочкой API-интерфейсов криптографии Java и используется `FingerprintManager` для защиты целостности запроса на аутентификацию. Как правило `Javax.Crypto.Cipher` объект — это механизм для шифрования результатов сканер отпечатков пальцев. `Cipher` Самого объекта будет использоваться ключ, созданный с помощью хранилища ключей Android API-интерфейсы.

Чтобы понять, как все эти классы работают вместе, сначала взглянем на следующий код, который демонстрирует способ создания `CryptoObject`и описывается более подробно:

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

В примере кода будет создана новая `Cipher` для каждого `CryptoObject`, с помощью ключа, который был создан для приложения. Ключ определяется `KEY_NAME` переменной, заданной в начале `CryptoObjectHelper` класса. Метод `GetKey` будет попробовать и получить ключ, с помощью API хранилища ключей Android. Если ключ отсутствует, то метод `CreateKey` будет создан новый ключ для приложения.

Шифр создается с помощью вызова `Cipher.GetInstance`, принимающий _преобразования_ (строковое значение, указывающее шифрования как для шифрования и расшифровки данных). Вызов `Cipher.Init` завершит инициализацию шифр, предоставив ключ из приложения. 

Важно понимать, что существуют ситуации, где Android, может сделать недействительным ключ: 

* Новый отпечаток будет зарегистрирован с устройством.
* Существуют не отпечатки пальцев, зарегистрировано устройство.
* Пользователь отключил блокировки экрана.
* Пользователь изменил блокировки экрана (тип screenlock или использовать шаблон или ПИН-код).

В этом случае `Cipher.Init` вызовет [ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html). Приведенный выше образец кода будет перехватывать это исключение, удалите раздел и затем создать новую.

Далее разделе описано, как создать ключ и сохранить его на устройстве.

## <a name="creating-a-secret-key"></a>Создание секретного ключа

`CryptoObjectHelper` Класс использует Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/) создайте ключ и сохранить его на устройстве. `KeyGenerator` Класс можно создать ключ, но при этом требуется некоторые метаданные о типе создаваемого ключа. Эти сведения предоставляются с помощью экземпляра [ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html) класса. 

Объект `KeyGenerator` создается с помощью `GetInstance` метод фабрики. В примере кода используется [ _Advanced Encryption Standard_ ](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) в качестве алгоритма шифрования. AES будет разбить данные на блоки фиксированного размера и шифрования, каждый из этих блоков.

Далее, `KeyGenParameterSpec` создается с помощью `KeyGenParameterSpec.Builder`. `KeyGenParameterSpec.Builder` Заключает в оболочку следующие сведения о ключе, который должен быть создан:

* Имя ключа.
* Ключ должен быть допустимым как для шифрования и расшифровки.
* В образце кода `BLOCK_MODE` присваивается _сцепления блоков шифра_ (`KeyProperties.BlockModeCbc`), это означает, что каждый блок является XORed с предыдущего блока (Создание зависимостей между каждый блок). 
* `CryptoObjectHelper` Использует [ _открытый ключ шифрования Standard #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) для создания байты, которые будут заполняемое блоки, чтобы убедиться, что все они не будут одинакового размера. .
* `SetUserAuthenticationRequired(true)` означает, что прежде чем можно будет использовать ключ необходим, проверку подлинности.

Один раз `KeyGenParameterSpec` будет создан, он используется для инициализации `KeyGenerator`, который создаст ключ и безопасно хранить его на устройстве. 

## <a name="using-the-cryptoobjecthelper"></a>С помощью CryptoObjectHelper

Теперь, когда образец кода большую часть логики для создания инкапсулированные `CryptoWrapper` в `CryptoObjectHelper` класса, давайте вернемся к кода с самого начала данного руководства и использовать `CryptoObjectHelper` шифр создать и запустить сканер отпечатков пальцев: 

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

Теперь, когда мы узнали, как создать `CryptoObject`, позволяет перейти к см. в разделе как `FingerprintManager.AuthenticationCallbacks` используются для передачи результатов работы службы сканер отпечатков пальцев в приложение Android.



## <a name="related-links"></a>Связанные ссылки

- [Шифра](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
