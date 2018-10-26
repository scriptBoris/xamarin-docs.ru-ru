---
title: Устранение неполадок tvOS 10 приложениями созданных с помощью Xamarin
description: В этой статье предоставляет некоторые советы по устранению неполадок для работы с tvOS 10 в приложениях Xamarin. Он описывает проблемы, связанные с App Store, совместимость двоичных файлов, CFNetwork HttpProtocol, CloudKit, образ основных компонентов, NSUserActivity и UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3815790cfb73f93f399c14d3da44aa3210725388
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120003"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Устранение неполадок tvOS 10 приложениями созданных с помощью Xamarin

В следующих разделах перечислены некоторые известные проблемы, которые могут возникнуть при использовании tvOS 10 с помощью Xamarin и решение этих проблем:

- [Магазин приложений](#App-Store)
- [Совместимость двоичных файлов](#Binary-Compatibility)
- [CFNetwork протокола HTTP](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Образ основных компонентов](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Известные проблемы:

 - При тестировании покупки из приложений в изолированной среде, может появиться диалоговое окно проверки подлинности дважды.
 - При тестировании покупки из приложений с помощью размещенное содержимое в изолированной среде, появится диалоговое окно пароля каждый раз, приложение будет выведен на передний план, пока не завершит загрузку содержимого.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Совместимость двоичных файлов

Известные проблемы:

 - Вызов `NSObject.ValueForKey` будет `null` ключа приведет к возникновению исключения.
 - Ссылка на шрифт по имени при вызове `UIFont.WithName` приведет к сбою.
 - Оба `NSURLSession` и NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http:// "URL-адреса.
 - Приложения может зависнуть, если они изменяют суперпредставления geometry в любом `ViewWillLayoutSubviews` или `LayoutSubviews` методы.
 - Для всех подключений SSL/TLS симметричного шифрования RC4 теперь отключено по умолчанию. Кроме того Защита транспорта API больше не поддерживает SSLv3, и рекомендуется, что приложение остановить использование шифрования SHA-1 и 3DES как можно скорее.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork протокола HTTP

`HTTPBodyStream` Свойство `NSMutableURLRequest` класса должно быть присвоено поток неоткрытый с момента `NSURLConnection` и `NSURLSession` теперь строго соблюсти это требование.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Долго выполняющиеся операции, будет возвращать _«У вас нет разрешения для сохранения файла.»_ Ошибка.

<a name="CoreImage" />

## <a name="core-image"></a>Образ основных компонентов

`CIImageProcessor` API теперь поддерживает счетчиком произвольные входные изображения. `CIImageProcessor` Удаляется API, который был включен в tvOS 10 бета-версии 1.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

После выполнения операции передачи `UserInfo` свойство `NSUserActivity` объект может быть пустым. Явным образом вызвать `BecomeCurrent` NSUserActivity "объект в качестве текущего решения.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Известные проблемы:

 - Изменения в фоновом режиме внешний вид `UINavigationBar`, `UITabBar` или `UIToolBar` может привести к передаче макета для разрешения на новый внешний. Попытка изменить эти вхождений внутри `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` или `DidUpdateSubviews` события может привести к циклу бесконечный макета.
 - В tvOS 10, вызвав `RemoveGestureRecognizer` метод `UIView` объекта явно отменяет любые выполняющиеся распознаватель жестов.
 - Представленные контроллеров представлений может повлиять на внешний вид строки состояния.
 - tvOS 10 требует, чтобы разработчик для вызова `base.AwakeFromNib` для подклассов `UIViewController` и переопределение `AwakeFromNib` метод.
 - Приложения с пользовательским `UIView` подклассов, которые переопределяют `LayoutSubviews` и "грязных" макет перед вызовом `base.LayoutSubviews` может активировать в макет бесконечный цикл в tvOS 10.
 - Направление конкретных или flippable изображений ресурсы — это зеркального отражения при назначении `UIButton` объектов.

## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Новые возможности в tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
