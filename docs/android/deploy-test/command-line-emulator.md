---
title: Эмулятор командной строки
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 513b06749d1616e9fd10f04d22259810c0b4d265
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120727"
---
# <a name="command-line-emulator"></a>Эмулятор командной строки


## <a name="running-the-android-emulator-from-the-command-line"></a>Запуск эмулятора Android из командной строки

Для запуска эмулятора Android из командной строки можно использовать средство emulator, предоставляемое Android SDK. Это средство можно использовать для запуска эмулятора из терминала в OS X или из командной строки на компьютере Windows.

Для запуска определенного эмулятора Android выполните следующую команду из каталога средства в папке android SDK (например, C:\android-sdk-windows\tools):

В Windows

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

В macOS

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

Причина, по которой требуется настроить размер раздела, это предоставление эмулятору достаточно места для работы с платформой Xamarin.Android, установленной в эмуляторе, так как по умолчанию устанавливается эмулятор небольшого размера.

Подробные сведения о дополнительных параметрах см. на сайте по Android: [http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
