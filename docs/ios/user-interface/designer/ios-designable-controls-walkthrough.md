---
title: Использование пользовательских элементов управления в конструкторе IOS
description: В этом документе описывается, как создать пользовательский элемент управления и использовать его с помощью конструктора Xamarin для iOS. Показано, как сделать элемент управления в панели элементов конструктора iOS, реализовать элемент управления, таким образом, чтобы правильно отображаются и времени разработки и многое другое.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 0401c2c05677c719bbe4914cc7e008b650fdd198
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526246"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Использование пользовательских элементов управления в конструкторе IOS

## <a name="requirements"></a>Требования

Конструктор iOS Xamarin доступна в Visual Studio для Mac и Visual Studio 2015 и 2017 в Windows.

В этом руководстве предполагается Знакомство с содержимым, охваченных [руководства по началу работы](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Пошаговое руководство

> [!IMPORTANT]
> Начиная с версии Xamarin.Studio 5.5, немного отличается от более ранних версиях способ создания пользовательских элементов управления. Чтобы создать пользовательский элемент управления, либо `IComponent` интерфейс необходим (с помощью соответствующей реализации методов) или класса может сопровождаться атрибутом `[DesignTimeVisible(true)]`. Последний способ используется в следующем примере пошагового руководства.


1. Создайте новое решение из **iOS > приложение > приложение одного представления > C#** шаблона, назовите его `ScratchTicket`и следуйте указаниям мастера создания проекта:

    [![](ios-designable-controls-walkthrough-images/01new.png "Создание нового решения")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Создайте файл пустой класс с именем `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Создайте новый класс ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Добавьте следующий код для `ScratchTicketView` класса:

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;
    
            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }
    
            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }
    
            public ScratchTicketView()
            {
                Initialize();
            }
    
            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }
    
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }
    
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }
    
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }
    
            public override void Draw(CGRect rect)
            {
                base.Draw(rect);
    
                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);
    
                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();
    
                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }
    
                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);        
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```


1. Добавить `FillTexture.png`, `FillTexture2.png` и `Monkey.png` файлов (доступные [из GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) для **ресурсы** папки.
    
1. Дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструкторе:

    [![](ios-designable-controls-walkthrough-images/03new.png "Конструктор iOS")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Перетащите **представление изображений** из **элементов** на представление в раскадровке.

    [![](ios-designable-controls-walkthrough-images/04new.png "Это представление изображения, добавить в макет")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Выберите **представление изображений** и измените его **изображение** свойства `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Изображение представления изображение свойству присвоить значение Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. Так как мы используем классы размера необходимо ограничить в этом представлении изображения. Щелкните дважды, чтобы перевести в режим ограничения изображения. Давайте ограничить ее в центре, нажав кнопку закрепления center дескриптор и выравнивание по вертикали и горизонтали:

    [![](ios-designable-controls-walkthrough-images/06new.png "Центрирование изображения")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Чтобы ограничить высоту и ширину, щелкните закрепление размер маркеров (маркеры кости форме) и выберите ширины и высоты соответственно:

    [![](ios-designable-controls-walkthrough-images/07new.png "Добавление ограничений")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Обновите рамку, в зависимости от ограничений, щелкнув кнопкой "Обновить" на панели инструментов:

    [![](ios-designable-controls-walkthrough-images/08new.png "Панель инструментов ограничений")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. Затем выполнить сборку проекта, чтобы **Scratch представление билет** будет отображаться в разделе **настраиваемые компоненты** на панели элементов:

    [![](ios-designable-controls-walkthrough-images/09new.png "Настраиваемые компоненты панели элементов")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Перетаскивание **Scratch представление билет** таким образом, чтобы она размещалась над monkey изображение. Настройте маркеры перетаскивания, так, чтобы представление билет Scratch охватывает monkey полностью, как показано ниже:

    [![](ios-designable-controls-walkthrough-images/10new.png "Представление временных файлов билета через представление изображений")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Ограничить представление билет временных файлов в представление изображения, нарисовав ограничивающий прямоугольник, чтобы выбрать оба представления. Выберите параметры, чтобы ограничить ее ширину, высоту, Center и среднего и обновления кадров, в зависимости от ограничений, как показано ниже:

    [![](ios-designable-controls-walkthrough-images/11new.png "Центрирование и добавление ограничений")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Запустите приложение и «scratch off» образа, чтобы отобразить monkey.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Запустите пример приложения")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Добавление свойства времени разработки

Конструктор также включает в себя поддержку времени разработки для пользовательских элементов управления свойство числового типа, перечисления, строка, bool, CGSize, UIColor и UIImage. Чтобы продемонстрировать, давайте добавим свойство `ScratchTicketView` задать изображение, которое является «поцарапан Выкл.»

Добавьте следующий код, чтобы `ScratchTicketView` класса для свойства:

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image 
{
    get { return image; }
    set { 
            image = value;
            SetNeedsDisplay ();
        }
}
```

Мы также может потребоваться добавить проверку значений null для `Draw` метода, следующим образом:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);
            
        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);       
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Включая `ExportAttribute` и `BrowsableAttribute` со значением аргумента `true` приводит свойство, отображаемое в конструкторе **свойство** панели. Изменение значения свойства на другое изображение, включенный в проект, такие как `FillTexture2.png`, результаты при обновлении элемента управления во время разработки, как показано ниже:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Изменение свойства времени разработки")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Сводка

В этой статье мы рассмотрели способы создания пользовательского элемента управления, а также использовать его в приложении iOS с помощью конструктора iOS. Мы узнали, как создать и построить элемент управления, чтобы сделать его доступным для приложения в конструкторе **элементов**. Кроме того мы рассмотрели способы реализации элемента управления, таким образом, чтобы он правильно отображает во время разработки и среды выполнения, а также способ предоставления свойства пользовательского элемента управления в конструкторе.



## <a name="related-links"></a>Связанные ссылки

- [ScratchTicket (пример)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [необходимые изображения (пример)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
