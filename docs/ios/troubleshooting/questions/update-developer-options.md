---
title: Как включить параметры разработчика после обновления iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 9eb25676cc4a3a0679d6a02fc64fef5632f76e33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30775488"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Как включить параметры разработчика после обновления iOS?

Ошибка в операций ввода-вывода может привести к параметров разработчика исчезает после обновления версии iOS, это было обнаружено при переключении на iOS 8.x. Эти параметры можно повторно включать с помощью следующих действий:

1. На компьютере Mac подключите устройство iOS.
2. Запустите Xcode.
3. Откройте **окна > устройства** & Выберите устройство.
4. Дождитесь Xcode завершения копирования символов и проверьте Settings.app устройства для параметров разработчика.
5. Перезагрузите устройство.
