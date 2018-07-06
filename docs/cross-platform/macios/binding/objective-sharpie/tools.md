---
title: Цели Sharpie средства и команды
description: Этот документ предоставляет обзор средств, включенных с Sharpie цели и аргументы командной строки для использования с ними.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 718b5104ddc4593d080b88b062c42d371d9e8e2e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855072"
---
# <a name="objective-sharpie-tools--commands"></a>Цели Sharpie средства и команды

_Обзор средств, включенных с Sharpie цели и аргументы командной строки для их использования._

<style type="text/css"> .Terminal синий {color: rgb(10,96,254);} .terminal зеленый {color: rgb(12,156,26);} .terminal пурпурный {цвет: rgb(152,12,103);} </style>


После успешной цели Sharpie [установлен](~/cross-platform/macios/binding/objective-sharpie/get-started.md), откройте окно терминала и ознакомиться с <em>команды</em> Sharpie цель может предложить:

<pre>$ <b>sharpie -help</b>
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation</pre>

Цели Sharpie предоставляет следующие средства:

|Средство|Описание:|
|--- |--- |
|**xcode**|Сведения о текущей установке Xcode и версиях iOS и Mac пакеты SDK, которые доступны. Мы будем использовать в дальнейшем при создании наших привязки.|
|**POD**|Выполняет поиск, настраивает, устанавливает (в локальном каталоге) и привязывает Objective-C [cocoapod для службы](https://cocoapods.org/) библиотек, доступных из главного репозитория спецификаций. Этот инструмент оценивает установленных cocoapod для службы автоматически определить правильные входные данные для передачи `bind` ниже средства. Новые возможности 3.0!|
|**bind**|Анализирует файлы заголовков (`*.h`) в библиотеке Objective-C в исходный [ApiDefinition.cs и StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) файлов.|
|**update**|Проверяет наличие более новых версиях Sharpie цели и загружает и запускает программу установки, если таковой доступен.|
|**проверить документы**|Отображаются подробные сведения о `[Verify]` атрибуты.|
|**Документы**|Переход к этому документу в веб-браузере по умолчанию.|

Чтобы получить справку по определенное средство Sharpie цели, введите имя средства и `-help` параметр. Например `sharpie xcode -help` возвращает следующие выходные данные:

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

Прежде чем приступать процесса привязки, нам нужно получить сведения о нашей текущей установленных SDK, введя следующую команду в окне терминала `sharpie xcode -sdks`. Выходные данные могут различаться в зависимости от версий Xcode установки. Цели Sharpie ищет пакеты SDK, устанавливать его в любом `Xcode*.app` под `/Applications` каталог:

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

Приведенные выше, мы видим, что у нас есть `iphoneos9.1` пакет SDK установлен на компьютере, и он имеет `arm64` поддержка архитектуры. Мы будем использовать это значение для всех примеров в этом разделе. Располагая этой информацией, мы готовы выполнить синтаксический анализ файлов заголовка библиотеки Objective-C в исходный `ApiDefinition.cs` и `StructsAndEnums.cs` для привязки проекта.

## <a name="related-links"></a>Связанные ссылки

- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)