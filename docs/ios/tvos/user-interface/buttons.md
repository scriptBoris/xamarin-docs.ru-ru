---
title: Работа с кнопками tvOS в Xamarin
description: В этом документе описывается работа с кнопками в приложении tvOS, созданных с помощью Xamarin. Он описывается работа с кнопками в коде, а также в раскадровках и рассматривается Задание стиля кнопки.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116346"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Работа с кнопками tvOS в Xamarin

Используйте экземпляр `UIButton` класс для создания кнопки, способному получать фокус, доступный для выбора в окне tvOS. Когда пользователь выбирает кнопку, чтобы она отправляет сообщение действие к целевому объекту разрешить входных ваш ответ приложения Xamarin.tvOS для пользователя.

[![](buttons-images/buttons01.png "Пример кнопки")](buttons-images/buttons01.png#lightbox)

Дополнительные сведения о работе с фокусом и переход с удаленной Siri, см. в разделе наших [работа с навигацией и фокусом](~/ios/tvos/app-fundamentals/navigation-focus.md) и [контроллеры Siri Remote и контроллеры Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) документации.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Сведения о кнопках

В tvOS кнопки используются для действий конкретного приложения и может содержать заголовок, значок или оба. Когда пользователь переходит с помощью пользовательского интерфейса приложения [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), фокус переместится на указанной кнопки, что изменение цвета текста и фона. Тень также применяется к кнопке, добавление объемные эффекты, чтобы он отображался, чтобы справиться с остальной части пользовательского интерфейса.

[![](buttons-images/buttons01.png "Пример кнопки")](buttons-images/buttons01.png#lightbox)

Apple имеет следующие рекомендации по работе с кнопками:

- **Название или значок используется** — при оба значка ограничено и заголовок может быть включено в кнопке, так что старайтесь не совмещения.
- **Четко разрушающее кнопки Марк** — Если с помощью кнопки разрушающая действия (например, при удалении файла), пометив его таким образом с помощью текста и/или значок. Разрушительные действия всегда должен представлять [оповещения](~/ios/tvos/user-interface/alerts.md) запросом для ограничения действия.
- **Не используйте кнопки Назад** -меню кнопки на удаленном Siri используется для возврата к предыдущему экрану. Единственным исключением этого правила является для покупки из приложений или разрушительные действия где **отменить** должна появиться кнопка.

Дополнительные сведения о работе с концентрацией и навигации, см. в разделе наших [работа с навигацией и фокусом](~/ios/tvos/app-fundamentals/navigation-focus.md) документации.

<a name="Button-Icons" />

### <a name="button-icons"></a>Изображения кнопок

Apple предлагает использовать простой, хорошо узнаваемого изображений для значков на кнопки. Слишком сложный значки трудно распознавать на экран ТВ пульта на диване, поэтому воспользуйтесь возможно получить идея собой простейшее представление. По возможности использовать стандарт, хорошо известному изображений для значков (например, для поиска увеличительное стекло).

<a name="Button-Titles" />

### <a name="button-titles"></a>Кнопка заголовки

При создании заголовков для кнопок, Apple имеет следующие рекомендации:

- **Показать описательный текст ниже кнопки значки** — там, где возможно, поместить понятные и описательный текст под значком только кнопки для дальнейшего получения назначения кнопки по.
- **Использование команд или фраз глагол для заголовка** -явно определять поместить действие, которое будет выполнять, когда пользователь нажимает кнопку.
- **Используйте стиль заголовка регистр** — за исключением статей, объединения или связи между понятиями (четыре буквы или меньше), должны быть с прописной буквы каждого слова название кнопки.
- **Используйте короткие, заголовка для точки** -использовать кратчайший возможных формулировки для описания действия кнопки.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Кнопки и раскадровки

Для работы с кнопками в приложении Xamarin.tvOS проще всего добавить их в пользовательском Интерфейсе приложения с помощью конструктора Xamarin для iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)


1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **кнопку** из **библиотеки** и поместите его в представлении: 

    [![](buttons-images/storyboard01.png "Кнопки")](buttons-images/storyboard01.png#lightbox)
1. В **обозревателе свойств**, вы можете настроить несколько свойств кнопки, такие как его **Title** и **цвет текста**: 

    [![](buttons-images/storyboard02.png "Свойства кнопок")](buttons-images/storyboard02.png#lightbox)
1. Затем переключиться в режим **вкладке "события"** и обработке **событий** из **кнопку** и назовите его `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "Вкладке \"события\"")](buttons-images/storyboard03.png#lightbox)
1. Вы автоматически переключается на `ViewController.cs` представление, где можно разместить новое действие кода с помощью **вверх** и **вниз** клавиш со стрелками: 

    [![](buttons-images/storyboard04.png "Размещение нового действия в коде")](buttons-images/storyboard04.png#lightbox)
1. Нажмите клавишу **ввод** для выбора расположения: 

    [![](buttons-images/storyboard05.png "Редактор кода")](buttons-images/storyboard05.png#lightbox)
1. Сохраните изменения ко всем файлам.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **кнопку** из **библиотеки** и поместите его в представлении: 

    [![](buttons-images/storyboard01vs.png "Кнопки")](buttons-images/storyboard01vs.png#lightbox)
1. В **обозревателе свойств**, вы можете настроить несколько свойств кнопки, такие как его **Title** и **цвет текста**: 

    [![](buttons-images/storyboard02vs.png "В обозревателе свойств")](buttons-images/storyboard02vs.png#lightbox)
1. Затем переключиться в режим **вкладке "события"** и обработке **событий** из **кнопку** и назовите его `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "Вкладке \"события\"")](buttons-images/storyboard03vs.png#lightbox)
1. Сохраните изменения ко всем файлам.



Изменение контроллера представления (пример `ViewController.cs`) и добавьте следующий код для обработки кнопке:


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

Пока кнопки `Enabled` свойство `true` и он не рассматривается другим элементом управления или представлении, его можно сделать элемент с фокусом, с помощью удаленного Siri. Если пользователь выбирает кнопку и нажимает области Touch `ButtonPressed` выполняются действия, определенный выше.

> [!IMPORTANT]
> Хотя можно назначить действия, такие как `TouchUpInside` для `UIButton` в конструктор при создании iOS **обработчик событий**, он никогда не вызываться, так как у Apple TV нет на сенсорном экране или поддерживает события касания. Следует всегда использовать значение по умолчанию **тип действия** при создании **действия** для элементов пользовательского интерфейса tvOS.




Дополнительные сведения о работе с раскадровками, см. в разделе наших [по Tvos краткое руководство по](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Кнопки и кода

При необходимости `UIButton` могут создаваться в C# код и добавить к представлению приложения tvOS. Пример:

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

При создании нового `UIButton` в коде, указать его `UIButtonType` как один из следующих:

- **Система** -это стандартный тип кнопки, представленный tvOS и имеет тип, который будет использоваться чаще всего.
- **DetailDisclosure** -представляется «выключить» тип кнопки, используемый, чтобы скрыть или отобразить подробные сведения.
- **InfoDark** -темной подробные сведения о, кнопка, отображенная в круг «i».
- **InfoLight** -света подробные сведения о, кнопка, отображенная в круг «i».
- **AddContact** -отображение кнопки в качестве кнопки Добавить контакт.
- **Custom** -позволяет настроить несколько признаков кнопки.

Определите на экране размер и расположение кнопки. Пример

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Затем задайте заголовок для кнопки. `UIButtons` отличаются от большинства `UIKit` элементов управления, в том, что они имеют состояние, поэтому вы не можете просто изменить заголовок, необходимо изменить его для данного `UIControlState`. Пример:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Затем используйте `AllEvents` событие, чтобы увидеть, когда пользователь нажимает кнопку. Пример

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Наконец необходимо добавить кнопку представления, чтобы отобразить ее:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Хотя можно назначить действия, такие как `TouchUpInside` для `UIButton`, он никогда не вызываться, так как у Apple TV нет на сенсорном экране или поддерживает события касания. Следует всегда использовать события например **AllEvents** или **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Задание стиля кнопки

tvOS предоставляет несколько свойств из `UIButton` , можно использовать для предоставления его название и оформить ее с помощью таких вещей, как цвет фона и изображения.

<a name="Button-Titles" />

### <a name="button-titles"></a>Кнопка заголовки

Как было показано выше, `UIButtons` отличаются от большинства `UIKit` элементов управления, в том, что они имеют состояние, поэтому вы не можете просто изменить заголовок, необходимо изменить его для данного `UIControlState`. Пример:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Можно задать цвет заголовка для кнопки с помощью `SetTitleColor` метод. Пример:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

А также изменять заголовок тень с помощью `SetTitleShadowColor`. Пример:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Можно задать тени заголовка для изменения из *Engraved* для *рельеф* при выделении кнопки следующим образом:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Кроме того можно использовать с атрибутами текст как название кнопки. Пример:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Изображения кнопки

Объект `UIButton` может иметь изображение, подключенные к ней и можно использовать изображение в качестве фона.

Чтобы задать фоновое изображение кнопки для заданного `UIControlState`, используйте следующий код:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Задайте `AdjustsImageWhenHiglighted` свойства `true` для рисования более светлый оттенок изображения, при выделении кнопки (по умолчанию). Задайте `AdjustsImageWhenDisabled` свойства `true` должно быть нарисовано изображение темнее в том случае, если кнопка отключена (опять же, это значение по умолчанию).

Чтобы задать изображение, отображаемое на кнопке, используйте следующий код:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Используйте `TintColor` свойство, чтобы задать оттенок цвета, который применяется к заголовку и изображение кнопки. Для кнопок `Custom` типа, это свойство не влияет, необходимо реализовать `TintColor` поведение самостоятельно.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работа с кнопками внутри приложения Xamarin.tvOS. Он показал, как работать с кнопками в конструкторе iOS и способ создания кнопки в C# кода. Наконец показал, как изменить название кнопки и изменить его стиль и внешний вид.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
