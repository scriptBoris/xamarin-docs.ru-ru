---
title: Безопасность транспорта приложения в Xamarin.iOS
description: Безопасность транспорта приложения (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, server серверной части приложения) и приложения.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 0645b326576a68c97479bc5b59aabaa104f87ae2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114266"
---
# <a name="app-transport-security-in-xamarinios"></a>Безопасность транспорта приложения в Xamarin.iOS

_Безопасность транспорта приложения (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, server серверной части приложения) и приложения._

В этой статье будут представлены изменения параметров безопасности, безопасность транспорта приложения накладывает на приложение iOS 9 и [это означает для проектов Xamarin.iOS](#xamarinsupport), оно покроет [параметры конфигурации ATS](#config) и Здесь рассматриваются как [отказаться от ATS](#optout) ATS при необходимости. Так как ATS включен по умолчанию, все небезопасные подключения internet приведет к появлению исключения в приложениях iOS 9 (если она явно разрешен).


## <a name="about-app-transport-security"></a>Сведения о безопасности транспорта приложения

Как уже говорилось, ATS гарантирует все подключение к Интернету в iOS 9 и OS X El Capitan соответствия безопасное подключение рекомендации, тем самым предотвращая случайное раскрытие конфиденциальной информации, либо напрямую с помощью приложения или библиотеку, которая является Использование.

Для существующих приложений, реализовать `HTTPS` протокола, когда это возможно. Для новых приложений Xamarin.iOS, следует использовать `HTTPS` исключительно в том случае, при взаимодействии с Интернет-ресурсам. Кроме того общий API связи должны быть зашифрованы с помощью TLS версии 1.2 с безопасной пересылки.

Любого соединения, установленного с помощью [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) или [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) будет использоваться ATS по умолчанию в приложениях, созданных для iOS 9 и OS X 10.11 (El Capitan).

## <a name="default-ats-behavior"></a>Поведение по умолчанию ATS

Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9 и OS X 10.11 (El Capitan), все подключения с помощью [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) или [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) будет предметом Требования к безопасности ATS. Если эти требования не соответствуют подключений, они вызовут сбой с исключением.

### <a name="ats-connection-requirements"></a>Требования к ATS подключения

ATS будут применяться следующие требования для всех подключений к Интернету:

- Все алгоритмы шифрования соединения должны использовать пересылку (PFS). См. список ниже принятые шифров.
- Версии 1.2 или более поздней должен использоваться протокол безопасности транспортного уровня (TLS).
- Для всех сертификатов, необходимо использовать по крайней мере SHA256 пальца с 2048 бит или больше ключ RSA, или 256 бит или больше ключ Elliptic Curve (ECC).

Опять же поскольку ATS включена по умолчанию в iOS 9, любая попытка установить соединение, не соответствует этим требованиям приведет к возникновению исключения. 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Совместимые шифры ATS

Следующий тип шифра полная безопасность пересылки, принимаются ATS защищенный обмен данными в Интернете:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Дополнительные сведения о работе с iOS internet communication классов см. в разделе Apple [ссылки на класс NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), [ссылку CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) или [ссылки на класс NSURLSession ](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Поддержка ATS в Xamarin.iOS

Так как ATS включен по умолчанию в iOS 9 и OS X El Capitan, если приложение Xamarin.iOS, или любая библиотека или службы, им устанавливает подключение к Интернету, вам потребуется выполнить определенное действие или подключениями приведет к возникновению исключения.

Для имеющегося приложения, Apple предлагает, поддерживается ли `HTTPS` протокола как можно скорее. Если вы либо невозможно, так как при подключении к сторонним веб-службы, который не поддерживает `HTTPS` или если пользуетесь `HTTPS` будет крайне неэффективно, вы можете отказаться от ATS. См. в разделе [Opting горизонтального ATS](#optout) Дополнительные сведения в приведенном ниже разделе.

Для нового приложения Xamarin.iOS, следует использовать `HTTPS` исключительно в том случае, при взаимодействии с Интернет-ресурсам. Опять же, может возникнуть ситуация, (например, с помощью стороннюю службу web) где это невозможно, и вы должны отказаться от ATS.

Кроме того ATS обеспечивает высокоуровневый API обмена данными с зашифрованы с помощью TLS версии 1.2 с безопасной пересылки. См. в разделе [требования к ATS подключения](#ATS-Connection-Requirements) и [совместимых шифры ATS](#ATS-Compatible-Ciphers) разделах выше, для получения дополнительных сведений.

Хотя может быть знакомы с TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) он является преемником SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) и предоставляет коллекцию протоколов шифрования для обеспечения безопасности через Сетевые подключения.

На уровне TLS управляется, используемым веб-служба, поэтому за пределами приложения управления. Как `HttpClient` и `ModernHttpClient` будет использовать самый высокий уровень шифрования TLS, поддерживаемые сервером.

В зависимости от сервера, что идет (особенно в случае стороннюю службу), может потребоваться отключить пересылку (PFS) или выберите более низком уровне TLS. См. в разделе [Настройка варианты ATS](#Configuring-ATS-Options) Дополнительные сведения в приведенном ниже разделе.

> [!IMPORTANT]
> Безопасность транспорта приложения не применяется для приложений Xamarin с помощью **HTTPClient управляемых реализаций**. Он применяется к подключениям, с помощью CFNetwork **реализаций HTTPClient** или **NSURLSession HTTPClient реализаций** только.

### <a name="setting-the-httpclient-implementation"></a>Параметр реализация HTTPClient

Чтобы задать реализация HTTPClient, используемым приложением iOS, дважды щелкните **проекта** в **обозревателе решений** открыть **параметры проекта**. Перейдите к **сборка iOS** и выберите тип нужного клиента в разделе **реализация HttpClient** раскрывающегося списка:

![](ats-images/client01.png "Установка iOS параметры сборки")


#### <a name="managed-handler"></a>Управляемого обработчика

Управляемый это полностью управляемая обработчик HttpClient, который был отправлен с более ранними версиями Xamarin.iOS и является обработчиком по умолчанию.

Преимущества:

- Это наиболее совместимый с Microsoft .NET и более старую версию Xamarin.

Недостатки:

- Он не является полностью интегрирован с iOS (например, она ограничена TLS 1.0).
- Это обычно гораздо медленнее, чем собственных API.
- Он требует более управляемого кода и создает крупных приложениях.

#### <a name="cfnetwork-handler"></a>CFNetwork обработчика

CFNetwork на основе обработчика зависит от собственного `CFNetwork` framework.

Преимущества:

- Использует собственный API для повышения производительности и небольшого размера исполняемого файла.
- Добавлена поддержка новых стандартов, таких как TLS 1.2.

Недостатки:

- Необходима ОС iOS 6 или более поздней версии.
- Недоступно для watchOS.
- Некоторые функции HttpClient и параметры недоступны.

#### <a name="nsurlsession-handler"></a>Обработчик NSUrlSession

Обработчик NSUrlSession основе основан на собственный `NSUrlSession` API.

Преимущества:

- Использует собственный API для повышения производительности и небольшого размера исполняемого файла.
- Добавлена поддержка новых стандартов, таких как TLS 1.2.

Недостатки:

- Необходима ОС iOS 7 или более поздней.
- Некоторые функции HttpClient и параметры недоступны. 

## <a name="diagnosing-ats-issues"></a>Диагностика неполадок ATS

При попытке подключения к Интернету напрямую или с веб-представление в iOS 9, в форме может появиться ошибка:

> Безопасность транспорта приложения заблокировал в виде открытого текста HTTP (http://www.-the-blocked-domain.com) загрузки ресурсов, так как он не защищен. Временные исключения можно задать через файл Info.plist приложения.

В iOS9 приложение Transport Security (ATS) обеспечивает безопасное соединение между Интернет-ресурсов (например, server серверной части приложения) и приложения. Кроме того, ATS требуется взаимодействие с использованием `HTTPS` протокол и высокого уровня API связи с помощью TLS версии 1.2 с безопасной пересылки.

Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9 и OS X 10.11 (El Capitan), все подключения с помощью `NSURLConnection`, `CFURL` или `NSURLSession` будут применяться требования к безопасности ATS. Если эти требования не соответствуют подключений, они вызовут сбой с исключением.

Компания Apple предоставляет также [TLSTool пример приложения](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) , можно скомпилировать (или при необходимости перекодированные с Xamarin и C#) и использовать для диагностирования неполадок ATS/TLS. См. в разделе [Opting горизонтального ATS](#optout) сведения о том, как решить эту проблему в приведенном ниже разделе.


<a name="config" />

## <a name="configuring-ats-options"></a>Настройка параметров ATS

Некоторые функции ATS можно настроить, задав значения для определенных ключей в вашем приложении **Info.plist** файла. Доступны следующие разделы для управления ATS (_с отступом, чтобы показать, как они являются вложенными_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Каждый ключ имеет следующий тип и значение:

- **NSAppTransportSecurity** (`Dictionary`) — содержит все ключи параметров и значений в параметре ATS.
- **NSAllowsArbitraryLoads** (`Boolean`) — Если `YES` ATS будет отключена для любого домена **не** в `NSExceptionDomains`. Для указанных доменов будет использоваться указанные параметры безопасности.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`) — Если `YES` позволит веб-страницы правильно загрузить, хотя по-прежнему включена защита Apple Transport Security (ATS) для остальной части приложения.
- **NSExceptionDomains** (`Dictionary`) — это совокупность доменов и параметры безопасности, которые следует использовать ATS к указанному домену.
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-коллекцию исключений для данного домена (например) `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`) — минимальная версия TLS как `TLSv1.0`, `TLSv1.1` или `TLSv1.2` (по умолчанию).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`) — Если `NO` не использовать шифр прямой безопасности домена. Значение по умолчанию — `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`) — Если `NO` (по умолчанию), все связи с этим доменом должны находиться в `HTTPS` протокола.
- **NSRequiresCertificateTransparency** (`Boolean`) — Если `YES` домена Secure Sockets Layer (SSL) должны содержать допустимые прозрачности данные. Значение по умолчанию — `NO`.
- **NSIncludesSubdomains** (`Boolean`) — Если `YES` эти параметры переопределяют все поддомены этого домена. Значение по умолчанию — `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`) — используется, когда домен стороннюю службу вне контроля разработчика версии TLS.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`) — Если `YES` для 3-й домена стороны требуется полная безопасность пересылки.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`) — Если `YES` ATS позволит небезопасный обмен данными с доменами сторонних производителей.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Отказ от извлечения ATS

Хотя Apple настоятельно рекомендует `HTTPS` протокола и безопасное подключение к Интернету на основе сведений, возможны ситуации, это не всегда возможно. Например, при взаимодействии с стороннюю службу web или через Интернет, доставленных рекламы в вашем приложении.

Если приложение Xamarin.iOS должно сделать запрос к небезопасным доменом, следующие изменения в приложение **Info.plist** файла приведет к отключению безопасности по умолчанию, применяемые ATS к указанному домену:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

В Visual Studio для Mac, дважды щелкните `Info.plist` файл в **обозревателе решений**, переключитесь в **источника** просмотра и добавления выше ключи:

[![](ats-images/ats01.png "Представление источника файла Info.plist")](ats-images/ats01.png#lightbox)


Если вашему приложению необходим для загрузки и отображения веб-содержимого с сайтов, небезопасный, добавьте следующий в приложение **Info.plist** файла, чтобы разрешить веб-страницы правильно загрузить, хотя по-прежнему включена защита Apple Transport Security (ATS) для rest приложения:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

При необходимости можно внести следующие изменения в приложение **Info.plist** файл, чтобы полностью отключить ATS для всех доменов и обмен данными через Интернет:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

В Visual Studio для Mac, дважды щелкните `Info.plist` файл в **обозревателе решений**, переключитесь в **источника** просмотра и добавления выше ключи:

[![](ats-images/ats02.png "Представление источника файла Info.plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Если приложению требуется подключение на небезопасной веб-сайте, вы должны **всегда** введите домен, как исключения с помощью `NSExceptionDomains` вместо отключение ATS полностью с помощью `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` должен использоваться только в чрезвычайных ситуациях аварийного.




Опять же, отключить ATS следует _только_ использоваться в качестве последнего средства, при переключении на безопасных подключений нецелесообразно или недоступны.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье появился приложения Transport Security (ATS) и описаны способом, он обеспечивает защищенный обмен данными с Интернетом. Во-первых мы рассматривали изменения, необходимые для приложения Xamarin.iOS, работающих под управлением iOS 9 ATS. Затем мы рассматривали управление ATS функции и параметры. Наконец мы рассматривали, отказ от ATS в приложении Xamarin.iOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
