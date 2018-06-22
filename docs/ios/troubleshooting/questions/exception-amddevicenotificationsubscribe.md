---
title: Возвращаемый System.Exception AMDeviceNotificationSubscribe...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 257e0c14de3c23825b6abe6601c25438db81c58e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776486"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>Возвращаемый System.Exception AMDeviceNotificationSubscribe...

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, проверьте файл [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) с помощью полное управление версиями сведения и полный создать выходные данные журнала.


## <a name="fix"></a>Исправление

1.  KILL `usbmuxd` обработки, чтобы при перезагрузке системы:

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

Это сообщение может появиться в окно сообщения об ошибке при первом запуске Visual Studio для Mac или в `mtbserver.log` файл в приложении узла построения Xamarin.iOS (**узла построения Xamarin.iOS > журнала узла построить представление**).

Обратите внимание, что возникла проблема редко. Если Visual Studio удается подключиться к узлу построения Mac, есть другие ошибки, которые, скорее всего, появятся в `mtbserver.log` файла.

### <a name="errors-in-systemlog"></a>Ошибки в вышеперечисленные

В следующих двух случаях ошибка сообщений может также появиться несколько раз в `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Дополнительные сведения

Предположение первопричину ошибки, один том, что в OS X, системных служб, ответственный за reporting сведения об устройстве и симулятора iOS можно в редких случаях вводить непредвиденном состоянии. Xamarin не может правильно взаимодействовать с системные службы в этом состоянии. Перезагрузка компьютера перезапускает службы системы и позволяет решить проблему.

В зависимости от ошибки из `system.log` впечатление, что эта проблема может быть связана с Bonjour (`mDNSResponder`). Смена различные сети Wi-Fi кажется повысить вероятность попадания в проблему.

## <a name="references"></a>Ссылки

*   [Ошибка 11789 - MonoTouch.MobileDevice.MobileDeviceException: Возвращается AMDeviceNotificationSubscribe: 0xe8000063 [NORESPONSE разрешить]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
