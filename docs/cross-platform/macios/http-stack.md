---
title: HttpClient и селектор реализации SSL/TLS для iOS и Mac OS
description: Стек HttpClient и SSL/TLS селектор реализации определяет реализации HttpClient и SSL/TLS, который будет использоваться приложения Xamarin iOS, tvOS и macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251213"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Селектор реализации HttpClient и SSL/TLS для iOS и Mac OS

**Селектор реализации HttpClient** для Xamarin.iOS, Xamarin.tvOS и Xamarin.Mac управляет `HttpClient` используемой реализации. Можно переключиться на реализацию, которая использует собственный транспортов, iOS, tvOS и macOS (`NSUrlSession` или `CFNetwork`в зависимости от операционной системы). Плюсом является TLS 1.2-поддержку, меньшее двоичные файлы быстрее и загрузит и; Недостатком является то, что требуется для выполнения асинхронных операций для выполнения цикла событий.

Проекты должны ссылаться на **System.Net.Http** сборки.

> [!WARNING]
> **Апреля 2018 г.** — из-за повышения уровня безопасности требования, а также соблюдение стандартов PCI, основных поставщиков облачных служб и веб-серверы должны прекратив поддержку версии TLS, предшествующей версии 1.2.  Проекты Xamarin, созданные в предыдущих версиях Visual Studio по умолчанию для использования более старые версии TLS.
>
> Чтобы продолжить работу с этих серверов и служб, приложений **следует обновить проекты Xamarin с `NSUrlSession` показано ниже, затем перестройте и повторно развертывать приложения** для пользователей.

### <a name="selecting-an-httpclient-stack"></a>Выбрав в стек HttpClient

Чтобы настроить `HttpClient` используется в приложении:

1. Дважды щелкните **имя_проекта** в **обозревателе решений** чтобы открыть параметры проекта.
2. Переключиться в режим **построения** параметров проекта (например, **сборка iOS** для приложения Xamarin.iOS).
3. Из **реализация HttpClient** раскрывающемся списке выберите `HttpClient` введите одно из следующих: **NSUrlSession** (рекомендуется), **CFNetwork**, или  **Управляемые**.

[![Выберите управляемый, CFNetwork или NSUrlSession реализация HttpClient](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Для поддержки TLS 1.2 `NSUrlSession` параметр рекомендуется использовать.

### <a name="nsurlsession"></a>NSUrlSession

`NSURLSession`-На основе обработчика зависит от собственного `NSURLSession` framework, доступных в iOS 7 и более поздних. 
**Это рекомендуемый параметр.**

#### <a name="pros"></a>Преимущества

- Она использует собственные API-интерфейсы для повышения производительности и меньший размер исполняемого файла.
- Поддержка последних стандартов, таких как TLS 1.2.

#### <a name="cons"></a>Недостатки

- Необходима ОС iOS 7 или более поздней.
- Некоторые `HttpClient` функции и параметры будут недоступны.

### <a name="cfnetwork"></a>CFNetwork

`CFNetwork`-На основе обработчика зависит от собственного `CFNetwork` framework, доступных в iOS 6 и более поздних.

#### <a name="pros"></a>Преимущества

- Она использует собственные API-интерфейсы для повышения производительности и меньший размер исполняемого файла.
- Поддержка новых стандартов, таких как TLS 1.2.

#### <a name="cons"></a>Недостатки

- Необходима ОС iOS 6 или более поздней версии.
- Доступно не для watchOS.
- Некоторые функции HttpClient/параметры недоступны.

### <a name="managed"></a>Управляемый

Управляемый это полностью управляемая обработчик HttpClient, который был отправлен с помощью предыдущей версии Xamarin.

#### <a name="pros"></a>Преимущества

- Он имеет функцию наиболее совместимый набор с помощью Microsoft .NET и более старые версии Xamarin.

#### <a name="cons"></a>Недостатки

- Он не полностью интегрирована с ОС Apple и ограничен TLS 1.0. Он не может быть возможность подключиться к безопасным веб-серверам или облачных служб в будущем.
- Он обычно гораздо медленнее варианты для шифрования собственных API.
- Он требует более управляемого кода, тем самым создавая распространяемый приложение большего размера.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Программная установка HttpMessageHandler

В дополнение к конфигурации на уровне проекта, показанный выше, можно также создать экземпляр `HttpClient` и внедрить требуемый `HttpMessageHandler` через конструктор, как показано в этих фрагментах кода:

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Это дает возможность использовать другой `HttpMessageHandler` из в **параметры проекта** диалоговое окно.

## <a name="ssltls-implementation"></a>Реализация SSL/TLS

Протокол SSL (Secure Socket Layer) и его "преемник", TLS (Transport Layer Security), обеспечивают поддержку HTTP и других сетевых подключений с помощью `System.Net.Security.SslStream`. Xamarin.iOS, Xamarin.tvOS или в Xamarin.Mac `System.Net.Security.SslStream` реализация будет вызывать Apple собственная реализация SSL/TLS, вместо того чтобы использовать управляемую реализацию, предоставляемые Mono. Собственная реализация Apple поддерживает TLS 1.2.

> [!WARNING]
> Предстоящий выпуск Xamarin.Mac 4.8 поддерживает только macOS 10.9 или более поздней версии.
> Предыдущие версии Xamarin.Mac поддерживается macOS 10.7 или более поздней версии, но эти устаревшие версии macOS не достаточно TLS инфраструктуры для поддержки TLS 1.2. Для macOS 10.7 или macOS — 10.8 используйте Xamarin.Mac 4.6 или более ранней версии.

## <a name="app-transport-security"></a>Безопасность транспорта приложения

Apple _безопасность транспорта приложения_ (ATS) применяет безопасных соединений между Интернет-ресурсов (например, server серверной части приложения) и приложения. ATS гарантирует все Интернет-коммуникациями соответствия безопасное подключение рекомендации, тем самым предотвращая случайное раскрытие конфиденциальной информации, либо напрямую с помощью приложения или библиотеку, которая потребляет.

Поскольку ATS включена по умолчанию в приложениях, созданных для iOS 9, tvOS 9 и OS X 10.11 (El Capitan) и более поздних версиях, все подключения с помощью `NSUrlConnection`, `CFUrl` или `NSUrlSession` будут применяться требования к безопасности ATS. Если подключения не удовлетворяют этим требованиям, они вызовут сбой с исключением.

С учетом выбранных HttpClient Stack и реализация SSL/TLS, может потребоваться внести изменения в приложения, чтобы правильно работать со ATS.

Чтобы подробнее узнать о ATS, см. наш [руководство по безопасности транспорта приложения](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Известные проблемы

В этом разделе рассматриваются известные проблемы с поддержкой TLS в Xamarin.iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Не удалось загрузить с ошибкой «Запрошенное значение, который не был найден AppleTLS» проект

Xamarin.iOS 9.8 появились некоторые новые параметры, которые содержатся **.csproj** файл для приложения Xamarin.iOS. Эти изменения могут вызвать проблемы при открытии проекта с более ранними версиями Xamarin.iOS. Ниже приведен пример сообщения об ошибке, которое может отображаться в этом сценарии:

![Снимок экрана: ошибка при попытке загрузить проект, запросу не найдено значение для прежних версий](http-stack-images/tlserror-xs.png)

Эта ошибка вызвана появлением `MtouchTlsProvider` параметр в файл проекта в Xamarin.iOS 9.8. Если это не может изменять для Xamarin.iOS 9.8 (или более поздней версии), способ — вручную изменить **.csproj** файл приложения, удалите `MtouchTlsprovider` элемент, а затем сохраните файл изменил проект.

Ниже приведен пример того, что `MtouchTlsProvider` параметр может выглядеть как внутри **.csproj** файла:

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Связанные ссылки

- [Протокол TLS](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Безопасность транспорта приложения](~/ios/app-fundamentals/ats.md)
