---
title: Android луч.
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: 89e668b8936db9a05fca2353b334b630b8363a74
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762763"
---
# <a name="android-beam"></a>Android луч.

Android луч — это технология рядом с полем связи действия (NFC), представленные в Android 4.0, который позволяет приложениям обмениваться информацией по NFC в непосредственной близости.

[![Диаграмма, иллюстрирующая два устройства в непосредственной близости от общего доступа к данным](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android луч работает с помощью принудительной отправки сообщений через NFC, когда два устройства находятся в диапазоне. Устройства около 4cm друг от друга могут совместно использовать данные с помощью Android луч. Действия на одном устройстве, создает сообщение и указывает действие (или действия), можно обработать, опубликуйте его. Если указанное действие находится на переднем плане, устройства, в диапазоне Android луч будет отправлять сообщения на втором устройстве. На принимающем устройстве целью вызывается с данными сообщения.

Android поддерживает два способа настройки сообщений с Android луч.

-   `SetNdefPushMessage` -Перед Android луч инициируется, приложение может вызвать SetNdefPushMessage, чтобы указать NdefMessage для принудительной отправки по NFC, а также действия, которое внедряет его. Этот механизм лучше всего использовать, если сообщение не изменяется во время приложения.

-   `SetNdefPushMessageCallback` -Если инициируется луч Android, приложение может обрабатывать обратный вызов для создания NdefMessage. Этот механизм позволяет создавать сообщения, пока они не устройства находятся в диапазоне. Он поддерживает сценарии, где сообщение может меняться в зависимости от выполняемых в приложении.


В любом случае для отправки данных с Android луч приложение передает `NdefMessage`, упаковка данных в нескольких `NdefRecords`. Давайте рассмотрим ключевые моменты, которые необходимо решать, прежде чем мы может активировать Android луч. Во-первых, мы будем работать с создания стиль обратного вызова `NdefMessage`.


## <a name="creating-a-message"></a>Создание сообщения

Можно зарегистрировать обратные вызовы с `NfcAdapter` в действии `OnCreate` метод. Например, при условии, что `NfcAdapter` с именем `mNfcAdapter` объявлена как переменная класса в действии, можно написать следующий код, чтобы создать обратный вызов, который формируется сообщение:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

Действия, которое реализует `NfcAdapter.ICreateNdefMessageCallback`, передается `SetNdefPushMessageCallback` описанный выше метод. Если Android луч инициируется, система будет вызывать `CreateNdefMessage`, из которого можно сконструировать действия `NdefMessage` как показано ниже:

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

На принимающей стороне, система вызывает с целью `ActionNdefDiscovered` действия, из которого можно извлечь NdefMessage следующим образом:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Полный пример кода, использующего Android луч, показано выполнение на снимке экрана ниже, в разделе [Android луч Демонстрация](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) в коллекции примеров.

[![Снимки экрана пример из образца луч Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация Android луч (пример)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
