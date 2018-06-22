---
title: Игры приходится Fruity подробные сведения
description: В этом руководстве рассматриваются приходится Fruity игры, охватывающие CocosSharp общие и основные понятия разработки игр как физический, управления содержимым, состояния игры и разработки игр.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 19b57d41fdf8aa4f8a749cf4979755161a0e7e51
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921564"
---
# <a name="fruity-falls-game-details"></a>Игры приходится Fruity подробные сведения

_В этом руководстве рассматриваются приходится Fruity игры, охватывающие CocosSharp общие и основные понятия разработки игр как физический, управления содержимым, состояния игры и разработки игр._

Приходится Fruity — это простой, основанное на физических игра, в которой игрок сортирует красные и желтые фруктов в цветной сегменты, чтобы получить точки. Игра предназначена для того, чтобы заработать столько точки можно без уведомления drop фруктов в ячейке неправильный конечный игры.

![](fruity-falls-images/image1.png "Цель игры — заработать столько точки можно без уведомления drop фруктов в ячейке неправильный конечный игры")

Возвращается Fruity расширяет концепции, реализованные в [BouncingGame руководство](~/graphics-games/cocossharp/bouncing-game.md) , добавив следующий код:

 - Содержимое в формате PNG
 - Дополнительно физических
 - Состояния игры (переход между сцены)
 - Средства настройки игры коэффициенты через переменные, содержащиеся в одном классе
 - Встроенная поддержка visual отладки
 - Код организации с помощью игры сущностей
 - Игры проектирования основное внимание уделено значение интересных и воспроизведения

Хотя [BouncingGame руководство](~/graphics-games/cocossharp/bouncing-game.md) основное внимание уделено введение в основные понятия CocosSharp, приходится Fruity показано, как привести его вместе в готовой продукции игры. Так как в этом руководстве ссылается BouncingGame, читатели следует сначала ознакомиться с [BouncingGame руководство](~/graphics-games/cocossharp/bouncing-game.md) перед чтением этого руководства.

В настоящем руководстве описывается реализация и разработки для предоставления ценной информации, которые помогут принять собственную игру Fruity снижается. Он содержит следующие разделы:


- [Класс игровое](#gamecontroller-class)
- [Игры сущностей](#game-entities)
- [Графики фруктов](#fruit-graphics)
- [Физический](#physics)
- [Игра содержимого](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Класс игровое

Fruity PCL попадает проект включает `GameController` класс, который отвечает за создание экземпляра игры и перемещение между сцены. Этот класс используется iOS и Android проекты, чтобы исключить повторяющиеся кода.

Код, содержащийся в `Initialize` метод похож на код в`Initialize` метод в шаблон CocosSharp без изменений, но он содержит ряд изменений.

По умолчанию `GameView.ContentManager.SearchPaths` зависит от разрешения устройства. Описанные ниже более подробно, приходится Fruity использует то же содержимое, вне зависимости от разрешения устройства, поэтому `Initialize` добавляет метод `Images` путь (без вложенных папок) для `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Класс, наследующий от включают новые шаблоны CocosSharp `CCLayer`, подразумевая, что визуальные элементы игры и логика должны быть добавлены к этому классу. Вместо этого приходится Fruity использует несколько `CCLayer` порядок рисования экземпляров элемента управления. Эти `CCLayer` экземпляров содержащихся в `GameView` класс, унаследованный от класса `CCScene`. Приходится Fruity также включает в себя сцены, первый, `TitleScene`. Таким образом `Initialize` метод создает `TitleScene` экземпляр, который передается в `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Содержимое приходится Fruity был создан с низким разрешением пикселей графики в целях внешнего вида. `GameView.DesignResolution` Задается так, чтобы игры только 384 единиц в ширину и высоту в 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Наконец `GameController` класс предоставляет статический метод, который может быть вызван любой `CCGameScene` переход на другой `CCScene`. Этот метод используется для перемещения между `TitleScene` и `GameScene`.


## <a name="game-entities"></a>Игры сущностей

Возвращается Fruity использует шаблон сущности для большинства объектов игры. Подробное описание того, этот шаблон можно найти в [руководства сущностей в CocosSharp](~/graphics-games/cocossharp/entities.md).

Реализация сущности игры объекты находятся в папке сущностей в **FruityFalls.Common** проекта:

![](fruity-falls-images/image2.png "Сущности в папку FruityFalls.Common проекта")

Сущности — это объекты, которые наследуют от `CCNode`и, возможно, визуальные элементы, конфликтов и поведение каждого кадра.

`Fruit` Объект представляет типичный сущность CocosSharp: он содержит визуальный объект, конфликтов и логика каждые кадра. Его конструктору отвечает за инициализацию фруктов:


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

`CreateFruitGraphic` Метод создает `CCSprite` экземпляра и добавляет его в `Fruit`. `IsAntialiased` Свойство имеет значение false, чтобы придать игры некачественно вид. Это значение имеет значение false во всех `CCSprite` и `CCLabel` экземпляры во всей игры:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Каждый раз, когда игрок касается `Fruit` экземпляра с `Paddle`, значение точки этой фруктов увеличивается на единицу. Это позволяет опытным проигрыватели корпоративные фруктов для дополнительных точек дополнительного запроса. Значение точки фруктов отображается с использованием `extraPointsLabel` экземпляра.

`CreateExtraPointsLabel` Метод создает `CCLabel` экземпляра и добавляет его в `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Возвращается Fruity включает два различных типа фруктов — вишнями и лимонов. `CreateFruitGraphic` Назначает визуальный элемент по умолчанию, но фруктов визуальных элементов можно изменить с помощью `FruitColor` свойство, которое в свою очередь вызывает `UpdateGraphics`:


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

Первая инструкция if в `UpdateGraphics` обновляет отладки графики, которые используются для визуализации области конфликтов. Эти визуальные элементы будут отключены до окончательной версии игры выполняется, а также могут храниться на во время разработки для отладки физических. Вторая часть изменений `graphic.Texture` свойство путем вызова `CCTextureCache.SharedTextureCache.AddImage`. Этот метод предоставляет доступ к текстуры по имени файла. Дополнительные сведения об этом можно найти в [кэширование текстуры руководства](~/graphics-games/cocossharp/texture-cache.md).

`extraPointsLabel` Цвет настраивается для сохранения контрастность с изображением фруктов и его `PositionY` значение корректируется центр `CCLabel` на фруктов `CCSprite`:

![](fruity-falls-images/image3.png "Цвет extraPointsLabel регулируется для сохранения контрастность с изображением фруктов, а его PositionY значение корректируется по центру CCLabel на Фрукты CCSprite")

![](fruity-falls-images/image4.png "Цвет extraPointsLabel регулируется для сохранения контрастность с изображением фруктов, а его PositionY значение корректируется по центру CCLabel на Фрукты CCSprite")


### <a name="collision"></a>Конфликт

Приходится Fruity реализует решения пользовательских конфликтов с помощью объектов в папке Geometry.

![](fruity-falls-images/image5.png "Возвращается Fruity реализует решения пользовательских конфликтов с помощью объектов в папке Geometry")

Конфликт в приходится Fruity реализуется с помощью `Circle` или `Polygon` объекта, обычно с помощью одного из этих двух типов, добавляемого в качестве дочернего элемента сущности. Например `Fruit` объект имеет `Circle` вызывается `Collision` инициализирующий в его `CreateCollision` метод:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Обратите внимание, что `Circle` класс наследует от `CCNode` класса, поэтому могут быть добавлены как дочерний для наших игры сущностей:


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


## <a name="physics"></a>Физический

Физический в приходится Fruity можно разделить на две категории: перемещение и конфликтов. 


### <a name="movement-using-velocity-and-acceleration"></a>Перемещение с помощью и ускорение

Использует приходится Fruity `Velocity` и `Acceleration` значения для управления движением этих сущностей, аналогично [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Сущности реализовывать свою логику перемещения в метод с именем `Activity`, который вызывается один раз на кадр. Например, можно увидеть реализацию перемещения в `Fruit` класса `Activity` метод:

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
`Fruit` Объект является уникальным в своей реализации перетаскивания — значение, что снижает скорость относительно быстродействия фруктов перемещения. Эта реализация перетаскивания предоставляет *терминалов скорости*, являющееся максимальной скорости, может быть экземпляр фруктов. Перетащите также завершает работу перемещение по горизонтали фруктов, что облегчает игры немного воспроизведения.

`Paddle` Применяется объект `Velocity` в его `Activity` метода, но его `Velocity` вычисляется с помощью `desiredLocation` значение:


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

Как правило, игры, которые используют `Velocity` для управления позицию их объекты непосредственно не задать позиций сущности не менее после инициализации. Вместо непосредственного задания `Paddle` положение, `Paddle.HandleInput` назначает метод `desiredLocation` значение:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Конфликт

Возвращается Fruity реализует частично реалистичных конфликт между фруктов и другие collidable объекты, например `Paddle` и `GameScene.Splitter`. Для отладки конфликтов, приходится Fruity конфликтов областей можно сделать видимыми путем изменения `GameCoefficients.ShowDebugInfo` в `GameCoefficients.cs` файла:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Установка этого значения равным `true` позволяет для подготовки к просмотру областей конфликтов:  

![](fruity-falls-images/image6.png "Значение true включает для подготовки к просмотру областей конфликтов")

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

`FruitVsBorders` конфликт выполняет собственную логику для конфликтов, а не полагаться на логики, содержащейся в другом классе. Это различие существует из-за конфликтов между фруктов и границами экрана не вполне сплошная — возможна фруктов достаточно передать за пределы экрана внимательны ракетка перемещения. Фруктов будет переходить от экрана при попадании с ракетка, но если игрок медленно помещает фруктов будут перемещены за край, так и вне экрана:


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

`FruitVsBins` Метод отвечает за проверку, если все плоды задержался в один из двух ячеек. В этом случае проигрыватель присвоенных точек (если фруктов/bin цветов совпадение), или игра заканчивается (Если цвета совпадают):


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

Фруктов и ракетка и фруктов и разделителей (область разделения двух ячейках) используют конфликтов `FruitPolygonCollision` метод. Этот метод состоит из трех частей:

1. Проверка конфликтуют объекты
1. Переместить объекты (только Фрукты в приходится Fruity), чтобы они больше не перекрываются
1. Настройка скорости объектов (только Фрукты в приходится Fruity) для имитации показатель, в следующем коде показано пунктов, упомянутых выше:


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

Ответ возвращается Fruity конфликтов одна сторона — корректируются только скорости и положение фруктов. Стоит отметить, что другие игры могут иметь конфликтов, которые влияют на обоих объекты, такие как символ, который помещает Боулдер или двумя автомобилями аварийное завершение работы друг в друга.

Математически конфликт логики, содержащейся в `Polygon` и `CollisionResponse` классы выходит за рамки данного руководства, но как записано, эти методы можно использовать для многих типов игры. Многоугольник и `CollisionResponse` классы даже поддерживают непрямоугольные и выпуклая многоугольников, поэтому этот код можно использовать для многих типов игры.

 


## <a name="game-content"></a>Игра содержимого

Рисунок Fruity возвращается немедленно отличает игры от BouncingGame. Во время игр макеты похожи, проигрыватели будут немедленно заметить разницу в вида две игры. Игроки часто решить, попробуйте игру по его визуальные элементы. Поэтому очень важно, разработчики вложить средства в принятии визуально привлекательные игры.

Иллюстрации для Fruity приходится был создан для следующих целей:

 - Темой
 - Акцент на как один символ — Xamarin monkey
 - Возникают яркого цвета для создания гибких ослабить,
 - Сравните между фона и переднего плана, поэтому можно легко отслеживать визуально игрового процесса объектов
 - Возможность создания простых визуальных эффектов без анимации тяжелой ресурсов


### <a name="content-location"></a>Расположение содержимого

Возвращается Fruity включает в себя все его содержимое в папке «изображения» в проекте Android:

![](fruity-falls-images/image7.png "Возвращается Fruity включает в себя все его содержимое в папке «изображения» в проекте Android")

Эти же файлы связаны в проекте iOS, чтобы избежать дублирования, и поэтому изменения в файлах повлиять на обоих проектов:

![](fruity-falls-images/image8.png "Эти же файлы связаны в проекте iOS, чтобы избежать дублирования, и поэтому изменения в файлах повлиять на обоих проектов")

Стоит отметить, что содержимое не находится в пределах **Ld** или **Hd** папки, которые являются частью CocosSharp шаблон по умолчанию. **Ld** и **Hd** папок предназначены для использования для игр, которые предоставляют два набора содержимого — один для устройств с низким разрешением, таких как телефоны и один для устройств более высоким разрешением, таких как планшетные ПК. Art приходится Fruity намеренно создается с некачественно внешнего вида, поэтому необходимо предоставить содержимое для разных размеров. Таким образом **Ld** и **Hd** папки будут полностью удалены из проекта.


### <a name="gamescene-layering"></a>GameScene слоев

Как упоминалось ранее в этом руководстве `GameScene` отвечает за все создания экземпляра объекта игры, позиционирование и логику взаимодействия объектов (например, конфликт). Все объекты добавляются к одному из четырех `CCLayer` экземпляров:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Эти четыре уровня создаются в `CreateLayers` метод. Обратите внимание, что они добавляются в `GameScene` в порядке их к началу:


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

После создания слои все визуальные объекты добавляются слои с помощью `AddChild` метода, как показано в `CreateBackground` метод:


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

`Vine` Однозначно сущности используется для содержимого — он не оказывает влияния на игрового процесса. Он состоит из twenty `CCSprite` экземпляров число выбранных методом проб и ошибок, чтобы убедиться, что растительным орнаментом всегда достигает верхней части экрана:


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

Первый `CCSprite` располагается, поэтому ее нижнего края соответствует позиции растительным орнаментом. Это делается путем присвоения свойству AnchorPoint `new CCPoint(.5f, 0)`. `AnchorPoint` Используется свойством *нормализовать координаты* какой диапазон от 0 до 1, независимо от размера `CCSprite`:

![](fruity-falls-images/image9.png "Свойство AnchorPoint использует нормализованный координаты какой диапазон от 0 до 1, независимо от размера CCSprite")

Последующие растительным орнаментом спрайтов размещаются в соответствии с `graphic.ContentSize.Height` значение, которое возвращает высоту изображения в пикселях. На следующей диаграмме показан как график растительным орнаментом вверх плитки:

![](fruity-falls-images/image10.png "Эта схема показывает, как график растительным орнаментом плитки вверх")

С момента начала координат `Vine` сущность является в нижней части растительным орнаментом, а затем разместив ее нетрудно, как показано в `Paddle.CreateVines` метод:


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

Экземпляры растительным орнаментом `Paddle` сущности также имеют интересные особенности поворота. Поскольку `Paddle` сущности в целом поворачивает согласно проигрывателя входных данных (который в настоящем руководстве описывается более подробно ниже), `Vine` экземпляры также затрагивает этот поворота. Однако поворот `Vine` экземпляров вместе с `Paddle` создает нежелательных визуальный эффект, как показано в следующем анимации:

![](fruity-falls-images/image11.gif "Однако поворот экземпляров растительным орнаментом вместе с ракетка порождает нежелательных visual эффект как показано в следующей анимации")

В противовес `Paddle` поворот, `leftVine` и `rightVine` поворачиваются экземпляров ракетка, как показано в противоположном направлении `Paddle.Activity` метод:


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

Обратите внимание, небольшой объем поворота добавляется к растительным орнаментом через `vineAngle` коэффициент. Это значение можно изменить для настройки того, насколько vines поворот.


## <a name="gamecoefficients"></a>GameCoefficients

Каждую игру хорошо — это совокупность итерации, поэтому приходится Fruity содержит класс с именем `GameCoefficients` определяет, каким образом игры. Этот класс содержит выразительный переменных, которые используются в игры для физических элементов управления, макет, запускающая и оценки.

Например физических фруктов, определяются следующие переменные:


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

Как следует из самих названий, эти переменные могут использоваться для настройки быстродействия фруктов возвращается, как горизонтальный перемещения замедляет работу со временем и амплитуду ракетка.

Игры коэффициенты, хранящиеся в коде или в данных файлах (например, XML) может быть особенно полезна в рабочие группы, игры конструкторы, не являющиеся также программистов.

`GameCoefficients` Класса также включают значения для включения отладочную информацию, например создать сведения или конфликтов областей:


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

В этом руководстве изучена приходится Fruity игры. Он рассматриваются основные понятия, включая содержимое, физических и управление состоянием игры.

## <a name="related-links"></a>Связанные ссылки

- [Документация по CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
