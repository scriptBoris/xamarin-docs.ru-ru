---
title: Проверка приложения в Xamarin.iOS для запросов
description: В этой статье описывается метод RequestReview, Apple, добавляемый в iOS 10 и обсуждаются способы ее применения в Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109787"
---
# <a name="request-app-review-in-xamarinios"></a>Проверка приложения в Xamarin.iOS для запросов

_В этой статье рассматривается метод RequestReview, Apple iOS 10 и его применение в Xamarin.iOS._

Знакомы с iOS 10.3, `RequestReview()` метод позволяет запрашивать у пользователя, чтобы оценить и просматривать приложения iOS. Когда этот метод вызывается в приложении доставки, которое пользователь установил из App Store, iOS 10 будет обрабатывать весь оценка и процесс анализа для разработчиков. Так как этот процесс управляется политики App Store, оповещение может или не отображается.

![](request-app-review-images/review01.png "Пример оповещения, запрос на проверку")

## <a name="requesting-a-rating-or-review"></a>Запрос оценку или отзыв

Хотя `RequestReview()` статический метод `SKStoreReviewController` могут вызываться в любой момент, где имеет смысл во взаимодействие с пользователем, процесс проверки регулируемого и обрабатываются политики App Store. Таким образом этот метод может или не может отображать оповещение и никогда не должен вызываться в ответ на действия пользователя, например при нажатии кнопки.

Например приложение может запросить проверку после ее запуска заданное количество раз или игра может запросить проверку после игрок завершает уровнем.

На запросы проверки, как только завершится приложения Xamarin.iOS запуск, внесите следующие изменения для `AppDelegate.cs` файла:

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }
        
        ...
        
    }
}
```

> [!NOTE]
> Вызов `RequestReview()` недостаточное число ресурсов разработки приложение всегда отображает оценку и просмотрите диалоговое окно, поэтому его можно проверить. Это не относится к приложениям, которые были распространены в TestFlight, где будет игнорироваться вызова метода.

Когда `RequestReview()` был вызван в приложении доставки, которое пользователь установил из App Store, iOS 10 будет обрабатывать весь процесс оценки и проверки для разработчиков. Опять же так как этот процесс управляется политики App Store, оповещение может или не отображается.

## <a name="linking-to-an-app-store-product-page"></a>Создание ссылок на страницу продукта App Store 

Дополнение к новым `RequestReview` метод, разработчик по-прежнему может предоставить прямая ссылка на страницу продукта приложения в App Store из в приложении. Путем добавления `action=write-review` в конец URL-адрес страницы продукта, страница будет открыт где пользователь может создать Обзор приложения автоматически. 

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается метод RequestReview, Apple iOS 10 и его применение в Xamarin.iOS.



## <a name="related-links"></a>Связанные ссылки

- [iOSTenThree образца](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
