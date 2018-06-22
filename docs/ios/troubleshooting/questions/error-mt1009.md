---
title: 'Ошибка MT1009: Не удалось скопировать сборку'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: de1d7ea8ce4c358ad69150be3da6b38fb6c730ae
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776083"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Ошибка MT1009: Не удалось скопировать сборку

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin.iOS. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.

Как описано в нашем [заметки о выпуске](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), это известная проблема в Xamarin.iOS 7.2.6. Эта проблема вызвана разрешения файла требуется более высокий уровень разрешений при Xamarin.iOS устанавливается вместе с другой учетной записью выберите основной учетной записи разработчика.

Для устранения этой проблемы откройте Terminal.app на компьютере Mac и выполните следующую команду:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

