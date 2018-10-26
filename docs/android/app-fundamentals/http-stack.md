---
title: HttpClient Stack и селектор реализации SSL/TLS для Android
description: Стек HttpClient и реализация SSL/TLS селекторы определить реализация HttpClient и SSL/TLS, который будет использоваться для приложений Xamarin.Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 47c9ddf3f1a61b0ec7e2a8ed993ad665267993fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114279"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient Stack и селектор реализации SSL/TLS для Android

Стек HttpClient и реализация SSL/TLS селекторы определить реализация HttpClient и SSL/TLS, который будет использоваться для приложений Xamarin.Android.

Проекты должны ссылаться на **System.Net.Http** сборки.

> [!WARNING]
> **Апреля 2018 г.** — из-за повышения уровня безопасности требования, а также соблюдение стандартов PCI, основных поставщиков облачных служб и веб-серверы должны прекратив поддержку версии TLS, предшествующей версии 1.2.  Проекты Xamarin, созданные в предыдущих версиях Visual Studio по умолчанию для использования более старые версии TLS.
>
> Чтобы продолжить работу с этих серверов и служб, приложений **следует обновить проекты Xamarin с `Android HttpClient` и `Native TLS 1.2` параметры, показано ниже, затем перестройте и повторно развертывать приложения** для вашей пользователи.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Конфигурация Xamarin.Android HttpClient **параметры проекта > Параметры Android**, нажмите кнопку **Дополнительно** кнопки.

Ниже приведены рекомендуемые параметры для поддержки TLS 1.2:

[![Параметры Android Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Используется конфигурация Xamarin.Android HttpClient **параметры проекта > Создать > сборка Android** параметры и щелкните **Общие** вкладки.

Ниже приведены рекомендуемые параметры для поддержки TLS 1.2:

[![Visual Studio для Mac параметры Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Альтернативные параметры

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler является новый обработчик, который делегирует в машинный код Java/OS, вместо реализации все, что в управляемом коде.
**Это рекомендуемый вариант.**

#### <a name="pros"></a>Преимущества

- Используйте собственный API для повышения производительности и меньший размер исполняемого файла.
- Поддержка последних стандартов, например:. TLS 1.2.

#### <a name="cons"></a>Недостатки

- Требуется Android 5.0 или более поздней версии.
- Некоторые функции HttpClient/параметры недоступны.

### <a name="managed-httpclienthandler"></a>Управляемый (HttpClientHandler)

Управляемых это полностью управляемая HttpClient обработчик, который был отправлен с предыдущими версиями Xamarin.Android.

#### <a name="pros"></a>Преимущества

- Это наиболее совместимый (компоненты) с MS .NET и более старые версии Xamarin.

#### <a name="cons"></a>Недостатки

- Она не интегрирована полностью операционной системы (например) ограничивается использованием TLS 1.0).
- Это обычно гораздо более медленный (например) шифрование) чем собственного API.
- Она требует более управляемый код, создание крупных приложений.



### <a name="choosing-a-handler"></a>Выбор обработчика

Выбор между `AndroidClientHandler` и `HttpClientHandler` зависит от потребностей приложения. `AndroidClientHandler` рекомендуется для наиболее актуальные поддержки безопасности, например:.

-   Вам нужно поддерживать протокол TLS 1.2 +.
-   Приложения предназначен для платформы Android 5.0 (API 21) или более поздней версии.
-   Вам потребуется TLS 1.2 + Поддержка `HttpClient`.
-   Не требуется поддержка TLS 1.2 + `WebClient`.

`HttpClientHandler` — Это хороший выбор, если вам нужна TLS 1.2 + поддержки, но должен поддерживать версиях Android до Android 5.0. Также является хорошим выбором при необходимости TLS 1.2 + Поддержка `WebClient`.

Начиная с Xamarin.Android 8.3 `HttpClientHandler` Boring SSL по умолчанию (`btls`) как базовый поставщик TLS. Поставщик Boring SSL TLS обеспечивает следующие преимущества:

-   Он поддерживает TLS 1.2 +.
-   Он поддерживает все версии Android.
-   Он предоставляет протокол TLS 1.2 + поддерживает оба `HttpClient` и `WebClient`.

Недостатком использования Boring SSL, так как поставщик TLS базового является то, что его можно увеличить размер итоговом пакете APK (он добавляет дополнительный размер пакета APK на поддерживаемых ABI около 1 МБ).

Начиная с Xamarin.Android 8.3, поставщик TLS по умолчанию — Boring SSL (`btls`). Если вы не хотите использовать Boring SSL, можно вернуться к исторических управляемая реализация протокола SSL, задав `$(AndroidTlsProvider)` свойства `legacy` (Дополнительные сведения о настройке свойств сборки см. в разделе [процесса сборки](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Программно с помощью `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler` Является `HttpMessageHandler` реализации специально для Xamarin.Android.
Экземпляры этого класса будет применять собственную `java.net.URLConnection` реализации для всех HTTP-соединений. Теоретически, это позволит обеспечить увеличение производительности HTTP и небольшого размера пакета APK.

В этом фрагменте кода приведен пример того, как явным образом для одного экземпляра `HttpClient` класса:

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> Базовое устройство Android должно поддерживать протокол TLS 1.2 (т. е. Android 5.0 и более поздние версии)


## <a name="ssltls-implementation-build-option"></a>Параметр сборки реализация SSL/TLS

Этот параметр проекта определяет, какие базовую библиотеку TLS будут использоваться все веб-запроса, оба `HttpClient` и `WebRequest`. По умолчанию выбирается протокол TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Поле со списком Реализация протоколов TLS/SSL в Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Поле со списком Реализация протоколов TLS/SSL в Visual Studio для Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Пример:

```csharp
var client = new HttpClient();
```

Если было задано реализация HttpClient **управляемый** и реализация TLS было присвоено **собственный TLS 1.2 +**, то `client` объекта будет автоматически использовать управляемый `HttpClientHandler` и Протокол TLS 1.2 (предоставляемый библиотекой BoringSSL) для его HTTP-запросов.

Тем не менее если **реализация HttpClient** присваивается `AndroidHttpClient`, затем все `HttpClient` объектов будет использовать основной класс Java `java.net.URLConnection` и будут затронуты процедурой **Реализация протоколов TLS/SSL** значение. `WebRequest` объекты будет использовать библиотеку BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Другие способы управления конфигурацией SSL/TLS

Существует три способа, что приложения Xamarin.Android можно управлять параметрами TLS:

1. Выберите библиотеку TLS HttpClient внедрения и по умолчанию в параметрах проекта.
2. Программно с помощью `Xamarin.Android.Net.AndroidClientHandler`.
3. Объявите переменные среды (необязательно).

Из трех вариантов, рекомендуется использовать для объявления по умолчанию параметры проекта Xamarin.Android `HttpMessageHandler` и TLS для всего приложения. Затем, при необходимости, программно создать экземпляр `Xamarin.Android.Net.AndroidClientHandler` объектов. Эти параметры описаны выше.

Третий вариант &ndash; использование переменных среды &ndash; описан ниже.

### <a name="declare-environment-variables"></a>Объявите переменные среды

Существует две переменные среды, которые связаны с использованием TLS в Xamarin.Android:

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Значение по умолчанию объявляет эту переменную среды `HttpMessageHandler` , будет использоваться приложением. Пример:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Эта переменная среды будет объявлять, какие TLS библиотека будет использоваться, либо `btls`, `legacy`, или `default` (Это равносильно опусканию эта переменная):

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Эта переменная среды устанавливается путем добавления _файла среды_ в проект. Файл среды — это файл текстовом формате Unix с действием сборки **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Снимок экрана AndroidEnvironment действие построения в Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Снимок экрана AndroidEnvironment построить действие в Visual Studio для Mac.](http-stack-images/tls03-xs.png)

-----

См. в разделе [среды Xamarin.Android](~/android/deploy-test/environment.md) руководство Дополнительные сведения о переменных среды и Xamarin.Android.


## <a name="related-links"></a>Связанные ссылки

- [Протокол TLS](~/cross-platform/app-fundamentals/transport-layer-security.md)
