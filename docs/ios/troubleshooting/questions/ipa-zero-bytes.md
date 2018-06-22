---
title: Файл IPA составляет 0 байт
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 40799d0b8b051459145f51671ae7f6143db9635a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777789"
---
# <a name="ipa-file-is-0-bytes"></a>Файл IPA составляет 0 байт

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.



Произошли некоторые известные проблемы в предыдущих версиях Xamarin, после чего файл IPA в Windows как 0 байт. 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>Исправлены в Xamarin для Visual Studio 3.11.584 
- [Ошибка 24416 - конфигурации «Ad-Hoc» построение из командной строки не не копировать IPA-файл в Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Ошибки 24417 - изменение «свойства проекта "->" iOS IPA параметры "->" имя пакета» запрещает IPA будут скопированы Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Ошибка 29822 - номер «Сборки» [XVS.iOS 3.11] параметр, отличается от «Версия» номеров IPA причины не для копирования в Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>Исправлены в Xamarin для Visual Studio 4.1.0.496
- [Ошибки 27989 - вывести ipa-файл на происходит сбой сервера построения, если имя сборки не соответствует имени проекта](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
