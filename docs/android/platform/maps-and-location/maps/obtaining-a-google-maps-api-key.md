---
title: Получение Google Maps ключ API
description: Как получить ключ API карт Google для добавления сопоставляет функциональные возможности приложения.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120025"
---
# <a name="obtaining-a-google-maps-api-key"></a>Получение Google Maps ключ API

Чтобы использовать функцию Google карты в Android, необходимо зарегистрироваться для получения ключа API для карт с помощью Google. До этого вы увидите только пустую сетку вместо карту в ваших приложениях. Необходимо получить ключ v2 Google Maps Android API - ключи из более старых Google Maps Android API ключа версии 1 не будет работать.

Получение ключа API для карт v2 состоит из следующих шагов:

1.  Получите отпечаток SHA-1 из хранилища ключей, который используется для подписи приложения.
2.  Создайте проект в консоли Google API.
3.  Получение ключа API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Получение ключей подписи отпечаток пальца

Чтобы запросить ключ API для карт от Google, вы должны знать отпечаток SHA-1 из хранилища ключей, который используется для подписи приложения.
Как правило это означает, что необходимо будет определить отпечаток SHA-1 для хранилища ключей отладки, а затем отпечатков SHA-1 для хранилища ключей, который используется для подписи приложения для выпуска.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

По умолчанию хранилище ключей, которое используется для подписывания отладочных версий Xamarin.Android, можно найти по следующему адресу:

**C:\\пользователей\\[USERNAME]\\AppData\\локального\\Xamarin\\Mono для Android\\debug.keystore**

Информация о хранилище ключей отображается при вызове команды `keytool` из JDK. Это средство обычно находится в каталоге bin Java:

**C:\\Program Files (x86)\\Java\\jdk [VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

По умолчанию хранилище ключей, которое используется для подписывания отладочных версий Xamarin.Android, можно найти по следующему адресу:

**/Users/[username]/.Local/Share/Xamarin/Mono для Android/debug.keystore**

Информация о хранилище ключей отображается при вызове команды `keytool` из JDK. Это средство обычно находится в каталоге bin Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Выполните keytool, используя следующую команду (с использованием пути к файлам, показанный выше):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Пример Debug.KeyStore

Для ключа отладки по умолчанию (который будет автоматически создана для отладки) используйте следующую команду:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Рабочие ключи

При развертывании приложения в Google Play, он должен быть [подписан закрытым ключом](~/android/deploy-test/signing/index.md).
`keytool` Нужно будет выполняться с закрытого ключа сведения и итоговый отпечаток SHA-1 используется для создания ключа Google Maps API рабочей среде. Не забудьте обновить **AndroidManifest.xml** файла с правильным ключом Google Maps API перед развертыванием.

### <a name="keytool-output"></a>Выходные данные Keytool

Вы должны увидеть примерно следующие выходные данные в окне консоли:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

Будет использоваться отпечаток SHA-1 (перечисленных после **SHA1**) Далее в этом руководстве.

## <a name="creating-an-api-project"></a>Создание проекта API

После получения отпечатка SHA-1 подписи хранилища ключей, бывает необходимо создать новый проект в консоли Google API (или добавить службу Google Maps Android API версии 2 в существующий проект).

1. В браузере перейдите к [API консоли разработчиков Google и панель мониторинга служб](https://console.developers.google.com/apis/dashboard/) и нажмите кнопку **выберите проект**. Щелкните имя проекта или создайте новую, нажав кнопку **новый ПРОЕКТ**:

   [![Создание проекта Google Developer консоли кнопки](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Если вы создали новый проект, введите имя проекта в **новый проект** отображаемом диалоговом окне. Это диалоговое окно будет производить код уникальный проекта, основанный на имя проекта. Затем щелкните **создать** кнопку, как показано в следующем примере:

   [![Новый проект называется XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Через минуту создается проект и вы перейдете к **панели мониторинга** страницы проекта. После этого нажмите кнопку **ВКЛЮЧИТЬ API-Интерфейсы и службы**:

   [![Щелкнув Google Maps Android API в разделе библиотеки](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Из **библиотеки API** щелкните **Maps SDK для Android**. На следующей странице щелкните **ВКЛЮЧИТЬ** для включения службы для этого проекта:

   [![Нажав кнопку "Разрешить" в разделе панели мониторинга](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

На этом этапе создания проекта API и Google Maps Android API версии 2 была добавлена к нему. Тем не менее нельзя использовать этот API в вашем проекте, пока вы не создадите учетные данные для него. В следующем разделе показано, как создать ключ API и приложения Xamarin.Android утвержденный список, чтобы она авторизована для использования этого ключа.

## <a name="obtaining-the-api-key"></a>Получение ключа API

После **Google Developer Console** API проект был создан, бывает необходимо создать ключ Android API. Приложения Xamarin.Android должны иметь ключ API, перед предоставлением им доступа к API Android карты v2.

1. В **Maps SDK для Android** страницы, отображаемой (после нажатия кнопки **ВКЛЮЧИТЬ** на предыдущем шаге), перейдите в меню **учетные данные** вкладку и нажмите кнопку **создать учетные данные** кнопки:

   [![Пакет SDK карт для Android учетные данные сообщения](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Нажмите кнопку **ключ API**:

   [![Добавление учетных данных в диалоговое окно проекта](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. После нажатия этой кнопки создается ключ API. Далее необходимо ограничить этот ключ, таким образом, чтобы только ваше приложение может вызвать API-интерфейсов с помощью этого ключа. Нажмите кнопку **ключ RESTRICT**:

   [![Щелкнув ограничения ключа на странице учетных данных](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Изменение **имя** из **API клавиша 1** имени, которое поможет вспомнить, для чего используется ключ (**XamarinMapsDemoKey** используется в этом примере). Затем щелкните **приложений Android** "переключатель":

   [![Выбор приложений Android на странице учетных данных](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Чтобы добавить отпечаток SHA-1, щелкните **+ добавить имя пакета и отпечатков пальцев**:

   [![Добавить имя пакета и отпечатков пальцев](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Введите имя пакета приложения и введите отпечаток сертификата SHA-1 (полученных с помощью `keytool` как описано ранее в этом руководстве). В следующем примере имя пакета для `XamarinMapsDemo` является ввода, а затем отпечаток SHA-1 сертификата, полученный из **debug.keystore**:

   [![Введенное имя пакета является com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Обратите внимание, что, чтобы пакет APK для доступа к Google карты, необходимо включить отпечатки пальцев SHA-1 и упаковать имена для каждого хранилища ключей (отладочную и окончательную), которые используются для входа пакет APK. Например если вы используете один компьютер для отладки и другой компьютер для создания выпуска APK, должно содержать отпечаток SHA-1 сертификата из хранилища ключей отладки первого компьютера и отпечаток SHA-1 сертификата из хранилища ключей выпуска из второй компьютер. Нажмите кнопку **+ добавить имя пакета и отпечатков пальцев** добавить другое имя отпечатков пальцев и пакета, как показано в следующем примере:

   [![Добавление другой отпечаток пальца создает еще один сертификат SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Чтобы сохранить внесенные изменения, нажмите кнопку **Save** (Сохранить). После этого вы вернетесь к списку ключей API. Если у вас есть другие ключи API, которые вы создали ранее, они также отображаются здесь. В этом примере отображается только один ключ API (созданный на предыдущих шагах):

   [![XamarinMapsDemoKey отображается в списке ключей API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Подключение проекта к учетной записи оплаты

Начиная с июня, 11 2018, ключ API не будет работать, если проект не подключен к учетной записи оплачиваемых (даже если она все еще доступно бесплатно для мобильных приложений).

1. Нажмите кнопку меню "гамбургер" и выберите **выставления счетов** страницы:

   [![Выбрав в разделе выставления счетов меню "гамбургер"](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Связать проект с создания платежного счета, щелкнув **связать учетную запись выставления счетов** следуют **создать учетную запись выставления СЧЕТОВ** отображаемого контекстного меню (если у вас нет учетной записи, вам будет рекомендовано создайте новую):

   [![Ссылки проекта для платежного счета](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Добавление ключа в проекте

Наконец, добавьте этот ключ API для **AndroidManifest.XML** файл приложения Xamarin.Android. В следующем примере `YOUR_API_KEY` нужно заменить с помощью ключа API, созданные на предыдущих шагах:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Связанные ссылки

- [Консоли Google API](https://code.google.com/apis/console/)
- [Ключ API Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
