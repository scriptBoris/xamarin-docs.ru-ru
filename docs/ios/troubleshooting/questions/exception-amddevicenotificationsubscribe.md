---
title: System.Exception AMDeviceNotificationSubscribe вернул...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119258"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe вернул...

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.


## <a name="fix"></a>Исправление

1.  KILL `usbmuxd` обработки, чтобы его перезагрузке системы:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  Если это не устранит проблему, перезагрузите Mac.

## <a name="error-message"></a>Сообщение об ошибке

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Это сообщение может появиться в окно сообщения об ошибке при первом запуске Visual Studio для Mac или в `mtbserver.log` файл в приложение Xamarin.iOS Build Host (**Xamarin.iOS Build Host > View Build Log узла**).

Обратите внимание, что это ошибка редко. Если Visual Studio не удается подключиться к узлу сборки Mac, существуют другие ошибки, которые чаще всего, появятся в `mtbserver.log` файл.

### <a name="errors-in-systemlog"></a>Ошибки в system.log

В некоторых случаях следующие две ошибки сообщения может также появиться несколько раз в `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Дополнительные сведения

Предположение первопричину ошибки, один том, что в OS X, системные службы ответственность для передачи сведений о устройств и симуляторов iOS можно в редких случаях вводить непредвиденном состоянии. Xamarin не может правильно взаимодействовать с системных служб в этом состоянии. Перезагрузка компьютера перезапускает системных служб и устранены.

В зависимости от ошибки, возникшие при `system.log` оказывается, что эта проблема может быть связана с Bonjour (`mDNSResponder`). Смена различные сети Wi-Fi кажется повышают вероятность попадания в проблему.

## <a name="references"></a>Ссылки

*   [Ошибка 11789 - MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe вернул: 0xe8000063 [разрешить NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
