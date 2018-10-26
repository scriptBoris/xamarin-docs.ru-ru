---
title: IPA-файла составляет 0 байт
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4835c980b6b11c92ec1c81dea69f229aa5652275
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102663"
---
# <a name="ipa-file-is-0-bytes"></a>IPA-файла составляет 0 байт

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.



Отсутствуют некоторые известные проблемы в предыдущих версиях Xamarin, которое может привести к IPA-файл на Windows 0 байт. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Исправлена в Xamarin для Visual Studio 3.11.584 
- [Ошибки 24416 - конфигурации «Ad-Hoc» построение из командной строки выполняет не копировать IPA-файл для Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Ошибки 24417 - изменение «свойства проекта iOS IPA, "->" Параметры "->" имя пакета» предотвращает IPA копируются обратно в Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Ошибки 29822 - номер «Сборки» [XVS.iOS 3.11] параметр, отличается от номера IPA причины «Версия» не для копирования для Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Исправлена в Xamarin для Visual Studio 4.1.0.496
- [Bug 27989 - Показать IPA-файл на происходит сбой сервера сборки в том случае, если имя сборки не соответствует имени проекта](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
