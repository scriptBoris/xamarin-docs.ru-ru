---
title: Использование CocosSharp в Xamarin.Forms
description: CocosSharp может использоваться для добавления точное фигуры, изображения и отрисовку текста в приложение для расширенного представления
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: c823eb27552f0a42ad428ed6f36790e925079295
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998811"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Использование CocosSharp в Xamarin.Forms

_CocosSharp может использоваться для добавления точное фигуры, изображения и отрисовку текста в приложение для расширенного представления_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Усовершенствовано в 2016 г: Cocos # в Xamarin.Forms**

## <a name="overview"></a>Обзор

CocosSharp — это гибкая и мощная технология для отображения графики, чтение сенсорный ввод, воспроизведение аудио-и управление. В этом руководстве объясняется, как добавить CocosSharp в приложения Xamarin.Forms. Здесь рассматривается следующее:

* [Что такое CocosSharp](#what)
* [Добавление пакетов CocosSharp Nuget](#nuget)
* [Пошаговое руководство: Добавление CocosSharp в приложение Xamarin.Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>Что такое CocosSharp

[CocosSharp](~/graphics-games/cocossharp/index.md) является подсистемой игры с открытым исходным кодом, доступный на платформе Xamarin.
CocosSharp является эффективным для среды выполнения библиотеки, которая содержит следующие компоненты:

* Образ с помощью визуализации [CCSprite-класс](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* Фигуры с помощью визуализации [класс CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* С помощью логики каждый кадр [CCNode.Schedule-метод](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* С помощью управления содержимым (Загрузка и выгрузка ресурсы, такие как PNG-файлы) [CCTextureCache-класс](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* Анимаций с помощью [класс CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

В CocosSharp основное внимание уделяется упрощают создание кросс платформенных игр 2D; Тем не менее он может быть отличным добавлением к приложения на основе форм Xamarin. Поскольку игр обычно требуют эффективной визуализации и точный контроль над визуальных элементов, CocosSharp можно использовать для добавления эффективную визуализацию и эффекты не игровых приложений.

Xamarin.Forms построена на основе собственного, специфические для платформы пользовательского интерфейса системы. Например [ `Button`s](xref:Xamarin.Forms.Button) выглядят по-разному в iOS и Android и даже может отличаться по версиям операционной системы. Напротив CocosSharp не используют все визуальные объекты платформы, поэтому все визуальные объекты выглядят одинаково на всех платформах. Само собой разрешение и пропорции отличаются между устройствами, и это может повлиять на прорисовку CocosSharp его визуальные элементы. Эти сведения будут рассматриваться далее в этом руководстве.

Более подробные сведения можно найти в [CocosSharp разделе](~/graphics-games/cocossharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Добавление пакетов CocosSharp Nuget

Прежде чем использовать CocosSharp, разработчики должны сделать ряд дополнений в свои проекты Xamarin.Forms.
В этом руководстве предполагается, что проект Xamarin.Forms с помощью iOS, Android и .NET Standard проект библиотеки.
Весь код будет записываться в проекте библиотеки .NET Standard; Тем не менее библиотеки должны добавляться проекты iOS и Android.

Пакет CocosSharp Nuget содержит все объекты, необходимые для создания объектов CocosSharp.
Пакет nuget CocosSharp.Forms включает `CocosSharpView` класс, который используется для размещения CocosSharp в Xamarin.Forms.
Добавить **CocosSharp.Forms** NuGet и **CocosSharp** будут автоматически добавляться также.
Чтобы сделать это, щелкните правой кнопкой мыши <span class="UIItem">пакетов</span> папки в проект библиотеки .NET Standard и выберите <span class="UIItem">добавить пакеты... </span>. Введите условие поиска <span class="UIItem">CocosSharp.Forms</span>выберите <span class="UIItem">CocosSharp для Xamarin.Forms</span>, затем нажмите кнопку <span class="UIItem">Add Package</span>.

![](cocossharp-images/image1.png "Добавление пакетов диалогового окна")

Оба **CocosSharp** и **CocosSharp.Forms** будут добавлены в проект пакеты NuGet:

![](cocossharp-images/image2.png "Папка пакетов")

Повторите описанные выше действия для проектов для конкретной платформы (например, iOS и Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Пошаговое руководство: Добавление CocosSharp в приложение Xamarin.Forms

Выполните следующие действия, чтобы добавить простое представление CocosSharp в приложение Xamarin.Forms.

1. [Создание Xamarin страницы форм](#1)
1. [Добавление CocosSharpView](#2)
1. [Создание GameScene](#3)
1. [Добавление круг](#4)
1. [Взаимодействие с CocosSharp](#5)

Когда вы успешно добавили представления CocosSharp в приложение Xamarin.Forms, перейдите на [CocosSharp документации](~/graphics-games/cocossharp/index.md) Дополнительные сведения о создании содержимого с помощью CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Создание Xamarin страницы форм

CocosSharp может размещаться в любом контейнере Xamarin.Forms. В этом примере для данной страницы используется страница с именем `HomePage`. `HomePage` разбивается пополам, `Grid` Показать, как Xamarin.Forms CocosSharp могут быть визуализированы и одновременно на одной странице.

Во-первых, Настройка параметров страницы, чтобы он содержал `Grid` и два `Button` экземпляров:


```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

В iOS `HomePage` отображается, как показано на следующем рисунке:

![](cocossharp-images/image3.png "Снимок экрана домашней страницы")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Добавление CocosSharpView

`CocosSharpView` Класс используется для внедрения CocosSharp в Xamarin.Forms приложение. Так как `CocosSharpView` наследует от [Xamarin.Forms.View](xref:Xamarin.Forms.View) класс, он предлагает привычный интерфейс для макета, и он может использоваться внутри контейнеров макета например [Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid). Добавьте новый `CocosSharpView` в проект, выполнив `CreateTopHalf` метод:


```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

CocosSharp инициализация не выполняется сразу же, поэтому рекомендуем зарегистрироваться событие когда `CocosSharpView` завершения его создания. Это можно сделать `HandleViewCreated` метод:


```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

`HandleViewCreated` Метод имеет две важные сведения, которые мы будем рассматривать. Во-первых, `GameScene` класс, который будет создан в следующем разделе. Важно отметить, что приложение не будет компилироваться до `GameScene` создается и `gameScene` экземпляр ссылка будет разрешена.

Второй важный момент — `DesignResolution` свойство, которое определяет видимую область игры для CocosSharp объектов. `DesignResolution` Свойства будут рассмотрены после создания `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Создание GameScene

`GameScene` Класс наследует от элемента CocosSharp `CCScene`. `GameScene` Это первая точка, где мы имеем дело только с помощью CocosSharp. Код, содержащийся в `GameScene` работает в любом приложении CocosSharp ли он размещается в пределах проекта Xamarin.Forms или нет.

`CCScene` Класс является корнем visual все CocosSharp отрисовки. Любого видимого объекта CocosSharp должны содержаться в `CCScene`. В частности, необходимо добавить визуальные объекты `CCLayer` экземпляров и их `CCLayer` необходимо добавить экземпляры `CCScene`.

Приведенный ниже график могут помочь визуализировать типичный CocosSharp иерархии:

![](cocossharp-images/image4.png "Типичный иерархии CocosSharp")

Только один `CCScene` могут быть активны одновременно. Большинство игр используйте несколько `CCLayer` экземпляры, чтобы Сортировка содержимого, но наше приложение использует только один. Аналогичным образом большинстве игр используется несколько визуальных объектов, но у нас будет только один в наше приложение. Более подробный рассказ о CocosSharp, визуальной иерархии можно найти в [Пошаговое руководство по игре BouncingGame](~/graphics-games/cocossharp/bouncing-game.md).

Изначально `GameScene` класс будет практически пустым – мы просто создадим его соответствие ссылка в `HomePage`. Добавьте новый класс в проект .NET Standard library с именем `GameScene`. Он должен наследовать от `CCScene` следующим образом:


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Теперь, когда `GameScene` — мы определены, можно вернуться к `HomePage` и добавьте поля:


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Теперь можно скомпилировать наш проект и запустить его, чтобы увидеть CocosSharp под управлением. Мы еще не добавили ничего для наших `GameScene,` это черный — цвет по умолчанию сцены CocosSharp в верхней части нашей странице:

![](cocossharp-images/image5.png "Пустой GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Добавление круг

В приложении используется запущенный экземпляр обработчика CocosSharp, отображение пустой `CCScene`. Далее мы добавим визуальный объект: круг. `CCDrawNode` Класс может использоваться для рисования различных геометрических фигур, как описано в [Рисование геометрия с CCDrawNode руководство](~/graphics-games/cocossharp/ccdrawnode.md).

Добавить круг наших `GameScene` класса и создать его экземпляр в конструкторе, как показано в следующем коде:


```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

Выполнение приложения теперь показывает круг в левой части области отображения CocosSharp:

![](cocossharp-images/image6.png "Круг в GameScene")


#### <a name="understanding-designresolution"></a>Основные сведения о DesignResolution

Теперь, когда отображается визуального объекта CocosSharp, мы сможем решить проблему `DesignResolution` свойство.

`DesignResolution` Представляет ширину и высоту области CocosSharp по размещению и изменение размера объектов. Фактическое разрешение области измеряется в *пикселей* хотя `DesignResolution` измеряется в мире *единицы*. На следующей схеме показана разрешение различные части представления, отображаться на iPhone 5 с разрешением экрана 640 x 1136 пикселей:

![](cocossharp-images/image7.png "iPhone 5s разрешение разработки")

На схеме выше показаны измерения, пикселей с внешней стороны экрана черным шрифтом. Единицы отображаются внутри схемы в белым текстом. Ниже приведены некоторые важные сведения, отображенный выше.

* Источником отображение CocosSharp является слева внизу. В правой увеличивает значение X и перемещении вверх увеличивает значение Y. Обратите внимание, что значение Y инвертируется по сравнению с некоторые другие обработчики плоского макета, где (0,0) находится в левой верхней части холста.
* Чтобы сохранить пропорции его представления является CocosSharp по умолчанию. Поскольку шире, чем высота первой строки в сетке, CocosSharp не заполняет всю ширину элемента свою ячейку, как показано пунктирной белого прямоугольника. Это поведение можно изменить, как описано в разделе [руководство обработка нескольких разрешений в CocosSharp](~/graphics-games/cocossharp/resolutions.md).
* В этом примере CocosSharp сохранит область отображения 100 единиц ширину, высоту, независимо от размера или соотношение сторон своего устройства. Это означает, что код можно предположить, что X = 100 представляет правую привязан CocosSharp отображения, сохранение макета, согласованные на всех устройствах.


#### <a name="ccdrawnode-details"></a>Сведения о CCDrawNode

Простое приложение использует `CCDrawNode` класс Рисование окружности. Этот класс может быть очень полезно для бизнес-приложений, так как он обеспечивает отрисовки геометрического объекта на основе векторов — это функция, отсутствующие на Xamarin.Forms. В дополнение к круги `CCDrawNode` класс может использоваться для рисования прямоугольников, сплайны, линий и многоугольников пользовательские. `CCDrawNode` также является простой в использовании, так как он не требуется использовать файлы изображений (.png). Более подробное рассмотрение CCDrawNode можно найти в [Рисование геометрия с CCDrawNode руководство](~/graphics-games/cocossharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. Взаимодействие с CocosSharp

CocosSharp визуальные элементы (такие как `CCDrawNode`) наследовать от `CCNode` класса. `CCNode` предоставляет два свойства, которые могут использоваться для размещения объекта относительно его родительского элемента: `PositionX` и `PositionY`. В настоящее время наш код использует эти два свойства для размещения центра круга, как показано в этом фрагменте кода:


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

Это важно отметить, что CocosSharp объекты располагаются с явно указанными значениями позиции, в отличие от большинства представления Xamarin.Forms, которые автоматически располагаются в соответствии с поведение их родительские элементы управления макета.

Мы добавим код, чтобы разрешить пользователю щелкните одну из двух кнопок, чтобы переместить элемент управления circle влево или вправо, 10 единиц (не пикселей, поскольку Рисует элемент управления circle в абсолютном единицы CocosSharp пространстве). Сначала мы создадим два открытых метода в `GameScene` класса:


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Далее мы добавим обработчики две кнопки в `HomePage` реакция на щелчок мыши. После завершения наших `CreateBottomHalf` метод содержит следующий код:


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

Элемент управления circle CocosSharp теперь перемещает в ответ на нажатия. Также четко видно границы CocosSharp полотна, перемещая элемент управления circle достаточно далеко влево или вправо:

![](cocossharp-images/image8.png "GameScene с перемещением круг")

## <a name="summary"></a>Сводка

В этом руководстве показано, как добавить CocosSharp в Xamarin.Forms существующего проекта, как создать взаимодействие между Xamarin.Forms и CocosSharp и рассматриваются различные вопросы, при создании макетов в CocosSharp.

Игровое ядро CocosSharp и предлагает множество функциональные возможности и глубина, поэтому в этом руководстве представлены только общие действия CocosSharp. Разработчиков, заинтересованных в изучать дополнительные сведения о CocosSharp можно найти множество статей в [CocosSharp разделе](~/graphics-games/cocossharp/index.md).



## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы CocosSharp](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms (пример)](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
