---
title: Android луч
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 13a0a0d9c6a9d1d5f49020b1a8096f5e054d415c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114929"
---
# <a name="android-beam"></a>Android луч

Android луч — это технология рядом с полем связи действия (NFC), представленные в Android 4.0, который позволяет приложениям обмениваться информацией по NFC, когда в непосредственной близости.

[![Схема, иллюстрирующая два устройства в непосредственной близости общего доступа к данным](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android луч работает с помощью принудительной отправки сообщений через NFC, когда два устройства находятся в диапазоне. Устройства, около 4cm друг от друга могут обмениваться данными, с помощью Android луч. Действие на одном устройстве, создает сообщение и действие (или действия), может обрабатывать принудительной отправкой. Если указанное действие находится на переднем плане и устройства находятся в диапазоне, Android луч будет отправлять сообщения на втором устройстве. Для получающим устройством объект Intent был вызван, содержащий данные сообщения.

Android поддерживает два типа Настройка сообщений с Android луч:

-   `SetNdefPushMessage` — Перед инициируется Android луч, приложение может вызвать SetNdefPushMessage для указания NdefMessage для принудительной отправки NFC и действие, которое принудительной отправкой. Этот механизм используется наилучшим образом в том случае, если сообщение не изменяется во время использования приложения.

-   `SetNdefPushMessageCallback` -Если инициируется Android луч, приложение может обрабатывать обратный вызов для создания NdefMessage. Этот механизм позволяет создавать сообщения, пока они не устройства находятся в диапазоне. Он поддерживает сценарии, где сообщение может отличаться в зависимости от того, что происходит в приложении.


В любом случае для отправки данных с помощью Android луч приложение отправляет `NdefMessage`, упаковка данных в нескольких `NdefRecords`. Давайте рассмотрим ключевые моменты, которые необходимо решить перед активируется Android луч. Во-первых, мы будем работать с стиль обратного вызова для создания `NdefMessage`.


## <a name="creating-a-message"></a>Создание сообщения

Зарегистрировать обратные вызовы с `NfcAdapter` в действии `OnCreate` метод. Например, при условии, что `NfcAdapter` с именем `mNfcAdapter` объявляется как переменную класса в действии, можно написать следующий код, чтобы создать обратный вызов, который создает сообщение:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

Действие, которое реализует `NfcAdapter.ICreateNdefMessageCallback`, передается `SetNdefPushMessageCallback` описанного выше метода. Если инициируется Android луч, система будет вызывать `CreateNdefMessage`, из которого можно создать действие `NdefMessage` как показано ниже:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```


## <a name="receiving-a-message"></a>Получение сообщения

На принимающей стороне, система вызывает объект Intent с `ActionNdefDiscovered` действия, из которого можно извлечь NdefMessage следующим образом:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Полный пример кода, использующего Android луч, показано выполнение на снимке экрана ниже, см. в разделе [Android луч Демонстрация](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) в коллекции примеров.

[![Снимки экрана из Android луч демонстрационной версии](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Связанные ссылки

- [Android луч Demo (пример)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
