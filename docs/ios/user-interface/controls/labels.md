---
title: Метки в Xamarin.iOS
description: В этом документе описывается использование меток в Xamarin.iOS. В этом примере описывает создание метки программным способом и в конструкторе Ios.
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b52bdbd41eaafbc5e6c78e1f8514b701fd78bd6b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241917"
---
# <a name="labels-in-xamarinios"></a>Метки в Xamarin.iOS

`UILabel` Элемент управления используется для отображения одного или нескольких строк, читать только текст. 

## <a name="implementing-a-label"></a>Реализация метку

Новая метка создается путем создания экземпляра [ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>Метки и раскадровки

Можно также добавить метку для пользовательского интерфейса при использовании конструктора iOS. Поиск **метка** в **элементов** и перетащите его в представление:

![Метки в панели элементов](labels-images/image3.png)

На панели свойств можно изменить следующие свойства:

![Панель меток "Свойства"](labels-images/image2.png)

- **Текст контекста** — обычный или с атрибутами. Обычный текст можно задать [атрибутов форматирования](#Formatting_Text_and_Label) на всю строку. Тексты с атрибутами позволяет задать форматирование различных символов или слов в строке.
- **Цвета, шрифта, выравнивания** — форматирование атрибуты, которые могут применяться к метке.
- **Строки** — задает число строк, которые могут охватывать метки. Это значение равным 0, чтобы разрешить метку, используемую столько строк, при необходимости.
- **Поведение** — можно установить значение Enabled или выделенная. Enabled имеет значение по умолчанию, Выключенный текст будет отображаться в более светлый оттенок серого цвета. Выделены отключен по умолчанию и позволяет перерисовку с выделенной состояние, когда он выбран пользователем.
- **Baselane и разрыв строки** — 
    - Определяет базовые показатели, как текст будет помещен размеры шрифтов, отличается от указанного.
    - Разрывы строк определяют, как строка будет в оболочку или усечена, если более чем одной строке.
- **AutoShrink** — определяет, как будет минимальным размером шрифта в метке, при необходимости.
- **Выделен, тень, смещение** — можно задать цвет выделенной и тень, и смещение тени.

## <a name="truncating-and-wrapping"></a>Усечение и упаковки

Дополнительные сведения об использовании строки нарушает работу в iOS, см. в [усечение и переносить текст](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text) рецепт.

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>Форматирование текста и метки

Для форматирования строк, используемом в метке можно либо задать атрибуты на всю строку форматирования, или вы можете использовать строки с атрибутами. Ниже приведены примеры, как реализовать их.

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

Дополнительные сведения об использовании стиля текста `NSAttributedString` ссылаться на [стиль текста](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text) рецепт.

По умолчанию имеют метки `Enabled` присвоено значение true, но его можно установить отключено, чтобы дать пользователю подсказку, что определенный элемент управления отключен:

```csharp
label.Enabled = false;
```

Это задает метку для светлой серый цвет, как показано на следующем изображении пример экрана ограничения в iOS:

![Кнопку в отключенном состоянии в iOS](labels-images/image1.png)

Можно также задать цвета текста для выделения и тени в тексте метки для дополнительных эффектов:

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

Отображает текст следующим образом:

![Выделение цветом и тень набора по тексту](labels-images/image4.png)

Дополнительные сведения об изменении шрифт UILabel см [изменить шрифт](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font) рецепт.





