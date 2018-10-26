---
title: Сведения game Fruity Falls
description: В этом руководстве рассматриваются игре Fruity падает, охватывающие распространенных CocosSharp и понятия разработки игр, например физики, управления содержимым, состояния игры и проектирование игр.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114253"
---
# <a name="fruity-falls-game-details"></a>Сведения game Fruity Falls

_В этом руководстве рассматриваются игре Fruity падает, охватывающие распространенных CocosSharp и понятия разработки игр, например физики, управления содержимым, состояния игры и проектирование игр._

Fruity Falls — это простой, основанное на физики игра, в которой игрок сортирует красных и желтых фруктов цветной сегментам набора баллов. Цель игры — получать баллы и столько максимально не сообщив фруктов перетаскивания в ячейку не так, завершения игры.

![](fruity-falls-images/image1.png "Цель игры — получать баллы и столько максимально не сообщив фруктов перетаскивания в ячейку не так, завершения игры")

Fruity Falls расширяет концепций, представленных в [руководство по игре BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) , добавив следующий:

 - Содержимое в формате PNG
 - Расширенные Физика
 - Состояния игры (переход между сценами)
 - Средства настройки игр коэффициенты через переменные, содержащиеся в одном классе
 - Встроенная поддержка visual отладки
 - Код организации, с помощью сущностей game
 - Проектирование игр, посвященные веселым и воспроизведения значение

Хотя [руководство по игре BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) внимание уделено Знакомство с CocosSharp основные понятия, использованные Fruity показано, как объедините все вместе в законченный продукт игр. Так как в этом руководстве ссылается на BouncingGame, читатели должны сначала ознакомиться с [руководство по игре BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) перед чтением этого руководства.

В этом руководстве описывается реализация и оформления Fruity за использованные для получения ценной аналитической информации, которые помогут выполнить свою собственную игру. Он включает в себя следующие темы:


- [Класс игровое](#gamecontroller-class)
- [Сущностей Game](#game-entities)
- [Графики фруктов](#fruit-graphics)
- [Физика](#physics)
- [Игра содержимого](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Класс игровое

Проект PCL Fruity попадает включает `GameController` класс, который отвечает за создание экземпляра игры и переход между сценами. Этот класс используется для iOS и Android проектов, чтобы исключить повторяющийся код.

Код, содержащийся в `Initialize` метод похож на код в`Initialize` метод на шаблон CocosSharp без изменений, но содержит ряд изменений.

По умолчанию `GameView.ContentManager.SearchPaths` зависит от разрешения устройства. Как объясняется ниже более подробно, использованные Fruity использует то же содержимое, независимо от того, разрешение устройства, поэтому `Initialize` добавляет метод `Images` путь (с нет вложенных папок) `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Новые шаблоны CocosSharp включают класс, наследуемый от `CCLayer`, подразумевая, что визуальные элементы игры и логика должны быть добавлены к этому классу. Вместо этого возвращается Fruity использует несколько `CCLayer` экземпляров к элементу управления рисовать заказа. Эти `CCLayer` экземпляры, содержащиеся в `GameView` класс, унаследованный от `CCScene`. Fruity Falls также включает в себя сцены, первый из которых `TitleScene`. Таким образом `Initialize` метод создает экземпляр `TitleScene` экземпляр, который передается `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Содержимое для Fruity за использованные был создан с низким разрешением пикселей графики по внешнему причинам. `GameView.DesignResolution` Таким образом, игра только в том случае 384 единиц в ширину и высоту в 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Наконец `GameController` класс предоставляет статический метод, который может вызываться по любому `CCGameScene` переход на другой `CCScene`. Этот метод используется для перемещения между `TitleScene` и `GameScene`.


## <a name="game-entities"></a>Сущностей Game

Fruity Falls использует шаблон сущности для большинства игр объектов. Подробное описание этого шаблона можно найти в [руководство по сущности в CocosSharp](~/graphics-games/cocossharp/entities.md).

Реализация сущностей объектами игры можно найти в папке сущностей в **FruityFalls.Common** проекта:

![](fruity-falls-images/image2.png "Сущности в папку FruityFalls.Common проекта")

Сущности — это объекты, которые наследуют от `CCNode`и может иметь визуальных элементов, конфликтов и поведение каждого кадра.

`Fruit` Объект представляет типичный сущность CocosSharp: он содержит визуальный объект, конфликтов и логика каждого кадра. Его конструктор отвечает за инициализацию фруктов:


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>Графики фруктов

`CreateFruitGraphic` Метод создает `CCSprite` экземпляра и добавляет ее к `Fruit`. `IsAntialiased` Свойство имеет значение false для придания игры более вида размытыми. Это значение имеет значение false для всех `CCSprite` и `CCLabel` экземпляров на протяжении всего игры:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Каждый раз, когда игрок касается `Fruit` с экземпляром `Paddle`, значение точки, фруктов увеличивается на единицу. Это обеспечивает создать дополнительные сложности для опытных проигрывателей корпоративные фруктов для дополнительных точек. Значение точки фрукта отображается с использованием `extraPointsLabel` экземпляра.

`CreateExtraPointsLabel` Метод создает `CCLabel` экземпляра и добавляет ее к `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity Falls включает в себя два вида фруктов — вишенки и лимонов. `CreateFruitGraphic` Назначает визуальный элемент по умолчанию, но фруктов визуальных элементов можно изменить с помощью `FruitColor` свойство, которое в свою очередь вызывает `UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

Первая инструкция if в `UpdateGraphics` обновляет отладки графики, которые используются для визуализации области конфликтов. Эти визуальные элементы будут отключены до окончательной версии игры выполняется, но могут храниться на во время разработки для отладки физики. Вторая часть изменения `graphic.Texture` свойства путем вызова `CCTextureCache.SharedTextureCache.AddImage`. Этот метод обеспечивает доступ к текстуре по имени файла. Дополнительные сведения об этом методе можно найти в [кэширование текстуры руководство](~/graphics-games/cocossharp/texture-cache.md).

`extraPointsLabel` Цвет является настраивается для сохранения контрастности, с учетом образа фруктов и его `PositionY` значение корректируется центр `CCLabel` на фрукта `CCSprite`:

![](fruity-falls-images/image3.png "Цвет extraPointsLabel регулируется следует контрастность с изображением фруктов, а его PositionY значение корректируется по центру CCLabel на fruits CCSprite")

![](fruity-falls-images/image4.png "Цвет extraPointsLabel регулируется следует контрастность с изображением фруктов, а его PositionY значение корректируется по центру CCLabel на fruits CCSprite")


### <a name="collision"></a>Конфликт

Fruity Falls реализует решение пользовательских конфликтов, используя объекты в папке Geometry.

![](fruity-falls-images/image5.png "Fruity Falls реализует решение пользовательских конфликтов, используя объекты в папке Geometry")

Конфликта за использованные Fruity реализуется с помощью либо `Circle` или `Polygon` объекта, обычно с помощью одного из этих двух типов, добавляется в качестве дочернего элемента сущности. Например `Fruit` объект имеет `Circle` вызывается `Collision` инициализирующий в его `CreateCollision` метод:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Обратите внимание, что `Circle` класс наследует от `CCNode` класса, поэтому его можно добавить как дочерний для нашей игре сущностей:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` Создание похоже на `Circle` создания, как показано в `Paddle` класса:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Конфликт логики рассматривается [далее в этом руководстве](#collision).


## <a name="physics"></a>Физика

Физика в Fruity за использованные можно разделить на две категории: перемещение и расчет столкновений. 


### <a name="movement-using-velocity-and-acceleration"></a>Перемещение с помощью и ускорение

Использует Fruity Falls `Velocity` и `Acceleration` значения для управления движением этих сущностей, аналогичную [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Сущности реализуют логику перемещения в метод с именем `Activity`, который вызывается для каждого кадра. Например, мы видим, реализация перемещения в `Fruit` класса `Activity` метод:

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
`Fruit` Объект является уникальным в своей реализации перетаскивания: значение, что снизит скорость по отношению к скорость фруктов — перемещение. Эта реализация перетаскивания предоставляет *Конечная скорость*, это максимальная скорость, можно разделить на экземпляр фруктов. Перетащите и замедляет работу перемещение по горизонтали фруктов, что облегчает игры немного воспроизведения.

`Paddle` Объектов также применяется `Velocity` в его `Activity` метода, но его `Velocity` вычисляется с помощью `desiredLocation` значение:


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

Как правило, игры, которые используют `Velocity` для управления положение их объектов не непосредственно задать позиций сущности, по крайней мере не после инициализации. Вместо того, чтобы непосредственно `Paddle` позиции, `Paddle.HandleInput` метод назначает `desiredLocation` значение:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Конфликт

Fruity Falls реализует полуреалистичное конфликт между фруктов и другие collidable объекты, такие как `Paddle` и `GameScene.Splitter`. Для отладки столкновений, областей за использованные Fruity конфликтов можно сделать видимыми путем изменения `GameCoefficients.ShowDebugInfo` в `GameCoefficients.cs` файла:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Установка этого значения в `true` позволяет подготовки к просмотру областей конфликтов:  

![](fruity-falls-images/image6.png "Значение true позволяет подготовки к просмотру областей конфликтов")

Конфликт логики начинается в `GameScene.Activity` метод:


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` обрабатывает все несовместимости `Fruit` экземпляры с другими объектами: 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` конфликт выполняет собственную логику для конфликтов, а не полагается на логики, содержащейся в другом классе. Это различие потому, что конфликт между фруктов и границы экрана не вполне solid — возможно фруктов выходить за пределы экрана путем тщательного ракетка перемещения. За пределами экрана, по достижении с ракетка отскакивает фруктов, но если игрок медленно помещает фруктов будет переместить за край, так и на экране:


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

`FruitVsBins` Метод отвечает за проверку, если любой фруктов опустился в один из двух ячеек. Если Да, проигрыватель присвоенных точек (если фруктов/bin цветов совпадение), или игра заканчивается (Если цвета не совпадают):


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle и FruitPolygonCollision

Фруктов и ракетка и фруктов и разделителя (область разделения двух ячеек) используют конфликтов `FruitPolygonCollision` метод. Этот метод состоит из трех частей:

1. Проверка объекта конфликтуют
1. Переместите объекты (только Фрукты в Fruity за использованные) таким образом, чтобы они больше не перекрываются
1. Настройка скорости объектов (только Фрукты в Fruity за использованные) для имитации bounce, следующий код демонстрирует пунктов, упомянутых выше:


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Ответ конфликтов Fruity Falls одна сторона — изменяются только скорость и позиция фрукта. Стоит отметить, возможно, другие игры для конфликтов, что влияет на обе объекты, например символ, который помещает Боулдер или аварийно завершается двумя автомобилями друг в друга.

Математически конфликт логики, содержащейся в `Polygon` и `CollisionResponse` классы выходит за рамки данного руководства, но как записано, эти методы могут использоваться для многих типов игры. Многоугольник и `CollisionResponse` даже классы поддерживают непрямоугольные и выпуклой многоугольники, поэтому этот код можно использовать для многих типов игры.

 


## <a name="game-content"></a>Игра содержимого

Рисунок Fruity за использованные немедленно отличает игры от BouncingGame. Хотя макеты игр похожи, игроков, сразу увидят различие в вида две игры. Игроки часто решить, следует ли повторить игры, его визуальные элементы. Поэтому очень важно, разработчики вкладывать средства в создание визуально привлекательные игры.

Картинки для Fruity за использованные был создан для следующих целей:

 - Темой
 - Внимание уделяется только один символ — Xamarin monkey
 - Яркие цвета для создания ослабить, приятной работы
 - Сравните между фона и переднего плана, поэтому легко визуально отслеживать объекты игровой процесс
 - Возможность создания простой визуальные эффекты без анимации большим количеством ресурсов


### <a name="content-location"></a>Расположение содержимого

Fruity Falls включает в себя все его содержимое в папке "Images" в проекте Android:

![](fruity-falls-images/image7.png "Fruity Falls включает в себя все его содержимое в папке изображений в проект Android")

Эти же файлы связаны в проект iOS, чтобы избежать дублирования, и поэтому изменения в файлах повлиять на оба проекта:

![](fruity-falls-images/image8.png "Эти же файлы связаны в проект iOS, чтобы избежать дублирования, и поэтому изменения в файлах повлиять на оба проекта")

Следует отметить, что содержимое не находится внутри **Ld** или **Hd** папки, которые являются частью CocosSharp шаблон по умолчанию. **Ld** и **Hd** папок предназначены для использования для игр, которые предоставляют два набора содержимого — одна для устройств с низким разрешением, таких как телефоны и один для устройств с более высоким разрешением, например на планшетах. Искусство Fruity за использованные намеренно создается с размытыми внешнего вида, поэтому не требуется для предоставления содержимого для различных размеров экрана. Таким образом **Ld** и **Hd** папки будут полностью удалены из проекта.


### <a name="gamescene-layering"></a>GameScene слоев

Как упоминалось ранее в этом руководстве `GameScene` отвечает за все создания экземпляра объекта игр, размещение и логику взаимодействия объектов (например, конфликт). Все объекты добавляются к одному из четырех `CCLayer` экземпляров:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Эти четыре уровня создаются в `CreateLayers` метод. Обратите внимание, что они добавляются `GameScene` в порядке к началу:


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

После создания слоев все визуальные объекты, добавляемые слои с помощью `AddChild` метод, как показано в `CreateBackground` метод:


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>Растительным орнаментом сущности

`Vine` Сущности однозначно служит для содержимого, — он не оказывает влияния на игровой процесс. Он состоит из 20 `CCSprite` экземпляров, число выбранных методом проб и ошибок, чтобы убедиться в том, растительным орнаментом всегда достигает верхней части экрана:


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

Первый `CCSprite` находится, поэтому ее нижнего края соответствует позиции растительным орнаментом. Это достигается путем установки AnchorPoint `new CCPoint(.5f, 0)`. `AnchorPoint` Используется в свойстве *нормализовать координаты* диапазон от 0 до 1, независимо от размера `CCSprite`:

![](fruity-falls-images/image9.png "Свойство AnchorPoint использует нормализованных координатах диапазон от 0 до 1, независимо от размера CCSprite")

Последующие растительным орнаментом спрайтов располагаются в соответствии с `graphic.ContentSize.Height` значение, которое возвращает высоту изображения в пикселях. На следующей схеме показано, как на рисунке растительным орнаментом вверх плитки:

![](fruity-falls-images/image10.png "На этой схеме показано, как на рисунке растительным орнаментом плитки вверх")

С момента начала координат `Vine` сущность является в нижней части растительным орнаментом, а затем позиционирования, он просто, как показано в `Paddle.CreateVines` метод:


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

Экземпляров растительным орнаментом `Paddle` сущности есть интересные поведение поворота. Так как `Paddle` сущности в целом поворачивается в соответствии с ввод данных игроком, (который в этом руководстве описывается более подробно ниже), `Vine` экземпляры также будут затронуты этой поворота. Тем не менее, поворот `Vine` экземпляров вместе с `Paddle` создает нежелательным визуальный эффект, как показано в следующем:

![](fruity-falls-images/image11.gif "Тем не менее поворот экземпляров растительным орнаментом вместе с ракетка порождает нежелательным visual эффект как показано в следующем анимации")

Для противодействия `Paddle` поворота `leftVine` и `rightVine` экземпляры будут повернуты противоположно направлению ракетка, как показано в `Paddle.Activity` метод:


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

Обратите внимание на то, что небольшой объем поворота добавляется к растительным орнаментом через `vineAngle` коэффициент. Это значение может быть изменено для корректировки, насколько vines поворот.


## <a name="gamecoefficients"></a>GameCoefficients

Каждый хорошую игру — это совокупность итерации, поэтому за использованные Fruity содержит класс с именем `GameCoefficients` для контроля, как игры. Этот класс содержит выразительный переменные, которые используются в игру, чтобы физики элемента управления, макет, запускающая и оценки.

Например Физика фруктов управляются следующие переменные:


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

Как следует из самих названий, эти переменные могут использоваться для корректировки за период, как по горизонтали фруктов, насколько быстро замедляется перемещения течением времени, а также амплитуду ракетка.

Игры коэффициенты, хранящиеся в файлах кода или данных (например, XML) может быть особенно полезным для команд с игр конструкторами, не являющихся также программистов.

`GameCoefficients` Класс также содержит значения Включение отладочной информации, например порождение сведения или конфликт областей:


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>Заключение

В этом руководстве рассмотрены в игре Fruity падает. Он рассматриваются основные понятия, включая содержимое, физики и управления состояния игры.

## <a name="related-links"></a>Связанные ссылки

- [Документация по API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
