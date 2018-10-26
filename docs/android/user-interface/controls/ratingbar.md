---
title: RatingBar
description: Как добавить мини-приложение, RatingBar действие Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131859"
---
# <a name="ratingbar"></a>RatingBar

RatingBar является графического элемента пользовательского интерфейса, который отображает оценку от одной до пяти звездочек. Пользователь может выбрать оценку, касаясь знаков на звездочку в этом разделе, вы создадите мини-приложение, пользователь может предоставить оценку, [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) мини-приложения.

![Пример RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Создание RatingBar

1. Откройте **Resource/layout/Main.axml** файл и добавьте [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   элемент (внутри [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   `android:numStars` Атрибут определяет, сколько звезды для отображения панели рейтинг. `android:stepSize` Атрибут определяет степень детализации для каждого типа "звезда" (например, значение `0.5` позволит половину оценки).

2. Чтобы сделать что-то новую оценку задана, добавьте следующий код в конец [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   метод:

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Здесь фиксируется [ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/) мини-приложение из макета с [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/) и затем задает метод событий, то определяет действие, выполняемое, когда пользователь устанавливает оценку. В данном случае простой [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) новый элемент управления rating отображает сообщение.

3.  Запустите приложение.

