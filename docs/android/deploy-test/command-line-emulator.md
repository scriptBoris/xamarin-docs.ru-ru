---
title: Эмулятор командной строки
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/05/2018
ms.openlocfilehash: 995b0783604f752915daaa77a8362899ac61e174
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983592"
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

Подробные сведения о дополнительных параметрах см. на сайте по Android: [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
