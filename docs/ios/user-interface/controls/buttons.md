---
title: Кнопки в Xamarin.iOS
description: Класс UIButton используется для представления различных различные стили кнопки на экранах iOS. Это руководство описывает различные параметры для работы с кнопками в iOS.
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2018
ms.openlocfilehash: 32f6330ad2fddc2e8386d6e574918a011f3bebad
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986008"
---
# <a name="buttons-in-xamarinios"></a>Кнопки в Xamarin.iOS

В iOS `UIButton` класс представляет элемент управления button.

Свойства кнопки можно изменить программно или с помощью **панели свойств** из конструктора iOS:

![Панели свойств в конструкторе iOS](buttons-images/properties.png "панели свойств объекта в конструкторе iOS")

## <a name="creating-a-button-programmatically"></a>Создание кнопки программным способом

Объект `UIButton` могут создаваться с помощью всего нескольких строк кода.

- Создают экземпляр кнопки и указать его тип.

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  Тип кнопки задается `UIButtonType`:

  - `UIButtonType.System` -Кнопка общего назначения
  - `UIButtonType.DetailDisclosure` — Указывает, доступности, содержащие подробную информацию, обычно о конкретного элемента в таблицу
  - `UIButtonType.InfoDark` — Указывает доступность сведений о конфигурации; темные
  - `UIButtonType.InfoLight` — Указывает доступность сведений о конфигурации; Светлый
  - `UIButtonType..AddContact` — Указывает, что можно добавить контакт
  - `UIButtonType.Custom` -Настраиваемую кнопку

  Дополнительные сведения о различные типы кнопок взгляните на:
  
  - [Кнопку нестандартные](#custom-button-types) раздел этого документа
  - [Кнопку типы](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons) рецепт
  - Apple [iOS рекомендациям по интерфейсам](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/).

- Определите размер и положение кнопки:

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- Задайте текст кнопки. Используйте `SetTitle` метод, который должен отображаться текст и `UIControlState` значение:

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  Дополнительные сведения о Задание стиля кнопки и задание его текст см. в:

  - [Задание стиля кнопки](#styling-a-button) раздел этого документа
  - [Задать текст кнопки](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text) рецепт.

## <a name="handling-a-button-tap"></a>Обработка путем нажатия кнопки

Чтобы реагировать на них путем нажатия кнопки, можно создать обработчик для кнопки `TouchUpInside` событий:

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` не является событием доступно только кнопки. `UIButton` является дочерний класс этого `UIControl`, который определяет [различные события](https://developer.xamarin.com/api/type/UIKit.UIControlEvent/).

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>Использовании конструктора iOS, чтобы указать обработчики событий для кнопки

Используйте **события** вкладке **панели свойств** определить обработчики событий для кнопки различных событий.

Для соответствующего события введите имя нового обработчика событий или выберите его из списка. Это создаст обработчик событий в коде для контроллера представления кнопки.

![Вкладке "события" в панели свойств](buttons-images/image1.png "вкладке \"события\" в панели свойств")

## <a name="styling-a-button"></a>Задание стиля кнопки

`UIButton` элементы управления могут существовать в нескольких различных состояний, каждый указанных по `UIControlState` значение — `Normal`, `Disabled`, `Focused`, `Highlighted`и т. д. Каждое состояние можно предоставить уникальный стиль, указанный программно или с помощью конструктора iOS.

> [!NOTE]
> Полный список всех `UIControlState` значения, взгляните на [ `UIKit.UIControlState enumeration` ](https://developer.xamarin.com/api/type/UIKit.UIControlState/) документации.

Например, чтобы задать цвет заголовка и цвет тени для `UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Следующий код задает заголовок, кнопки строку с атрибутами (стилизованные) для `UIControlState.Normal` и `UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>Типы настраиваемых кнопок

Кнопки с `UIButtonType` из `Custom` не стили по умолчанию. Тем не менее можно настроить внешний вид кнопки, задав изображения для разных состояний:

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

В зависимости от того, является ли пользователь касается кнопки или нет, он будет обрабатывать как один из следующих образов (`UIControlState.Normal`, `UIControlState.Highlighted` и `UIControlState.Selected` состояния, соответственно):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted") 
 ![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

Дополнительные сведения о работе с пользовательскими кнопками см [использования изображения для кнопки](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) рецепт.

## <a name="related-links"></a>Связанные ссылки

- [UIButton книги](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
