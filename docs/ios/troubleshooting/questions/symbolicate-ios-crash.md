---
title: Где найти файл .dSYM symbolicate журналы аварийного завершения операций ввода-вывода
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920661"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Где найти файл .dSYM symbolicate журналы аварийного завершения операций ввода-вывода

При построении приложения iOS с помощью Visual Studio для Mac или Visual Studio 2017 г., файл .dSYM для symbolicate отчеты о сбоях будут располагаться в той же иерархии каталогов, как файл проекта приложения (CSPROJ-файл). Точное расположение зависит от параметров построения проекта.

- При наличии сборок конкретного устройства .dSYM можно найти в следующем каталоге:

    **&lt;каталог проекта&gt;/bin/&lt;платформы&gt;/&lt;конфигурации&gt;/device-builds /&lt;устройства&gt; - &lt; версия ОС&gt;/**

    Пример:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Если не включена сборок конкретного устройства, .dSYM можно найти в следующем каталоге:

    **&lt;каталог проекта&gt;/bin/&lt;платформы&gt;/&lt;конфигурации&gt;/**

    Пример:

    **TestApp/bin/iPhone/Release /**

> [!NOTE]
> В рамках процесса построения 2017 г. Visual Studio копирует файл .dSYM между узлом построения Mac и Windows. Если отсутствует файл .dSYM в Windows, убедитесь, что вы настроили параметры сборки приложения, чтобы [создать IPA-файл](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>См. также

- [Symbolicating операций ввода-вывода файлов приводит к сбою (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Функции приводит к сбою журналах приложения iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

