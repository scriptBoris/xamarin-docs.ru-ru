---
title: Свойства автоматизации
description: В этой статье объясняется, как использовать класс AutomationProperties в приложении Xamarin.Forms, таким образом, чтобы средство чтения с экрана можно говорить об элементах на странице.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c720b9f38d2a34155face10b75f5f054f3313711
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131917"
---
# <a name="automation-properties-in-xamarinforms"></a>Свойства автоматизации в Xamarin.Forms

_Xamarin.Forms позволяет значений специальных возможностей для элементов пользовательского интерфейса с помощью вложенных свойств в классе AutomationProperties, которые в свою очередь собственные специальные значения. В этой статье объясняется, как использовать класс AutomationProperties, таким образом, чтобы средство чтения с экрана можно говорить об элементах на странице._

Xamarin.Forms позволяет свойства автоматизации для элементов пользовательского интерфейса с помощью следующих вложенных свойств:

- `AutomationProperties.IsInAccessibleTree` — Указывает, доступен ли элемент доступности приложения. Дополнительные сведения см. в разделе [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` — Краткое описание элемента, который служит в качестве speakable идентификатора для элемента. Дополнительные сведения см. в разделе [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` — более подробное описание элемента, который может рассматриваться как текст подсказки, связанный с элементом. Дополнительные сведения см. в разделе [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` — позволяет другому элементу, определить сведения о специальных возможностях для текущего элемента. Дополнительные сведения см. в разделе [AutomationProperties.LabeledBy](#labeledby).

Эти присоединенного свойства набора собственные специальные значения таким образом, чтобы средство чтения с экрана можно говорить об элементе. Дополнительные сведения о вложенных свойствах см. в разделе [присоединенного свойства](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> С помощью `AutomationProperties` присоединенные свойства могут влиять на выполнение тестов пользовательского интерфейса на устройстве Android. [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId), `AutomationProperties.Name` И `AutomationProperties.HelpText` свойства оба задаются собственные `ContentDescription` свойство, с помощью `AutomationProperties.Name` и `AutomationProperties.HelpText` значения свойств, которые имеют приоритет перед `AutomationId`значение (если оба `AutomationProperties.Name` и `AutomationProperties.HelpText` задаются, будут сцеплены значения). Это означает, что все тесты, ищете `AutomationId` завершится ошибкой, если `AutomationProperties.Name` или `AutomationProperties.HelpText` также задаются в элементе. В этом случае тестов пользовательского интерфейса должна быть изменена для поиска значения `AutomationProperties.Name` или `AutomationProperties.HelpText`, или сочетание обоих.

Каждая платформа имеет различные озвучивания расположит значений специальных возможностей:

- iOS имеет VoiceOver. Дополнительные сведения см. в разделе [специальные возможности тестирования на устройства с VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) на developer.apple.com.
- В Android имеется talkback;. Дополнительные сведения см. в разделе [специальных возможностей для тестирования приложения](https://developer.android.com/training/accessibility/testing.html#talkback) на developer.android.com.
- Windows имеет экранного диктора. Дополнительные сведения см. в разделе [проверить сценарии основного приложения с помощью экранного диктора](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

Однако точное поведение средства чтения с экрана зависит от программного обеспечения и настройка пользователя его. Например большинство средств чтения с экрана прочитать текст, связанный с элементом управления, получивший фокус, позволяя выполнять ориентацию при переходе между элементами управления на странице. Некоторые средства чтения с экрана также считывать все приложение пользовательского интерфейса при появлении страницы, которая позволяет пользователю получать все доступные информационное содержимое страницы, прежде чем пытаться перемещаться по нему.

Средства чтения с экрана также считывать значения разные уровни доступа. В примере приложения:

- VoiceOver будет считывать `Placeholder` значение `Entry`, а затем инструкции по использованию элемента управления.
- Будет считывать talkBack `Placeholder` значение `Entry`, за которым следует `AutomationProperties.HelpText` значения с инструкциями по использованию элемента управления.
- Экранный диктор будет считывать `AutomationProperties.LabeledBy` значение `Entry`, а затем инструкции по использованию элемента управления.

Кроме того, определять их приоритеты Экранный диктор `AutomationProperties.Name`, `AutomationProperties.LabeledBy`, а затем `AutomationProperties.HelpText`. В Android могут сочетаться TalkBack `AutomationProperties.Name` и `AutomationProperties.HelpText` значения. Таким образом рекомендуется, специальных возможностей тщательное тестирование выполняется для каждой платформы, для оптимальной работы.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

`AutomationProperties.IsInAccessibleTree` Присоединенное свойство `boolean` , определяющий, если элемент является доступным и поэтому видимым для средства чтения с экрана. Оно должно быть присвоено `true` использовании других специальных присоединенных свойств. Это можно сделать в XAML следующим образом:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

Кроме того его можно задать в C# следующим образом:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Обратите внимание, что [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод может также использоваться для задания `AutomationProperties.IsInAccessibleTree` присоединенного свойства — `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

`AutomationProperties.Name` Значение вложенного свойства должен быть короткие, описательные текстовую строку, средство чтения с экрана для объявления элемента. Это свойство должно задаваться для элементов, которые имеют значение, что важно для понимания содержания или взаимодействия с пользовательским интерфейсом. Это можно сделать в XAML следующим образом:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

Кроме того его можно задать в C# следующим образом:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Обратите внимание, что [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод может также использоваться для задания `AutomationProperties.Name` присоединенного свойства — `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

`AutomationProperties.HelpText` Присоединенное свойство должно быть присвоено текст, описывающий элемент пользовательского интерфейса, и может рассматриваться в качестве текста подсказки связан с элементом. Это можно сделать в XAML следующим образом:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

Кроме того его можно задать в C# следующим образом:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Обратите внимание, что [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод может также использоваться для задания `AutomationProperties.HelpText` присоединенного свойства — `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

На некоторых платформах для редактирования элементы управления, например [ `Entry` ](xref:Xamarin.Forms.Entry), `HelpText` иногда свойство можно опустить и заменить текст заполнителя. Например, «введите здесь имя» является хорошим кандидатом для [ `Entry.Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) свойство, которое размещает текст в элементе управления до фактического ввод пользователя.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

`AutomationProperties.LabeledBy` Присоединенное свойство позволяет другому элементу, определить сведения о специальных возможностях для текущего элемента. Например [ `Label` ](xref:Xamarin.Forms.Label) рядом с полем [ `Entry` ](xref:Xamarin.Forms.Entry) может использоваться для описания того, что `Entry` представляет. Это можно сделать в XAML следующим образом:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

Кроме того его можно задать в C# следующим образом:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Обратите внимание, что [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) метод может также использоваться для задания `AutomationProperties.IsInAccessibleTree` присоединенного свойства — `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="related-links"></a>Связанные ссылки

- [Вложенные свойства](~/xamarin-forms/xaml/attached-properties.md)
- [Специальные возможности (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
