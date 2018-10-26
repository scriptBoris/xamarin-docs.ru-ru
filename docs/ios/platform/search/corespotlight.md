---
title: Поиск с помощью полезные сведения в Xamarin.iOS
description: В этом документе описывается, как использовать полезные сведения в приложении Xamarin.iOS для предоставления ссылок на содержимое в приложении. Он описывает создание, восстановление, обновления и удаления для поиска элементов.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110359"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Поиск с помощью полезные сведения в Xamarin.iOS

Полезные сведения — это новая платформа для iOS 9, которая представляет API базы данных, чтобы добавить, изменить или удалить ссылки на содержимое в приложении. Элементы, которые были добавлены с помощью полезные сведения будут доступны в поиске Spotlight на устройстве iOS.

Например, типы содержимого, который может быть проиндексирован с помощью полезные сведения просмотрите Apple сообщений, почта, календарь и заметки о приложений. Все они в настоящее время использовать полезные сведения для предоставления результатов поиска.

## <a name="creating-an-item"></a>Создание элемента

Ниже приведен пример создания элемента и осуществив его с помощью полезные сведения:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Эта информация будет выглядеть как показано ниже, в результате поиска:

[![](corespotlight-images/corespotlight01.png "Общие сведения о поиске Spotlight результат Core")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Восстановление элемента

Когда пользователь нажимает на элемент, добавленный к результатам поиска с помощью полезные сведения для вашего приложения `AppDelegate` метод `ContinueUserActivity` вызывается (этот метод также используется для `NSUserActivity`). Пример:

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Обратите внимание, что это время мы проверку наличия действия `ActivityType` из `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Обновление элемента

Возможны ситуации, когда элемент индекса, мы создали с помощью полезные сведения должны быть изменены, например, когда требуется изменение в заголовке или эскиз. Чтобы внести это изменение, мы используем тот же метод, которая была использована в первоначальном создании индекса.
Мы создаем новый `CSSearchableItem` использование одного идентификатора, которая была использована для создания элемента и присоединить новый `CSSearchableItemAttributeSet` содержащий измененных атрибутов:

[![](corespotlight-images/corespotlight02.png "Обновление элемента Обзор")](corespotlight-images/corespotlight02.png#lightbox)

При записи этого элемента для поиска индекса, существующий элемент обновляется новыми данными.

## <a name="deleting-an-item"></a>Удаление элемента

Полезные сведения предоставляет несколько способов для удаления индекса элемента, если он больше не требуется.

Во-первых можно удалить элемент по его идентификатору, например:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Затем можно удалить группу элементов индекса по доменному имени. Пример:

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Наконец вы можете удалить все элементы индекса со следующим кодом:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>Дополнительные основные функции Spotlight

Полезные сведения имеет следующие возможности, которые помогают обеспечить индекс, точной и свежей.

- **Пакетное обновление поддержки** — Если приложению требуется создать или изменить большое количество индексов в то же время, весь пакет могут отправляться `Index` метод `CSSearchableIndex` класс в одном вызове.
- **Реагирование на изменения индекса** — при использовании `CSSearchableIndexDelegate` приложения может реагировать на изменения и уведомления из индекса для поиска.
- **Применять защиту данных** — с помощью классов защиты данных, можно реализовать безопасность на элементы, добавляемые в поисковый индекс, с помощью полезные сведения.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Руководство по программированию приложения поиска](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
