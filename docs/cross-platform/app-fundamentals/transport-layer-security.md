---
title: Безопасности транспортного уровня (TLS) 1.2
description: В этом документе описывается как для поддержки TLS 1.2 для проектов Xamarin.iOS, Xamarin.Android и Xamarin.Mac. Показывается, как сделать это в Visual Studio 2017 и Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 3946a4ba190e1d8ba5c68a2d8ac36cf42900862f
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233982"
---
# <a name="transport-layer-security-tls-12"></a>Безопасности транспортного уровня (TLS) 1.2

С помощью последней версии [ _Transport Layer Security_ (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) важна для обеспечения сетевых подключений с приложения являются безопасными.

> [!WARNING]
> **Апреля 2018 г.** — из-за повышения уровня безопасности требования, а также соблюдение стандартов PCI, основных поставщиков облачных служб и веб-серверы должны прекратив поддержку версии TLS, предшествующей версии 1.2.  Проекты Xamarin, созданные в предыдущих версиях Visual Studio по умолчанию для использования более старые версии TLS.
>
> Чтобы продолжить работу с этих серверов и служб, приложений **следует обновить проекты Xamarin для использования параметров, указанных ниже, а затем повторное построение и повторно развертывать приложения** для пользователей.

Проекты должны ссылаться на **System.Net.Http** сборки и настроить, как показано ниже.

## <a name="update-xamarinandroid-to-tls-12"></a>Обновление Xamarin.Android до TLS 1.2

Обновление **реализация HttpClient** и **реализация SSL/TLS** для включения безопасности TLS 1.2.

> [!NOTE]
> Требуется Android 5.0 или более поздней версии.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Эти параметры можно найти в **свойства проекта > Параметры Android** и щелкнув **Дополнительно** кнопки:

[![Настройка HttpClient и TLS в Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Эти параметры можно найти в **параметры проекта > Создать > сборка Android** вкладке:

[![Настройка HttpClient и TLS в Visual Studio для Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Обновите Xamarin.iOS до TLS 1.2

Обновление **реализация HttpClient** параметр, чтобы включить безопасность TSL 1.2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Этот параметр можно найти в **свойства проекта > сборка iOS**:

[![Настройка HttpClient и TLS в Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Этот параметр можно найти в **параметры проекта > Создать > сборка iOS** вкладке:

[![Настройка HttpClient в Visual Studio для Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Обновите Xamarin.Mac до TLS 1.2

В Visual Studio для Mac, чтобы включить TLS 1.2 в приложении Xamarin.Mac, обновите **реализация HttpClient** в диалоговом окне **параметры проекта > Создать > сборка Mac**:

[![Настройка HttpClient в Visual Studio для Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> Будущий выпуск Xamarin.Mac 4.8 будет поддерживать только macOS 10.9 или более поздней версии.
> Предыдущие версии Xamarin.Mac поддерживали macOS 10.7 или более поздней версии, но в этих версиях macOS нет достаточной инфраструктуры TLS для поддержки TLS 1.2. Для macOS 10.7 или macOS 10.8 используйте Xamarin.Mac 4.6 или более ранней версии.

## <a name="alternative-configuration-options"></a>Альтернативные параметры

В этом разделе рассматриваются альтернативные варианты конфигурации поддерживается TLS 1.2, показанный выше.
Разработчики приложений могут попробовать в этих вариантов, только если они понимаете риски использования разных уровней поддержки TLS.

### <a name="httpclient-implementation"></a>Реализация HttpClient

Xamarin разработчики всегда были возможность использовать собственный сетевые классы в коде, но также используется параметр, который определяет, какой сетевой стек `HttpClient` классы. Это предоставляет знакомый API .NET, обладает преимуществами собственной платформы скорость и безопасность.

Доступны следующие варианты.

- **Управляемого стека** — функциональных возможностей, предоставляемых системой Mono сети, или
- **Собственный стек** — различные сетевые интерфейсы API, предоставляемые базовых платформ (Android, iOS или macOS).

Управляемый стек предоставляет самый высокий уровень совместимости с существующим кодом .NET, однако он может выполняться медленнее и привести больший размер исполняемого файла.

Встроенные функции все же может быть быстрее и имеют более высокий уровень безопасности (включая TLS 1.2), но может не предоставлять все функциональные возможности и варианты `HttpClient` класса.

### <a name="ssltls-implementation-android"></a>Реализация SSL/TLS (Android)

Параметры проекта Android также позволяют выбрать, какую реализацию SSL/TLS для поддержки:

- **Mono/управляемые** — TLS 1.1 на устройстве Android
- **Собственный** — TLS 1.2 на устройстве Android.

По умолчанию новые проекты Xamarin к собственной реализацией, которая поддерживает TLS 1.2 (что рекомендуется для всех проектов), однако можно переключиться обратно для управляемого кода, если необходимые для обеспечения совместимости.

> [!IMPORTANT]
> **Mono или управляемого** был параметр [удалены из iOS и Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/) параметры проекта.
>
> Собственный вариант всегда используется в iOS и Mac платформ.

## <a name="platform-specific-details"></a>Сведения о конкретных платформ

Сводка выше описаны параметры на уровне проекта для реализации HttpClient и SSL/TLS в проектах Xamarin. Реализация HttpClient также можно динамически задать в коде. См. Эти платформы руководства, Дополнительные сведения:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS и Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Сводка

Приложения должны использовать уровень безопасности транспорта (TLS) 1.2, везде, где это возможно.
Следует обновить параметры в существующие приложения в соответствии с инструкциями в этой статье, а затем повторное построение и повторного развертывания у клиентов.

## <a name="related-links"></a>Связанные ссылки

- [Безопасность транспорта приложения](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (февраль 2017 г.)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Википедия)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Заметки о выпуске Mono 4.8 - поддерживает TLS 1.2](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient, HttpClientHandler и описано равно WebRequestHandler](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP-клиент (пример)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
