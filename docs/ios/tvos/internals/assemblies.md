---
title: Поддерживаемые сборки с Xamarin для tvOS
description: Чтобы уточнить функций, доступных для приложения tvOS, этот документ предоставляет список сборок, поддерживаемых Xamarin для разработки приложений tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 89f2d4b1a4b58f49ab859d3603433427d05c7393
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996565"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Поддерживаемые сборки с Xamarin для tvOS

## <a name="supported-assemblies"></a>Поддерживаемые сборки

Это список сборок, поддерживаемых Xamarin для приложений Xamarin.tvOS. Ниже приводится подробный список этих.  Включить некоторые важные пропусков `System.EnterpriseServices`, стек ASP.NET и Windows.Forms.

|Assembly|Добавлено|Совместимость API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Для компиляторов.|
|Mono.Data.Sqlite.dll|1.2|Поставщик ADO.NET для SQLite; см. в разделе [ограничения](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Поддержка протокола потока табличных данных; используется для [System.Data.SqlClient](xref:System.Data.SqlClient) поддерживают в [System.Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|Криптографические API.|
|monotouch.dll|1.0|Эта сборка содержит [C# привязки к платформе CocoaTouch API](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|OpenGL/OpenAL объектно ориентированные API-интерфейсы, [расширенной поддержки устройства iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), а также типы из следующих пространств имен: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx), [с некоторые функциональные возможности удаления](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Клиент полный oData.|
|System.Drawing;|1.0|System.Drawing API — только классический API.<br />_System.Drawing не поддерживается на единый API Xamarin.Mac .NET 4.5 или мобильных платформ._|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) стека, заданное на [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), а также типы из следующих пространств имен: <ul><li>Система</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); частью [System.Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) поддержки.|
|System.Web.Services|1.1|[Базовых веб-служб](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) из профиля .NET 3.5, с сервера компоненты, удаленные.|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Переносимые библиотеки классов

Помимо привязки Mac, способные Xamarin.tvOS [переносимые библиотеки классов .NET](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Связанные ссылки

- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
