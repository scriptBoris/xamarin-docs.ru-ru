---
title: Где найти dsym-файл для символьного выражения журналов о сбоях iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114071"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Где найти dsym-файл для символьного выражения журналов о сбоях iOS?

При создании приложения iOS с помощью Visual Studio для Mac или Visual Studio 2017, dsym-файл, необходимый для символьного выражения отчетов о сбоях будут размещены в той же иерархии каталогов, как файл проекта приложения (CSPROJ-файл). Точное расположение зависит от параметров сборки проекта.

- Если вы включили сборки для конкретных устройств, .dSYM можно найти в следующем каталоге:

    **&lt;каталог проекта&gt;/bin/&lt;платформы&gt;/&lt;конфигурации&gt;/device-builds /&lt;устройства&gt; - &lt; версия ОС&gt;/**

    Пример:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Если вы не включили сборки для конкретных устройств, .dSYM можно найти в следующем каталоге:

    **&lt;каталог проекта&gt;/bin/&lt;платформы&gt;/&lt;конфигурации&gt;/**

    Пример:

    **TestApp/bin/iPhone/Release /**

> [!NOTE]
> В рамках процесса сборки Visual Studio 2017 копирует dsym-файл, между узлом сборки Mac и Windows. Если вы не видите файла .dSYM на Windows, убедитесь, что вы настроили параметры сборки вашего приложения, чтобы [создать IPA-файл](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>См. также

- [Symbolicating iOS сбоев файлов (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Мифы о журналов о сбоях приложений iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

