---
title: Отображение всплывающих элементов
description: 'В Xamarin.Forms существует два всплывающих элемента пользовательского интерфейса: предупреждение и лист действий. В этой статье демонстрируется использование интерфейсов API предупреждений и листов действий, которые позволяют задавать пользователям простые вопросы и предоставлять им пошаговые инструкции по выполнению задач.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1334340f18e664d4c652803e7678f45ee942eea8
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057409"
---
# <a name="displaying-pop-ups"></a>Отображение всплывающих элементов

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)

_В Xamarin.Forms существует два всплывающих элемента пользовательского интерфейса: предупреждение и лист действий. В этой статье демонстрируется использование интерфейсов API предупреждений и листов действий, которые позволяют задавать пользователям простые вопросы и предоставлять им пошаговые инструкции по выполнению задач._

Вывод предупреждения или вариантов на выбор — стандартная задача в пользовательском интерфейсе. В Xamarin.Forms есть два метода, относящихся к классу [`Page`](xref:Xamarin.Forms.Page), для взаимодействия с пользователем посредством всплывающих элементов: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) и [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*). Эти элементы визуализируются на каждой платформе с помощью соответствующих собственных элементов управления.

## <a name="displaying-an-alert"></a>Отображение предупреждения

На всех платформах, поддерживаемых Xamarin.Forms, есть модальный всплывающий элемент, позволяющий выводить предупреждения или задавать простые вопросы пользователю. Для вывода предупреждений в Xamarin.Forms используйте метод [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) на любой странице [`Page`](xref:Xamarin.Forms.Page). Следующая строка отображает простое сообщение:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Диалоговое окно предупреждения с одной кнопкой")

В этом примере не предполагается получение сведений от пользователя. Предупреждение отображается в модальном режиме, и после его закрытия пользователь продолжает работать с приложением.

Метод [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) можно также использовать для получения ответа от пользователя. Для этого предлагаются две кнопки и возвращается значение типа `boolean`. Для получения ответа на предупреждение предоставьте надписи для обеих кнопок и примените к методу оператор `await`. После того как пользователь выберет один из вариантов, ответ возвращается в код. Обратите внимание на ключевые слова `async` и `await` в примере кода ниже.

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Диалоговое окно предупреждения с двумя кнопками")](pop-ups-images/alert2.png#lightbox "Диалоговое окно предупреждения с двумя кнопками")

## <a name="guiding-users-through-tasks"></a>Предоставление пользователям инструкций по выполнению задач

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) — это стандартный элемент пользовательского интерфейса в iOS. Метод Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) позволяет включать этот элемент управления в кроссплатформенные приложения. При этом в Android и UWP будут отрисовываться собственные аналоги.

Чтобы отобразить лист действий, используйте метод [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) с оператором `await` на любой странице [`Page`](xref:Xamarin.Forms.Page), передав сообщение и надписи кнопок в виде строк. Этот метод возвращает надпись кнопки, нажатой пользователем. Вот простой пример.

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Диалоговое окно с листом действий")

Кнопка `destroy` отрисовывается не так, как другие. Ее можно оставить со значением `null` или указать в качестве третьего строкового параметра. В следующем примере используется кнопка `destroy`.

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Диалоговое окно с листом действий и кнопкой удаления")](pop-ups-images/action2.png#lightbox "Диалоговое окно с листом действий и кнопкой удаления")

## <a name="summary"></a>Сводка

В этой статье было продемонстрировано использование интерфейсов API предупреждений и списков действий, которые позволяют задавать пользователям простые вопросы и предоставлять им пошаговые инструкции по выполнению задач. В Xamarin.Forms есть два метода, относящихся к классу [`Page`](xref:Xamarin.Forms.Page), для взаимодействия с пользователем посредством всплывающих элементов: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) и [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*). Эти элементы визуализируются на каждой платформе с помощью соответствующих собственных элементов управления.



## <a name="related-links"></a>Связанные ссылки

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
