---
title: Доступные сборки
description: В этом документе перечислены сборок, доступных для использования в Xamarin.iOS, Xamarin.Android и Xamarin.Mac. Также содержит ссылки на документацию по .NET Standard библиотек и переносимых библиотек классов.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: 213632ae26ae60797e39bc718a95057fb7238609
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113356"
---
# <a name="available-assemblies"></a>Доступные сборки

Xamarin.iOS, Xamarin.Android и Xamarin.Mac все поставляются вместе с более чем десяти различных сборок. Так же, как Silverlight является расширенной подмножество классические сборки .NET, платформы Xamarin также является расширенной подмножеством несколько Silverlight и классические сборки .NET.

Платформы Xamarin не ABI, совместимый с существующей сборки, скомпилированные для другой профиль. Необходимо выполнить повторную компиляцию исходного кода для создания сборки, предназначенные для нужной конфигурации (так же, как вам потребуется выполнить повторную компиляцию исходного кода для работы с Silverlight и .NET 3.5 отдельно).

Приложения Xamarin.Mac можно скомпилировать в трех режимах:, использующую Xamarin проверенный Mobile Profile, .NET Framework 4.5 Xamarin.Mac, на котором можно выбирать существующие сборки настольные и найден неподдерживаемый тот, который использует API-Интерфейс .NET в системе Mono Установка. Дополнительные сведения см. в разделе наших [целевых платформ](~/mac/platform/target-framework.md) документации.

## <a name="net-standard-libraries"></a>Стандартные библиотеки .NET

В дополнение к iOS, Android и Mac привязки, могут использовать проекты Xamarin [библиотеки .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Переносимые библиотеки классов

Можно также использовать проекты Xamarin [переносимые библиотеки классов .NET](~/cross-platform/app-fundamentals/pcl.md), несмотря на то, что эта технология будет заменен .NET Standard.

## <a name="supported-assemblies"></a>Поддерживаемые сборки

Это сборки, доступных в **диспетчер ссылок > сборки > Framework** (Visual Studio 2017) и **изменить ссылки > пакеты** (Visual Studio для Mac) и их совместимости с помощью платформы Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Совместимость API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Включает ККЯ, MidEast, другие, используется редко, Запад|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Поставщик ADO.NET для SQLite; см. в разделе ограничения.|✓|✓|✓|
> |Mono.Data.Tds.dll|Поддержка протокола потока табличных данных; используется для [System.Data.SqlClient](xref:System.Data.SqlClient) поддерживают в [System.Data](xref:System.Data).|✓|✓|✓|
> |Mono.Dynamic. &#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|Криптографические API.|✓|✓|✓|
> |monotouch.dll|Эта сборка содержит привязку к платформе CocoaTouch API C#. Эта возможность доступна только в проекты iOS Classic.|✓| | |
> |MonoTouch. &#8203;1.dll диалоговое окно| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|OpenGL/OpenAL объектно ориентированного API, расширенная с целью предоставления поддержки устройства iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), а также типы из следующих пространств имен:<br />System.Collections.Specialized<br />Система. &#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net. &#8203;NetworkInformation, соответствующей выбранному<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime. &#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security. &#8203;AccessControl<br />System.Security.Authentication<br />System.Security. &#8203;Криптографии<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |Система. &#8203;ComponentModel. &#8203;Composition.dll| |✓|✓|✓|
> |Система. &#8203;ComponentModel. &#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx) , с помощью [удалены некоторые функциональные возможности](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data. &#8203;Служб. &#8203;Client.dll|Клиент полный oData.|✓|✓|✓|
> |System.IO. &#8203;Сжатия| |✓|✓|✓|
> |System.IO. &#8203;Сжатия. &#8203;Файловой системы| |✓|✓|✓|
> |System.Json.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net. &#8203;Http.dll| |✓|✓|✓|
> |Система. &#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime. &#8203;Serialization.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |Система. &#8203;ServiceModel.dll|Стек WCF, заданное на [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |Система. &#8203;ServiceModel. &#8203;Internals.dll| |✓|✓|✓|
> |Система. &#8203;ServiceModel. &#8203;Web.dll|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), а также типы из следующих пространств имен: <br />Система<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |Система. &#8203;Transactions.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); частью [System.Data](~/ios/data-cloud/system.data.md) поддержки.|✓|✓|✓|
> |System.Web. &#8203;Services.dll|Основные веб-службы из профиля .NET 3.5, с сервера компоненты, удаленные.|✓|✓|✓|
> |Система. &#8203;Windows.dll| |✓|✓|✓|
> |Система. &#8203;Xml.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml. &#8203;Linq.dll|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Эта сборка содержит привязку к платформе CocoaTouch API C#. Используется только в единой системе проектов iOS.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android. &#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |Система. &#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android. &#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices. &#8203;SymbolWriter.dll|Для компиляторов.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |Система. &#8203;Drawing.dll|System.Drawing не поддерживается на единый API Xamarin.Mac, .NET 4.5 или мобильных платформ. Можно добавить поддержку System.Drawing iOS и macOS с помощью [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) библиотеки|✓| |✓|
