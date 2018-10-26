---
title: Специальные возможности на iOS
description: В этом документе описаны специальные возможности в iOS, описание различных свойств и функций, которые могут использоваться для обеспечения приложения можно использовать для любого числа пользователей максимально.
ms.prod: xamarin
ms.assetid: 88D59B36-05A3-4356-AE29-EC2B69CE7162
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/18/2016
ms.openlocfilehash: aa3e15797ae1dac621ea8a78345044be1387ebaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108903"
---
# <a name="accessibility-on-ios"></a>Специальные возможности на iOS

Эта страница описывает способ использования iOS специальных API-интерфейсы для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).
Ссылаться на [Android специальных возможностей](~/android/app-fundamentals/accessibility.md) и [специальных возможностей OS X](~/mac/app-fundamentals/accessibility.md) страниц для других API платформы.

## <a name="describing-ui-elements"></a>Описание элементов пользовательского интерфейса

предоставляет iOS `AccessibilityLabel` и `AccessibilityHint` свойства разработчикам возможность добавлять описательный текст, который может использоваться с VoiceOver экрана средство чтения, чтобы элементы управления более доступным. Также можно пометить элементы управления с признаками один или несколько, которые предоставляют дополнительный контекст в доступных режимов.

Некоторые элементы управления могут не должны быть доступны (для, например, метки для текстового поля или изображения, которое является исключительно декоративным) – `IsAccessibilityElement` отключить специальные возможности в таких случаях.

**Конструктор пользовательского интерфейса**

**Панели свойств** содержится раздел специальных возможностей, позволяющий эти параметры для редактирования при выборе элемента управления в пользовательском Интерфейсе конструктора iOS:

![](accessibility-images/ios-designer-sml.png "Параметры специальных возможностей")

**C#**

Эти свойства можно также задать непосредственно в коде:

```csharp
usernameInput.AccessibilityLabel = "Search";
usernameInput.Hint = "Press Enter after typing to search employee list";
someLabel.IsAccessibilityElement = false;
displayOnlyText.AccessibilityTraits = UIAccessibilityTrait.Header | UIAccessibilityTrait.Selected;
```

### <a name="what-is-accessibilityidentifier"></a>Что такое AccessibilityIdentifier?

`AccessibilityIdentifier` Используется для задания уникальный ключ, который может использоваться для ссылки на элементы пользовательского интерфейса с помощью API автоматизации пользовательского интерфейса.

Значение `AccessibilityIdentifier` никогда не произносятся и не отображается для пользователя.

<a name="postnotification" />

## <a name="postnotification"></a>PostNotification

`UIAccessibility.PostNotification` Метод позволяет события для пользователя за пределами прямого взаимодействия (например, когда они взаимодействуют с определенного элемента управления).

### <a name="announcement"></a>Замечание

Объявления могут отправляться из кода, чтобы информировать пользователей, изменится определенное состояние (например, выполняется фоновая операция завершена). Это может сопровождаться визуальное в пользовательском интерфейсе:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.Announcement,
    new NSString(@"Item was saved"));
```

### <a name="layoutchanged"></a>LayoutChanged представления

`LayoutChanged` Объявления используется при макет экрана:

```csharp
UIAccessibility.PostNotification (
  UIAccessibilityPostNotification.LayoutChanged,
    someControl);  // someControl gets focus
```


## <a name="accessibility-and-localization"></a>Локализации и специальных возможностей

Свойства специальных возможностей, как метки и указания могут быть локализованы только как другой текст в пользовательском интерфейсе.

**MainStoryboard.strings**

Если пользовательский интерфейс, изложенные в раскадровке, вы можете предоставить перевод для свойства специальных возможностей в так же, как другие свойства. В следующем примере `UITextField` имеет **ИД локализации** из `Pqa-aa-ury` и два свойства специальных возможностей, устанавливаемое на испанском языке:

```csharp
/* Accessibility */
"Pqa-aa-ury.accessibilityLabel" = "Notas input";
"Pqa-aa-ury.accessibilityHint" = "escriba más información";
```

Этот файл будет помещен в **es.lproj** каталог для испанского содержимого.

**Localizable.strings**

Кроме того, можно добавить переводы **Localizable.strings** файл локализованные каталога содержимого (например) **ES.lproj** для испанского языка):

```csharp
/* Accessibility */
"Notes" = "Notas input";
"Provide more information" = "escriba más información";
```

Эти преобразования могут использоваться в C# через `LocalizedString` метод:

```csharp
notesText.AccessibilityLabel = NSBundle.MainBundle.LocalizedString ("Notes", "");
notesText.AccessibilityHint = NSBundle.MainBundle.LocalizedString ("Provide more information", "");
```

Ссылаться на [руководство по локализации iOS](~/ios/app-fundamentals/localization/index.md) узнать больше о локализации содержимого.

<a name="testing" />

## <a name="testing-accessibility"></a>Тестирование специальных возможностей

VoiceOver включен в **параметры** приложение, перейдя по адресу **Общие > Специальные возможности > VoiceOver**:

![](accessibility-images/settings-sml.png "Установка скорости речи")

**Специальных возможностей** экрана также предоставляет параметры для масштабирования, размер текста, цвет & контрастности параметры, параметры речи и другие параметры конфигурации.

Следуйте этим [VoiceOver инструкции](https://developer.apple.com/library/ios/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) для проверки специальных возможностей на устройствах iOS.


## <a name="simulator-testing"></a>Тестирования на симуляторе

При тестировании в симуляторе, **специальных возможностей инспектор** доступен для проверки специальных возможностей свойства и события настроены правильно. Включить инспектор в **параметры** приложение, перейдя по адресу **Общие > Специальные возможности > Инспектор специальных возможностей**:

![](accessibility-images/settings-inspector-sml.png "Включить инспектор специальных возможностей")

После включения окно инспектора наводит на экране iOS все время.
Ниже приведен пример выходных данных, при выборе представления строки таблицы — Обратите внимание, что **метка** содержит предложение, предоставляющее содержимое строки, и Кроме того, он является «Готово» (т. е. деления является видимым):

![](accessibility-images/tableview-a11y-sml.png "Использование инспектора специальных возможностей")

Хотя инспектор является видимым, используйте значок «X» в левом верхнем временно Показать и скрыть наложения и включить или отключить параметры специальных возможностей.



## <a name="related-links"></a>Связанные ссылки

- [Кросс платформенных специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md)
- [iOS (Apple) специальных возможностей](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/iPhoneAccessibility/Accessibility_on_iPhone/Accessibility_on_iPhone.html)
- [iOS VoiceOver](http://www.apple.com/accessibility/ios/voiceover/)
