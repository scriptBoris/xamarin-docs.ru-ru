---
title: TextKit в Xamarin.iOS
description: В этом документе описывается использование TextKit в Xamarin.iOS. TextKit предоставляет мощное текста функции макета и подготовки к просмотру.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111425"
---
# <a name="textkit-in-xamarinios"></a>TextKit в Xamarin.iOS

TextKit — это новый API, который предлагает мощные текст функции макета и подготовки к просмотру. Она построена на основе низкоуровневых framework основного текста, но гораздо проще в использовании, чем основного текста.

Чтобы сделать возможности TextKit доступным для стандартных элементов управления, несколько текстовых элементов управления iOS были заново реализованы для использования TextKit, включая:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Архитектура

TextKit предоставляет многоуровневую архитектуру, которая отделяет хранилища текста из макета и отображения, включая следующие классы:

-  `NSTextContainer` — Предоставляет систему координат и geometry, который используется для макета текста.
-  `NSLayoutManager` — Размещает текст, превратив текст в глифы. 
-  `NSTextStorage` — Содержит текстовые данные, а также обрабатывает пакетные обновления свойств текста. Любые пакетные обновления их обработкой и отправкой диспетчера макетов для фактической обработки изменения, такие как повторное вычисление макета и перерисовки текста.


Эти три класса, применяются к представлению, который визуализирует текст. Обработка представления, такие как встроенный текст `UITextView`, `UITextField`, и `UILabel` их установить еще нет, но можно создать и применить их к любому `UIView` также экземпляра.

Эта архитектура показана на следующем рисунке:

 ![](textkit-images/textkitarch.png "На этом рисунке показана архитектура TextKit")

## <a name="text-storage-and-attributes"></a>Хранилища текста и атрибуты

`NSTextStorage` Класс содержит текст, отображаемый в представлении. Она также взаимодействует любые изменения в текст — такие как изменения символов или их атрибуты — для диспетчера макетов для отображения. `NSTextStorage` наследует от `MSMutableAttributed` строки, позволяя изменять атрибуты текста задается в виде пакетов между `BeginEditing` и `EndEditing` вызовов.

Например следующий фрагмент кода указывает изменение на передний план и фон цвета, соответственно и предназначен для определенного диапазона:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

После `EndEditing` является именем, изменения отправляются в диспетчер макет, который в свою очередь выполняет все необходимые макета и вычисления отрисовки для текста, отображаемого в представлении.

## <a name="layout-with-exclusion-path"></a>Макет с путь исключения

TextKit также поддерживает макет и позволяет для сложных сценариев, например, несколькими столбцами и заполнение текста вокруг указанного пути вызывается *исключить пути*. Исключить пути применяются к контейнера текста, который изменяет геометрию макета текста, в результате текст обтекает вокруг указанных путей.

Добавление путь исключения требует параметр `ExclusionPaths` свойство диспетчер структуры. При задании этого свойства диспетчера макета сделать недействительными макета текста и потока текста вокруг путь исключения.

### <a name="exclusion-based-on-a-cgpath"></a>Исключения, исходя из CGPath

Рассмотрим следующий `UITextView` подкласс реализации:

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Этот код добавляет поддержку для рисования текстового представления с помощью двухмерной графики. Так как `UITextView` класс создана для использования TextKit для отрисовки текста и макет, он может использовать все возможности TextKit, например, задание путей для исключения.

> [!IMPORTANT]
> Этот пример подклассы `UITextView` Добавление сенсорного ввода, поддержка рисования. Создание подкласса `UITextView` не является обязательным для доступа к возможностям TextKit.



После рисования для представления текста, рисуемого `CGPath` применяется к `UIBezierPath` экземпляра, задав `UIBezierPath.CGPath` свойство:

```csharp
bezierPath.CGPath = exclusionPath;
```

Обновление следующая строка кода делает обновить путь макета текста:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Снимке экрана ниже показано, как макета текста меняется вокруг вычерченного пути:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "На этом снимке экрана показано, как макета текста меняется вокруг нарисованную траекторию")

Обратите внимание, что диспетчер макета `AllowsNonContiguousLayout` свойству присваивается значение false, в данном случае. В этом случае макет к пересчету во всех случаях, когда изменяется текст. Этот параметр может повысить производительность за счет отключения обновления полной компоновки, особенно в случае больших документов. Однако можно присвоить `AllowsNonContiguousLayout` для true мешающие путь исключения обновление макета в некоторых случаях — к примеру, если текст вводится во время выполнения без возврата каретки конечные до в пути.


## <a name="related-links"></a>Связанные ссылки

- [Введение в iOS 7 (пример)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Обзор пользовательского интерфейса iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Фоновая обработка](~/ios/app-fundamentals/backgrounding/index.md)
