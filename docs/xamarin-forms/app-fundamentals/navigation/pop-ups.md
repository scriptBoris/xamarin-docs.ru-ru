---
title: Отображение всплывающих окон
description: Xamarin.Forms предоставляет два pop повышение подобные элементы пользовательского интерфейса — предупреждение и листом действия. В этой статье демонстрируется использование предупреждения и действие листом API-интерфейсы простые вопросы пользователей и проводят пользователя через задачи.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 97f0917e4e8670ab379aae1b2707ae08cb29bb70
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2018
ms.locfileid: "32018883"
---
# <a name="displaying-pop-ups"></a>Отображение всплывающих окон

_Xamarin.Forms предоставляет два pop повышение подобные элементы пользовательского интерфейса — предупреждение и листом действия. В этой статье демонстрируется использование предупреждения и действие листом API-интерфейсы простые вопросы пользователей и проводят пользователя через задачи._

Отображение предупреждения или попросить пользователей принять решение — это обычная задача пользовательского интерфейса. Xamarin.Forms имеет два метода [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) классом для взаимодействия с пользователем через всплывающее окно: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) и [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/). Они отображаются соответствующие собственных элементов управления для каждой платформы.

## <a name="displaying-an-alert"></a>Отображение оповещения

Все платформы, поддерживаемые Xamarin.Forms имеют модальное всплывающее окно, чтобы предупредить пользователя или задать вопросы простой из них. Чтобы отобразить эти оповещения в Xamarin.Forms, используйте [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) метод для какого-либо [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Следующий код показывает простое сообщение пользователю:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Предупреждения диалоговое окно с одной кнопки")

В этом примере не собирать сведения от пользователя. Предупреждение отображается как модальная и после закрытия пользователь продолжает взаимодействовать с приложением.

[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) Метод может также использоваться для записи ответа пользователя путем предоставления двух кнопок и возвращения `boolean`. Чтобы получить ответ из оповещения, введите текст для обеих кнопок и `await` метод. Когда пользователь выделяет один из параметров ответ будет возвращаться в код. Примечание `async` и `await` ключевые слова в коде, приведенном ниже:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "предупреждения диалоговое окно с кнопками")](pop-ups-images/alert2.png#lightbox "диалоговое окно с кнопками на предупреждения")

## <a name="guiding-users-through-tasks"></a>Руководство пользователя через задачи

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) имеет общий элемент пользовательского интерфейса в iOS. Xamarin.Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) метод позволяет включать этот элемент управления в разных платформ приложений, подготовки к просмотру собственного представленным в Android и UWP.

Чтобы открыть окно действие `await` [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) в любом [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), передача сообщения и кнопку меток в виде строки. Метод возвращает строку метки кнопки, которую щелкнул пользователь. Простой пример показан ниже:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Диалоговое окно ActionSheet")

`destroy` Кнопки обрабатывается иначе, чем другие, а также можно оставить `null` или указан в качестве третьего параметра строки. В следующем примере используется `destroy` кнопки:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "диалоговое окно листа действий с кнопкой Destroy")](pop-ups-images/action2.png#lightbox "диалоговое окно листа действий с кнопкой Destroy")

## <a name="summary"></a>Сводка

В этой статье показано, с помощью предупреждения и действие листом API-интерфейсы простые вопросы пользователей и проводят пользователя через задачи. Xamarin.Forms имеет два метода [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) классом для взаимодействия с пользователем через всплывающее окно: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) и [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/), и они являются Оба заполняются соответствующие собственных элементов управления для каждой платформы.



## <a name="related-links"></a>Связанные ссылки

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
