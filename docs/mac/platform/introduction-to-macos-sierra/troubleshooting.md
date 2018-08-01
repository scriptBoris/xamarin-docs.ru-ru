---
title: Xamarin.Mac - macOS Сьерра Устранение неполадок
description: Этот документ содержит несколько советов по устранению неполадок для работы с macOS Сьерра в Xamarin.Mac приложениях. Советы относятся к Mac App Store, Apple Pay, совместимости с двоичными данными, CFNetwork, CloudKit и многое другое.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 5b2571d9562fd137257e2dd0ea2ada8f071bab92
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792333"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS Сьерра Устранение неполадок

_В этой статье предоставляет несколько советов по устранению неполадок для работы с macOS Сьерра в Xamarin.Mac приложениях._

В следующих разделах перечислены некоторые известные проблемы, которые могут возникнуть при использовании macOS Сьерра с Xamarin.mac и решения этих проблем:

- [Магазин приложений](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Двоичная совместимость](#Binary-Compatibility)
- [Протокол HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Образ Core](#CoreImage)
- [Уведомления](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>Магазин приложений

Известные проблемы:

- При тестировании покупки из приложений в среде "песочницы", диалоговое окно проверки подлинности, появляются дважды.
- При тестировании покупки из приложений с содержимым, размещенной в среде "песочницы", диалоговое окно пароля будет отображаться каждый раз, чтобы приложение переводится на передний план, до завершения загрузки содержимого.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Если введено неверное истечение срока действия даты или безопасности кода (CW) при добавлении новой карты платежа Apple Pay, карты, процесс подготовки будет прерван.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Двоичная совместимость

Известные проблемы:

- Вызов `NSObject.ValueForKey` будет `null` ключа приведет к возникновению исключения.
- Оба `NSURLSession` и NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' URL-адреса.
- Приложения может зависнуть, если они изменяют Обзор от geometry в любом `ViewWillLayoutSubviews` или `LayoutSubviews` методы.
- Для всех подключений SSL/TLS симметричного шифрования RC4 теперь отключено по умолчанию. Кроме того API защиты транспорта больше не поддерживает SSLv3 и рекомендуется, приложение остановить использование шифрования SHA-1 и 3DES как можно быстрее.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Протокол HTTP CFNetwork

`HTTPBodyStream` Свойство `NSMutableURLRequest` класса должно быть присвоено поток Неоткрытое с момента `NSURLConnection` и `NSURLSession` теперь строго соблюсти это требование.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Длительные операции вернет _«Нет разрешения на сохранение файла.»_ Произошла ошибка.

<a name="CoreImage" />

## <a name="core-image"></a>Образ Core

`CIImageProcessor` API теперь поддерживает числа произвольных входного образа. `CIImageProcessor` API, который был включен в macOS Сьерра бета-версия 1 будут удалены.

<a name="Notifications" />

## <a name="notifications"></a>Уведомления

При работе с расширениями содержимого уведомлений, просмотр контроллеров не выходят в правильно и может привести к ошибкам при достижении ограничений памяти расширения.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

После выполнения операции передачи `UserInfo` свойство `NSUserActivity` объект может быть пустым. Явным образом вызвать `BecomeCurrent` `NSUserActivity` объект в качестве текущего решения этой проблемы.

<a name="Safari" />

## <a name="safari"></a>Safari

Требуется безопасный WebGeolocation (`https://`) URL-адрес для работы с iOS 10 и macOS Сьерра, чтобы предотвратить злонамеренное использование данных расположения.







## <a name="related-links"></a>Связанные ссылки

- [Примеры Mac](https://developer.xamarin.com/samples/mac/)
- [Новые возможности OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
