---
title: Отображение вашей цифровой подписи хранилища ключей
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d32b2a20fee6b2bb007ee620e0ae4203e950bb98
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112478"
---
# <a name="finding-your-keystores-signature"></a>Отображение вашей цифровой подписи хранилища ключей

MD5 и SHA1 сигнатуры приложений Xamarin.Android зависят от файла **.keystore**, который использовали для подписи APK-файла. Обычно отладочная сборка и сборка выпуска используют различные файлы **.keystore**.

## <a name="for-debug--non-custom-signed-builds"></a>Для подписанных отладочных/ненастраиваемых сборок

Xamarin.Android подписывает все отладочные сборки при помощи одного и того же файла **debug.keystore**. Этот файл создается при первой установке Xamarin.Android. Ниже описывается процедура отображения MD5 и SHA1 сигнатур файла Xamarin.Android **debug.keystore** по умолчанию.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Найдите файл Xamarin **debug.keystore**, используемый для подписывания приложений. По умолчанию хранилище ключей, которое используется для подписывания отладочных версий приложений Xamarin.Android, располагается по следующему пути:

**C:\\Users\\*ИМЯ_ПОЛЬЗОВАТЕЛЯ*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

Информация о хранилище ключей отображается при вызове команды `keytool.exe` из JDK. Обычно она располагается по следующему пути:

**C:\\Program Files (x86)\\Java\\jdk*VERSION*\\bin\\keytool.exe**

Добавьте каталог, содержащий файл **keytool.exe**, в переменную среды `PATH`.
Откройте **командную строку** и запустите `keytool.exe` при помощи следующей команды:

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

При запуске **keytool.exe** должен выводить следующий текст. Метки **MD5:** и **SHA1:** указывают на соответствующие сигнатуры:

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Найдите файл Xamarin **debug.keystore**, используемый для подписывания приложений. По умолчанию хранилище ключей, которое используется для подписывания отладочных версий приложений Xamarin.Android, располагается по следующему пути:

**~/.local/share/Xamarin/Mono for Android/debug.keystore**


Информация о хранилище ключей отображается при вызове команды **keytool** из JDK. Обычно она располагается по следующему пути:

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

Добавьте каталог, содержащий файл **keytool**, в переменную среды **PATH**.
Откройте **Терминал** и запустите **keytool** с помощью следующей команды:

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

При запуске **keytool** должен выводить следующий текст. Метки **MD5:** и **SHA1:** указывают на соответствующие сигнатуры:

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>Для подписанных сборок выпуска и настраиваемых сборок

Процесс для сборок выпуска, подписанных собственным файлом **.keystore**, совпадает с процессом, описанным выше, за исключением того, что вместо файла **debug.keystore** Xamarin.Android использует файл **.keystore** для выпуска. При создании файла хранилища ключей измените значения пароля хранилища ключей и имени псевдонима на свои.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Когда для подписания приложения Xamarin.Android используется мастер **Распространить** в Visual Studio, получаемое хранилище ключей располагается в следующем месте:

**C:\\Users\\*Имя_пользователя*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\*Псевдоним*\\*Псевдоним*.keystore**

Например, при создании нового ключа подписи при помощи диалогового окна [Создать новый сертификат](~/android/deploy-test/signing/index.md#newcertvs) ключевое хранилище из этого примера будет находиться по следующему пути:

**C:\\Users\\*ИМЯ_ПОЛЬЗОВАТЕЛЯ*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

Дополнительные сведения о подписывании приложений Xamarin.Android см. в разделе [Подписывание пакета приложения для Android](~/android/deploy-test/signing/index.md).


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

При подписывании приложения в Visual Studio для Mac при помощи мастера **Подписать и распространить...** ключевое хранилище будет находиться по следующему пути:

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

Например, при создании нового ключа подписи при помощи диалогового окна [Создать новый сертификат](~/android/deploy-test/signing/index.md#newcertxs) ключевое хранилище из этого примера будет находиться по следующему пути:

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

Дополнительные сведения о подписывании приложений Xamarin.Android см. в разделе [Подписывание пакета приложения для Android](~/android/deploy-test/signing/index.md).


-----
