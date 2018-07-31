---
title: Core NFC в Xamarin.iOS
description: В этом документе описывается, как прочитать практически теги связи поля в Xamarin.iOS с помощью интерфейсов API, представленная в iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: 1381a4564f93fd091f181949454df3f06b31ae6b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350837"
---
# <a name="core-nfc-in-xamarinios"></a>Core NFC в Xamarin.iOS

_Теги чтения рядом с полем связи действия (NFC), с помощью iOS 11_

CoreNFC — это новая платформа, в iOS 11, который предоставляет доступ к _радиочастотной связи_ radio (действия NFC) для чтения тегов из приложения. Он работает на устройстве iPhone 7, плюс 7, 8, 8, а также X.

Чтения тегов NFC на устройствах iOS поддерживает все типы тегов NFC 1 – 5, которые содержат _формат обмена данными NFC_ сведения (NDEF).

Существуют некоторые ограничения, которые следует учитывать:

- CoreNFC поддерживает только операции чтения (не для записи или форматирование) тега.
- Сканирование тега должно быть инициированного пользователем и время ожидания после 60 секунд.
- Приложения должны быть видны на переднем плане для сканирования.
- CoreNFC можно протестировать только на реальных устройствах (не в симуляторе).

На этой странице описывается конфигурация, необходимая для использования CoreNFC и показано, как использовать API с помощью [«TFCTagReader» пример кода](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/).

## <a name="configuration"></a>Конфигурация

Чтобы включить CoreNFC, необходимо настроить три элемента в проекте:

- **Info.plist** ключ конфиденциальности.
- **Entitlements.plist** запись.
- Профиль подготовки с **чтение тегов NFC** возможностей.

### <a name="infoplist"></a>Info.plist

Добавить **NFCReaderUsageDescription** ключ конфиденциальности и текст, который отображается для пользователя во время сканирования. Использовать сообщение, подходящее для вашего приложения (например, объяснения назначения сканирование):

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

Приложение должно запросить **практически чтение тегов поле Communications** возможность с помощью следующих ключ/значение пары из вашей **Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Профиль подготовки

Создайте новый **идентификатор приложения** и убедитесь, что **чтение тегов NFC** службы установлен:

[![Страница нового идентификатора приложения портала разработчика с выбранного чтение тегов NFC](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Следует затем создания профиля подготовки для этого идентификатора приложения, а затем скачайте и установите его на разработки Mac.

## <a name="reading-a-tag"></a>Считывания тега

После того как вы настроили свой проект, добавить `using CoreNFC;` в верхнюю часть файла и выполните следующие три действия, чтобы реализовать NFC тег функциональные возможности чтения:

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. Реализуйте `INFCNdefReaderSessionDelegate`

Интерфейс имеет два метода для реализации:

- `DidDetect` — Вызывается при успешном считывании тег.
- `DidInvalidate` — Вызывается при возникновении ошибки или при достижении время ожидания 60 секунд.

#### <a name="diddetect"></a>DidDetect

В образце кода каждый сообщение добавляется в табличное представление:

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Этот метод может вызываться несколько раз (и массив сообщений может передаваться в) Если сеанс позволяет несколько операций чтения тег. Это задается с помощью третьего параметра `Start` метод (как описано в [шаг 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Недействительность может произойти по ряду причин.

- Произошла ошибка при проверке.
- Приложение перестало быть на переднем плане.
- Пользователь решает отменить сканирование.
- Сканирование было отменено по запросу приложения.

В приведенном ниже коде показано, как обработать ошибку:

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Когда сеанс более не действителен, нового объекта сеанса должны создаваться для повторной проверки.

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2. Запуск `NFCNdefReaderSession`

Сканирование должно начинаться с запросом пользователя, например нажатие кнопки.
Следующий код создает и запускает сеанс сканирования:

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Параметры для `NFCNdefReaderSession` конструктор таковы:

- `delegate` — Реализация `INFCNdefReaderSessionDelegate`. В образце кода делегата реализуется в контроллер представления таблиц, поэтому `this` используется в качестве аргумента делегата.
- `queue` Очередь, обратные вызовы, обрабатываются на. Это может быть `null`, в этом случае следует использовать `DispatchQueue.MainQueue` при обновлении элементов управления пользовательского интерфейса (как показано в примере).
- `invalidateAfterFirstRead` — Когда `true`, сканирование останавливается после первого успешного сканирования; при `false` сканирования будут по-прежнему и возвращены несколько результатов, пока сканирование отменяется или при достижении время ожидания 60 секунд.


### <a name="3-cancel-the-scanning-session"></a>3. Отмена сканирования сеанса

Пользователь может отменить сканирования сеанса с помощью предоставляемой системой кнопки в пользовательском интерфейсе:

![Кнопка "Отмена" при сканировании](corenfc-images/scan-cancel-sml.png)

Приложение программным способом можно отменить проверку, вызвав `InvalidateSession` метод:

```csharp
Session.InvalidateSession();
```

В обоих случаях делегата `DidInvalidate` метод будет вызываться.

## <a name="summary"></a>Сводка

CoreNFC позволяет приложению считывать данные из NFC-меток. Он поддерживает чтение разнообразных форматов тега (типы NDEF 1 – 5), но не поддерживает запись или форматирования.


## <a name="related-links"></a>Связанные ссылки

- [NFCTagReader (пример)](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [Введение в основные NFC (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/718/)
