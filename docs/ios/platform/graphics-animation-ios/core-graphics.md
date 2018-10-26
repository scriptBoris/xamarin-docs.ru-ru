---
title: Двухмерной графики в Xamarin.iOS
description: В этой статье рассматриваются платформы iOS двухмерной графики. В этом примере показано использование Core Graphics для рисования geometry, изображения и PDF-файлы.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f3fe22e56a2c45524923a316ef28e54e5a3cc3f8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102975"
---
# <a name="core-graphics-in-xamarinios"></a>Двухмерной графики в Xamarin.iOS

_В этой статье рассматриваются платформы iOS двухмерной графики. В этом примере показано использование Core Graphics для рисования geometry, изображения и PDF-файлы._

включает в себя iOS [ *двухмерной графики* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) framework и обеспечивает поддержку низкого уровня рисования. Эти платформы являются обеспечивают широкие возможности графического в UIKit. 

Двухмерной графики — это платформа низкого уровня двумерной графики, которая позволяет рисования аппаратно независимая графика. Все 2D, рисование в UIKit внутри использует двухмерной графики.

Двухмерной графики поддерживает рисование в ряде сценариев, в том числе:

-  [Рисование на экране с помощью `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Рисование изображений в памяти или на экране](#Drawing_Images_and_Text).
-  Создание и рисования в PDF-файл.
-  Для чтения и Рисование существующего PDF-файла.


## <a name="geometric-space"></a>Геометрические пространства

Независимо от сценария все операции рисования, выполненные с двухмерной графики выполняется геометрические пробел, это означает, что он работает в абстрактный точек, а не в точках. Вы описываете желаемого рисоваться с точки зрения геометрии и Рисование состояния, таких как цвета, стили линий, т. д. и двухмерной графики обрабатывает все преобразования в пикселях. Такое состояние добавляется в контекст графики, который можно рассматривать как холст по образцу.

Существует несколько преимуществ такого подхода:

-  Код становится динамические, а впоследствии можно изменить графики во время выполнения.
-  Снижая потребность в статические изображения в пакете приложения можно уменьшить размер приложения.
-  Графики становятся более устойчивым к Изменение разрешения на устройствах.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Рисование в подкласс UIView

Каждый `UIView` имеет `Draw` метод, который вызывается системой, когда он должен быть нарисован. Чтобы добавить код рисования к представлению, подкласс `UIView` и переопределить `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Draw никогда не должен вызываться напрямую. Вызывается системой во время выполнения цикла обработки. В первый раз с помощью выполнения цикла после добавления представления иерархии представлений, его `Draw` вызывается метод. Последующие вызовы `Draw` возникать, когда представление помечается как нуждающийся в рисуется с помощью вызова `SetNeedsDisplay` или `SetNeedsDisplayInRect` в представлении.

### <a name="pattern-for-graphics-code"></a>Шаблон для кода графики

Код в `Draw` реализации должны описывать изложить формируемого. Код рисования соответствует шаблону, в котором он задает некоторое состояние, рисования и вызывает метод для запроса, он рисуется. Этот шаблон можно обобщить следующим образом:

1. Получите контекст графики.

2. Задайте атрибуты рисования.

3. Создание некоторых геометрических объектов из графических примитивов.

4. Вызовите метод Draw или рисования штрихов.

### <a name="basic-drawing-example"></a>Пример базовых средств рисования

Например рассмотрим следующий фрагмент кода:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {
            
    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100), 
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);       
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Давайте разобьем следующий код:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
С помощью этой строки он получает текущим графическим контекстом, используемый для рисования. Можно считать контекста графики canvas, рисование передачах, содержащий все данные о состоянии о рисунком, таких как штриха и цвета заливки, а также геометрии для рисования.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

После получения графического контекста в коде настраивается некоторые атрибуты отображения, показано выше. В этом случае устанавливаются цвета линий ширины, штриха и заливки. Все последующие операции рисования будет использовать эти атрибуты, так как они сохраняются в состоянии графического контекста.

Создание геометрического объекта в коде используется `CGPath`, что позволяет графический контур, описываемый из линий и кривых. В этом случае путь добавляет линий, соединяющих массив точек, чтобы сложить треугольника. Как показано ниже используется двухмерной графики в системе координат для рисования представления, где источником является в левом верхнем углу с положительным x-direct вправо, а оси y положительные вниз:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

После создания пути он добавляется к контексту графики, таким образом, вызов `AddPath` и `DrawPath` соответственно можно создать его.

Ниже показано результирующее представление:

 ![](core-graphics-images/00-bluetriangle.png "Пример выходных данных треугольник")

## <a name="creating-gradient-fills"></a>Создание градиента заливки

Также доступны широкие формы отрисовки. Например Core Graphics позволяет создавать градиентные заливки и применение контуров обрезки. Чтобы нарисовать градиентную заливку путь из предыдущего примера, сначала путь должен быть задан в качестве контура обрезки:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Параметр по текущему пути, как контур обрезки ограничивает все последующие операции рисования в геометрический путь, например, следующий код, который рисует линейного градиента:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient, 
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top), 
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom), 
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Эти изменения позволяют градиентную заливку, как показано ниже:

 ![](core-graphics-images/01-gradient-fill.png "Пример с градиентной заливкой")

## <a name="modifying-line-patterns"></a>Изменение строки шаблонов

Атрибуты рисования строк можно также изменить при помощи двухмерной графики. Это включает в себя изменение ширины и stroke цвет линии, а также шаблон линии, как показано в следующем коде:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Добавив этот код перед рисования результатов операций в long, пунктирные обводки 10 единиц для работы с 4 единицы интервала между тире, как показано ниже:

 ![](core-graphics-images/02-dashed-stroke.png "Добавив следующий код перед рисования результатов операций в пунктирных штрихов")
 
Обратите внимание, что при использовании на единый API в Xamarin.iOS, тип массива должна быть `nfloat`и также должен быть явно приведен к Math.PI.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Рисование изображений и текста

Помимо рисования пути в контексте графического представления, Core Graphics также поддерживает рисования текста и изображений. Для рисования изображения, просто создайте `CGImage` и передать его в `DrawImage` вызова:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

Тем не менее в результате получается изображения, нарисованного сверху вниз, как показано ниже:

 ![](core-graphics-images/03-upside-down-monkey.png "Изображения, нарисованного сверху вниз")

Причиной этого является origin Core Graphics для рисования изображения находится в левом нижнем, а представление расположен в левом верхнем углу. Таким образом, чтобы правильно отображать изображения, источника должно быть изменено, что можно сделать, изменив *текущую матрицу преобразования* *(CTM)*. CTM определяет места проживания точек, называемых также *пространства пользователя*. Инверсия CTM по оси y и сдвигая его границы высота по оси y отрицательное можно перевернуть изображение.

Графический контекст содержит вспомогательные методы для преобразования CTM. В этом случае `ScaleCTM` «отражение» Рисование и `TranslateCTM` переходит в левый верхний угол, как показано ниже:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
    
    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}   
```

Полученное изображение отображается вертикально:

 ![](core-graphics-images/04-upright-monkey.png "Вертикальное положение изображения, отображаемого образца")

> [!IMPORTANT]
> Изменения графического контекста применяются для всех последующих операций рисования. Таким образом когда CTM преобразуется, изменения затронут все дополнительные рисование. Например если после преобразования CTM нарисованную треугольник, может показаться сверху вниз.

### <a name="adding-text-to-the-image"></a>Добавление текста в образе

Как пути и изображения, рисование текста с графикой Core включает тот же базовый шаблон настройки некоторых состояние графики и вызов метода для рисования. В случае с текстом, метод для отображения текста — `ShowText`. При добавлении к образу, рисование пример, приведенный ниже Рисует текст с помощью двухмерной графики:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);
    
    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Как вы видите, состояние графики для рисования текста задается аналогично рисованию geometry. Тем не менее рисования текста, также применяются рисования режим и шрифт текста. В этом случае тень также применяется, несмотря на то, что применение shadows работает так же, для пути рисования.

Получившийся текст отображается с изображением, как показано ниже:

 ![](core-graphics-images/05-text-on-image.png "Получившийся текст отображается с использованием образа")

## <a name="memory-backed-images"></a>Образы на основе памяти

Помимо рисования для представления графического контекста двухмерной графики поддерживает рисование памяти на базе образов, также известный как рисование вне экрана. Для этого необходимо:

-  Создание графического контекста, поддерживаемый обработки в памяти растрового изображения
-  Установка состояния, рисования и выдача команд рисования
-  Получение изображения из контекста
-  Удаление контекста


В отличие от `Draw` метод, где контекст предоставляется путем представления, в этом случае создании контекста в одном из двух способов:

1. Путем вызова `UIGraphics.BeginImageContext` (или `BeginImageContextWithOptions`)

2. При создании нового `CGBitmapContextInstance`

 `CGBitmapContextInstance` полезно при работе непосредственно с биты изображения, например для случаев, когда используется алгоритм обработки пользовательского образа. Во всех остальных случаях следует использовать `BeginImageContext` или `BeginImageContextWithOptions`.

Получив контекст изображения, добавив код отрисовки — так же, как он находится в `UIView` подкласс. Например, в примере выше используется для рисования треугольник можно использовать для рисования изображения в памяти, а не в `UIView`, как показано ниже:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100), 
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }
    
    return triangleImage;
}
```

Обычно документа на основе памяти растрового изображения используется для записи образа из любого `UIView`. Например, следующий код подготавливают слой представления на контекст растрового изображения и создает `UIImage` от него:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Рисование PDF-файлы

Помимо образов Core Graphics поддерживает рисование PDF. Например, изображения, вы может визуализации PDF-ФАЙЛ в памяти, а также чтение PDF-ФАЙЛ для подготовки к просмотру в `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF-ФАЙЛ в UIView

Графики Core также поддерживает чтение PDF-ФАЙЛ из файла и отображения его в представление с использованием `CGPDFDocument` класса. `CGPDFDocument` Класс представляет PDF-ФАЙЛ, в коде и может использоваться для чтения и рисования страницы.

Например, следующий код в `UIView` подкласс PDF-ФАЙЛ, считывает из файла в `CGPDFDocument`:

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }
  
     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

`Draw` Можно затем использовать метод `CGPDFDocument` необходимо считать страницу в `CGPDFPage` и визуализировать его путем вызова `DrawPDFPage`, как показано ниже:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);
        
    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);
        
        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {
            
        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);
        
        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);
        
        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF-ФАЙЛ на основе памяти

Для PDF-документ в памяти, необходимо создать контекст PDF путем вызова `BeginPDFContext`. Документ в PDF более детализированные на страницы. Каждая страница, начатую посредством вызова `BeginPDFPage` и завершается путем вызова метода `EndPDFContent`, с помощью графического кода между ними. Кроме того как с помощью рисования изображения памяти скопированный PDF, рисование использует источник в нижнем левом углу, который может быть включен, только изменив CTM для, например с изображениями.

Ниже показано, как для рисования текста в документ PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();
       
using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);      
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }
    
//complete a PDF page
UIGraphics.EndPDFContent ();
```

Получившийся текст рисуется в формат PDF, которые затем содержатся в `NSData` , могут сохраняться, отправленного, полученный по электронной почте и т. д.


## <a name="summary"></a>Сводка

В этой статье мы рассмотрели возможности графики, предоставляемого *двухмерной графики* framework. Мы узнали, как использовать Core Graphics для рисования geometry, изображения и PDF-файлы в контексте `UIView,` а также с контекстами графики на основе памяти.

## <a name="related-links"></a>Связанные ссылки

- [Пример графика Core](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Графика и анимация Пошаговое руководство](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Рецепты Core анимации](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
