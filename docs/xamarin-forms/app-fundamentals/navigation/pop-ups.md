---
title: Отображение всплывающих элементов
description: Xamarin.Forms предоставляет два во всплывающем регистрации подобные элементы пользовательского интерфейса — оповещение и листом действие. В этой статье демонстрируется использование окно оповещения и действия API-интерфейсы на простые вопросы пользователей и помогает пользователям сделать задачи.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996718"
---
# <a name="displaying-pop-ups"></a>Отображение всплывающих элементов

_Xamarin.Forms предоставляет два во всплывающем регистрации подобные элементы пользовательского интерфейса — оповещение и листом действие. В этой статье демонстрируется использование окно оповещения и действия API-интерфейсы на простые вопросы пользователей и помогает пользователям сделать задачи._

Отображение оповещения или где пользователю предлагается сделать выбор — это распространенная задача пользовательского интерфейса. Xamarin.Forms имеет два метода [ `Page` ](xref:Xamarin.Forms.Page) классом для взаимодействия с пользователем через всплывающее окно: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) и [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*). Они отображаются с помощью соответствующих собственных элементов управления на каждой платформе.

## <a name="displaying-an-alert"></a>Отображение оповещения

Все поддерживаемые Xamarin.Forms платформы имеют модальное всплывающее окно предупреждения пользователя и простые вопросы из них. Чтобы отобразить эти оповещения в Xamarin.Forms, используйте [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) метод на любом [ `Page` ](xref:Xamarin.Forms.Page). Следующий код показывает простое сообщение пользователю:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Диалоговое окно оповещения с помощью одной кнопки")

В этом примере не собирает данные от пользователя. Отображает предупреждения в модальном режиме и после закрытия пользователя продолжается, взаимодействующего с приложением.

[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) Метод может также использоваться для записи ответа пользователя путем представления две кнопки и возвращения сведений `boolean`. Чтобы получить ответ из оповещения, введите текст для обеих кнопок и `await` метод. После того как пользователь выберет один из вариантов ответ возвращается в код. Примечание `async` и `await` ключевые слова в образце кода ниже:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "предупреждения диалоговое окно с двумя кнопками")](pop-ups-images/alert2.png#lightbox "предупреждения диалоговое окно с кнопками")

## <a name="guiding-users-through-tasks"></a>Руководящие пользователей с помощью задач

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) является общий элемент пользовательского интерфейса в iOS. Xamarin.Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) метод позволяет включать этот элемент управления в кроссплатформенных приложений, Подготовка к просмотру собственного альтернативы в Android и UWP.

Чтобы отобразить лист действие `await` [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) в любом [ `Page` ](xref:Xamarin.Forms.Page), передавая сообщение и кнопку меток в виде строки. Метод возвращает строку метки кнопки, который был щелкнут пользователем. Ниже приведен простой пример.

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Диалоговое окно ActionSheet")

`destroy` Кнопка отображается по-другому, чем другие и можно оставить `null` или указан в качестве третьего параметра строку. В следующем примере используется `destroy` кнопки:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "диалоговое окно листа действий с кнопкой \"Destroy\"")](pop-ups-images/action2.png#lightbox "диалоговое окно листа действий с кнопкой \"Destroy\"")

## <a name="summary"></a>Сводка

В этой статье продемонстрированных окно оповещения и действия API-интерфейсы на простые вопросы пользователей и помогает пользователям сделать задачи. Xamarin.Forms имеет два метода [ `Page` ](xref:Xamarin.Forms.Page) классом для взаимодействия с пользователем через всплывающее окно: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) и [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*), и они являются Оба к просмотру с помощью соответствующих собственных элементов управления на каждой платформе.



## <a name="related-links"></a>Связанные ссылки

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
