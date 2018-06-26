---
title: Получение Google сопоставляет ключ API
description: Как получить ключ API сопоставляет Google для добавления сопоставляет функциональные возможности приложения.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: 365bc56c70ef903622c3a4583a30460f907b4ec9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935058"
---
# <a name="obtaining-a-google-maps-api-key"></a>Получение Google сопоставляет ключ API

Чтобы использовать функции карты Google в Android, необходимо зарегистрировать для ключа Maps API с Google. До этого вы увидите только что пустая сетка вместо карты в ваших приложениях. Необходимо получить ключ API, карты Google Android v2 - ключи из старых v1 ключа API Android Google карты не будет работать.

Получение ключа v2 Maps API состоит из следующих шагов:

1.  Получение отпечатка SHA-1 из хранилища ключей, используемого для подписания приложения.
2.  Создание проекта в консоли Google API.
3.  Получить ключ API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Получение пальца подписывания ключа

Чтобы запросить ключ Maps API Google, необходимо знать отпечатка SHA-1 из хранилища ключей, используемого для подписания приложения.
Как правило это означает, будет необходимо определить отпечаток SHA-1 для отладки хранилище ключей, а затем отпечаток пальца SHA-1 для хранилища ключей, используемый для подписи приложения для выпуска.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

По умолчанию хранилище ключей, используемого для подписания отладочные версии Xamarin.Android, его можно найти по следующему адресу:

**C:\\пользователей\\[USERNAME]\\AppData\\локального\\Xamarin\\Mono для Android\\debug.keystore**

Информация о хранилище ключей отображается при вызове команды `keytool` из JDK. Это средство обычно находится в каталоге bin Java:

**C:\\Program Files (x86)\\Java\\jdk [версия]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

По умолчанию хранилище ключей, используемого для подписания отладочные версии Xamarin.Android, его можно найти по следующему адресу:

**/Users/[username]/.Local/Share/Xamarin/Mono для Android/debug.keystore**

Информация о хранилище ключей отображается при вызове команды `keytool` из JDK. Это средство обычно находится в каталоге bin Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Выполните keytool, используя следующую команду (с использованием пути к файлам, показанный выше).

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Пример Debug.KeyStore

Для ключа отладки по умолчанию (который автоматически создается для отладки) используйте следующую команду:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Ключи производства

При развертывании приложения в Google Play, он должен быть [подписывается закрытым ключом](~/android/deploy-test/signing/index.md).
`keytool` Будет необходимо запустить с закрытого ключа сведения и полученный отпечатка SHA-1, используется для создания ключа API Google сопоставления рабочей. Не забудьте обновить **AndroidManifest.xml** файла с правильным ключом Google Maps API перед развертыванием.

### <a name="keytool-output"></a>Выходные данные Keytool

Вы увидите примерно следующие выходные данные в окне консоли:

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

После получения отпечатка SHA-1 из ключей подписывания, она необходима для создания нового проекта в консоли Google API (или добавьте службу v2 API Android Google карты к существующему проекту).

1. В браузере перейдите к [консоли API Google разработчики & панели мониторинга службы](https://console.developers.google.com/apis/dashboard/) и нажмите кнопку **выберите проект**. Щелкните имя проекта или создайте новую, нажав кнопку **новый ПРОЕКТ**:

   [![Создание проекта Google Developer консоли кнопки](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. При создании нового проекта, введите имя проекта в **новый проект** отображаемом диалоговом окне. Это диалоговое окно будет производить идентификатор уникальный проекта, который основан на имя проекта. Далее щелкните **создать** кнопки, как показано в следующем примере:

   [![Новый проект с именем XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Прошествии минуты, создается проект и вы перейдете на **мониторинга** страницы проекта. После этого щелкните **ВКЛЮЧИТЬ службы и API-Интерфейсы**:

   [![Щелкнув Google Maps API Android в разделе «библиотека»](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Из **библиотеки API** щелкните **Maps пакет SDK для Android**. На следующей странице щелкните **ВКЛЮЧИТЬ** для включения службы для данного проекта:

   [![Нажав кнопку "ВКЛЮЧИТЬ" в разделе панели мониторинга](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

На этом этапе создания проекта API и v2 API, карты Google Android был добавлен к нему. Тем не менее этот API нельзя использовать в проекте пока создать учетные данные для него. В следующем разделе показано, как создать ключ API и приложение Xamarin.Android белый список, чтобы он право на использование этого ключа.

## <a name="obtaining-the-api-key"></a>Получить ключ API

После **Google Developer Console** API проект был создан, его необходимо создать ключ Android API. Xamarin.Android приложения должны иметь ключ API, перед предоставлением им доступа к API Android карты v2.

1. В **Maps пакет SDK для Android** страница, отображаемая (после нажатия кнопки **ВКЛЮЧИТЬ** на предыдущем шаге), перейдите в меню **учетные данные** и нажмите кнопку **создать учетные данные** кнопки:

   [![Сопоставляет пакета SDK для Android учетные данные сообщения](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Нажмите кнопку **ключ API**:

   [![Добавьте учетные данные в диалоговое окно проекта](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. После нажатия этой кнопки создается ключ API. Далее нужно ограничивать этот ключ, чтобы только приложение может вызывать API-интерфейсы с этим ключом. Нажмите кнопку **ограничение ключа**:

   [![Щелкнув ограничения ключа на странице учетных данных](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Изменение **имя** из **1 ключ API** имени, которое поможет вспомнить, для чего используется ключ (**XamarinMapsDemoKey** используется в этом примере). Далее щелкните **приложений Android** переключателя:

   [![При выборе приложений Android на странице учетных данных](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Чтобы добавить отпечатка SHA-1, щелкните **+ добавить имя пакета и отпечатков пальцев**:

   [![Добавить имя пакета и отпечатков пальцев](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Введите имя пакета приложения и введите отпечаток сертификата SHA-1 (полученных с помощью `keytool` как описано ранее в этом руководстве). В следующем примере имя пакета для `XamarinMapsDemo` равно введены, отпечаток сертификата SHA-1, полученный от **debug.keystore**:

   [![Введенное имя пакета — com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Обратите внимание, чтобы ваш APK для доступа к карты Google, необходимо включить отпечатки пальцев SHA-1 и имена для каждого хранилища ключей (отладочной и окончательной), использовать для входа на apk-ФАЙЛ пакета. Например если использовать один компьютер для отладки и другой компьютер для создания выпуска apk-ФАЙЛ, следует включать отпечаток сертификата SHA-1 из хранилища ключей отладки первого компьютера и отпечаток сертификата SHA-1 из хранилища ключей выпуска из второй компьютер. Нажмите кнопку **+ добавить имя пакета и отпечатков пальцев** для добавления другой отпечатков пальцев и имени пакета, как показано в следующем примере:

   [![Добавление другого отпечатков пальцев создает еще один сертификат SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Чтобы сохранить внесенные изменения, нажмите кнопку **Save** (Сохранить). Далее вы вернетесь на список ключей API. Если у вас есть другие ключи API, созданные в более ранней версии, они также отображаются здесь. В этом примере отображается только один ключ API (созданного на предыдущих этапах):

   [![XamarinMapsDemoKey отображается в списке ключей API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Подключения к учетной записи оплачиваемых проекта

Начиная с июня, 11 2018, ключ API невозможно, если проект не подключен к учетной записи оплачиваемых (даже если служба по-прежнему может для мобильных приложений).

1. Нажмите кнопку меню гамбургер и выберите **выставления счетов** страницы:

   [![При выборе раздела выставления счетов меню гамбургер](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Связать проект с записью выставления счетов, щелкнув **связать учетную запись выставления счетов** следуют **создать учетную запись выставления СЧЕТОВ** во всплывающем окне отображается (если у вас нет учетной записи, будут выданы для создания нового):

   [![Ссылка проекта с записью выставления счетов](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Добавление раздела в проект

Наконец, добавьте этот ключ API для **AndroidManifest.XML** файл Xamarin.Android приложения. В следующем примере `YOUR_API_KEY` заменяемого с API-ключ, созданный на предыдущих шагах:

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
- [Ключ API Google карты](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
