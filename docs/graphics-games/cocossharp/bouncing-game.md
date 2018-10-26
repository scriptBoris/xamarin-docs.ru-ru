---
title: Сведения об игре BouncingGame
description: Этот документ содержит пошаговое руководство для создания BouncingGame, созданных с помощью CocosSharp простую прыгающий шарик игру.
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 42155eb541849f365e7fab0a3d5c3ad583e760b7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109410"
---
# <a name="bouncinggame-details"></a>Сведения об игре BouncingGame

> [!TIP]
> Код для BouncingGame можно найти в [образцы Xamarin](https://developer.xamarin.com/samples/mobile/BouncingGame/). Лучше всего следовать указаниям в этом руководстве, скачайте и изучите код, как руководство ссылается на нее.

В этом пошаговом руководстве показано, как реализовать с помощью CocosSharp простую прыгающий шарик игру. 

 - Распаковка содержимого игр
 - Общие элементы visual CocosSharp
 - Визуальные элементы для добавления `GameLayer`
 - Реализация логики каждый кадр

Наши завершения игры будет выглядеть следующим образом:

![BouncingGame, завершения](bouncing-game-images/image1.png "BouncingGame, завершено")

## <a name="unzipping-game-content"></a>Распаковка содержимого игр

Разработчики игр часто используется термин *содержимого* для ссылки на файлы не кода, которые обычно создаются средой visual исполнители, игр конструкторы или аудио конструкторы. Распространенных типов контента включены файлы, используемые для отображения визуальных элементов, воспроизведение звука или управлять поведением искусственного интеллекта (ИИ). С точки зрения группой разработки игр содержимое обычно создается не программистами. Нашей игре включает в себя содержимое двух типов:

 - PNG-файлы для определения отображением шар и ракетка.
 - Файл единый xnb для определения шрифта, используемого этим отображение оценки (более подробно рассматривается при добавлении отображение показатель ниже)

Это содержимое, используемый здесь можно найти в [содержимого zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Нам потребуется эти файлы скачан и распакован в расположение, которое мы будет обращаться к далее в этом пошаговом руководстве.

## <a name="common-cocossharp-visual-elements"></a>Общие элементы visual CocosSharp

CocosSharp предоставляет ряд классов, используемый для отображения визуальных элементов. Некоторые элементы непосредственно видимы, а другие используются для организации. В игре, мы будем использовать следующее:

 - `CCNode` — Базовый класс для всех визуальных объектов в CocosSharp. `CCNode` Класс содержит `AddChild` функцию, которая может использоваться для построения иерархии родители потомки, называемый также *визуального дерева* или *граф сцены*. Приведенные ниже, все классы наследуют от `CCNode`.
 - `CCScene` — Корень визуальное дерево для всех CocosSharp игр. Все визуальные элементы должны быть частью визуального дерева с помощью `CCScene` в корне, или они не будут видны.
 - `CCLayer` — Контейнер для визуального объекты, такие как `CCSprite`. Как и предполагает название, `CCLayer` класса позволяет указать, каким образом визуальные элементы слоя друг над другом.
 - `CCSprite` Отображает изображение или его части изображения. `CCSprite` экземпляры могут размещаться, размер и предоставляют ряд визуальных эффектов.
 - `CCLabel` Отображает строку на экране. Шрифт, используемый `CCLabel` определяется в файле xnb. Мы обсудим xnbs более подробно ниже.

Чтобы понять, как используются различные типы будут рассмотрены один `CCSprite`. Необходимо добавить визуальные элементы `CCLayer`, и должен иметь визуальное дерево `CCScene` в качестве корня. Таким образом, родительских или дочерних отношений для одного `CCSprite` бы `CCScene`  >  `CCLayer`  >  `CCSprite`.

## <a name="adding-visual-elements-to-gamelayer"></a>Добавление визуальных элементов GameLayer

### <a name="adding-the-paddle-sprite"></a>Добавление спрайта ракетка

Изначально мы настроим игры фона на черный, а также добавить один `CCSprite` Подготовка к просмотру на экране. Изменить `GameLayer` добавляемый класс `CCSprite` следующим образом:

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

Приведенный выше код создает один `CCSprite` и добавляет его в качестве дочернего элемента `GameLayer`. `CCSprite` Конструктор позволяет определить файл изображения для использования в качестве строки. Наш код сообщает CocosSharp искомый файл с именем `paddle` (расширение указано, который мы обсудим далее в этом руководстве). CocosSharp будет искать любые имена файлов `paddle` в корневой папке содержимого (который является **содержимого**) а также все папки, добавляемые `gameView.ContentManager.SearchPaths` (описано в предыдущем разделе).

Мы добавим файлы непосредственно к корню **содержимого** папки для iOS и Android. Для этого щелкните правой кнопкой мыши или щелкните кнопкой мыши элемент управления- **содержимого** папки в проект iOS и выберите **добавить** > **добавить файлы...** Перейдите к которой мы распакован содержимое, ранее и выберите **paddle.png**. Если появится запрос о том, как добавить файл в папку, необходимо выбрать **копирования** параметр:

![Добавление файла к диалоговому окну папку](bouncing-game-images/image2.png "добавить файл в папку диалоговое окно")

Далее мы добавим файл в проект Android. Щелкните правой кнопкой мыши или щелчком элемента управления в папке Content (который находится в **активы** папку на проекты Android) и нажмите «выбрать» **добавить** > **добавить файлы...** . Перейдите на этот раз в iOS проект **содержимого** папки. При запросе о том, как добавить файл, выберите **связать** параметр:

![Добавление файла к диалоговому окну папку](bouncing-game-images/addalink.png "добавить файл в папку диалоговое окно")

Мы обсудим, почему файлы надо было в оба проекта ниже. Каждый проект **содержимого** папки теперь содержат **paddle.png** файла:

![Содержимое папки содержимого](bouncing-game-images/image3.png "содержимое содержимого папки")

Если мы выполним игры мы увидим `CCSprite` изображаемого:

![Ракетка, нарисованных на экране](bouncing-game-images/image4.png "ракетка, нарисованных на экране")

### <a name="file-details"></a>Сведения о файле

Итак, мы добавили один файл в проект, и процесс был довольно прост. Мы просто добавили **paddle.png** файл **содержимого** папки со ссылкой на него в коде. Давайте кратко рассмотрим некоторые соображения при работе с файлами в CocosSharp.

#### <a name="capitalization"></a>Регистр букв

Обратите внимание, что имя файла и строки, мы использовали в коде для доступа к файлу и нижнем регистре. Это обусловлено тем, некоторые платформы (например, Windows desktop и iOS simulator) зависят от регистра символов, хотя других платформ (таких как устройства iOS и Android) чувствительны к регистру. Мы будем использовать все строчные файлы для оставшейся части этого руководства, таким образом, чтобы файлы и код до тех пор кросс платформенных максимально.

#### <a name="extensions"></a>Расширения

Конструктор для создания спрайта не включает расширение «PNG» при обращении к файлу ракетка. Расширение файлов обычно опускается при работающих над проектами CocosSharp как расширения файлов для одного типа активов могут различаться в разных платформах. Например звуковых файлов может быть .aiff, .mp3 или .wma форматов файлов, в зависимости от платформы. Если оставить отключение расширения позволит использовать один и тот же код для работы на всех платформах, независимо от того, расширение файла.

#### <a name="content-in-platform-specific-projects"></a>Содержимое в проекты под конкретные платформы

В отличие от большинства файлов кода, которые могут находиться в PCL необходимо добавить файлы содержимого для каждого проекта под конкретную платформу. CocosSharp она требуется по двум причинам:

1. Каждая платформа имеет различные **действия при сборке**. Следует использовать содержимое, добавленное в проекты iOS **BundleResource** действие при сборке. Следует использовать содержимое, добавленное в проекты Android **AndroidAsset** действие при сборке.
2. Некоторые платформы требуют форматы файлов для конкретных платформ. Например файлы шрифтов .xnb различаются iOS и Android, как мы увидим далее в этом пошаговом руководстве.

Если формат файла не отличается от платформы (такие как .png), каждой платформы можно использовать тот же файл, где один или несколько платформ может связать файл из одного расположения.

## <a name="orientation"></a>Ориентация

Так же, как и любое другое приложение CocosSharp приложений можно запускать в книжной и альбомной ориентации. Мы скорректируем игры для запуска в режиме только для книжной ориентации. Во-первых мы изменим код решения в игре для обработки пропорции книжной ориентации. Чтобы сделать это, измените `width` и `height` значения в `LoadGame` метод в `ViewController` класс на устройствах iOS и `MainActivity` класс на устройствах Android:

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

Далее нам нужно будет изменить каждого проекта под конкретную платформу для запуска в книжной ориентации.

### <a name="ios-orientation"></a>Ориентация iOS

Чтобы изменить ориентацию проект iOS, выберите **Info.plist** файл **BouncingGame.iOS** проекта, а также изменить **сведения о развертывании iPhone или iPod** и **сведения о развертывании iPad** для включения только книжной ориентации:

![Параметры ориентации](bouncing-game-images/image5.png "параметры ориентации")

### <a name="android-orientation"></a>Ориентация на Android

Чтобы изменить ориентацию проект Android, откройте файл MainActivity.cs в проекте BouncingGame.Android. Измените определение атрибута действия, поэтому он определяет только книжную ориентацию, следующим образом:

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>Система координат по умолчанию

Наш код, который создает экземпляр `CCSprite`, задает `PositionX` и `PositionY` значения до 100. По умолчанию, это означает, что `CCSprite` center будет находиться на 100 пикселей, вверх и вправо относительно левой нижней части экрана. Система координат можно изменить путем присоединения `CCCamera` для `CCLayer`. Мы не будет работать с `CCCamera` в этот проект, но Дополнительные сведения о `CCCamera` можно найти в [документация по CocosSharp API](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

100 точек, упомянутых выше «игры» пикселей, в отличие от пикселей на оборудовании. Это означает, что выполнение же игры на устройстве на другое разрешение (таких как iPad и iPhone) приведет к объектов расположения и размера правильно относительно физического экрана. В частности, игры видимой области всегда будет 1024 пикселей и 768 пикселей в ширину, так как это разрешение, указанный ранее в `LoadGame` метод.

## <a name="adding-the-ball-sprite"></a>Добавление спрайта шара

Теперь, когда мы знакомы с основами работы с `CCSprite`, мы добавим второй `CCSprite` — шар. Мы будем выполните действия, которые очень похожи, как мы создали ракетка `CCSprite`. 

Во-первых, мы добавим **ball.png** образ из распакованной папке в проект iOS **содержимого** папки. Выберите, чтобы **копирования** файл **содержимого** каталога. Выполните те же действия, как описано выше, чтобы добавить ссылку на **ball.png** файл в проект Android.

Далее создайте `CCSprite` Этот шар, добавив член вызывается `ballSprite` для `GameScene` класса, а также код создания экземпляра для `ballSprite`. После завершения `GameLayer` класса будет выглядеть следующим образом:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>Добавление метки оценки

Последний элемент visual, мы добавим в игру `CCLabel` для отображения, сколько раз пользователь успешно удерживали шар. `CCLabel` Для отображения строк на экране используются шрифта, указанный в конструкторе.

Добавьте следующий код для создания `CCLabel` в экземпляре `GameLayer`. После завершения работы, код должен выглядеть следующим образом:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

Обратите внимание, что использование scoreLabel `AnchorPoint` из `CCPoint.AnchorUpperLeft`, означающее, что `PositionX` и `PositionY` значения определяют его положение верхнего левого. Это позволяет нам позиции `scoreLabel` относительно левого верхнего угла экрана без учета измерений метки.

## <a name="implementing-every-frame-logic"></a>Реализация логики каждый кадр

На данный момент игра представляет статический сцены. Мы добавим логику для управления движением объекты на сцене, добавив код, который обновляет положение объектов с высокой частотой. В этом случае код будет выполняться в 60 раз в секунду — также называется обновления шестьдесят *кадров* в секунду (Если оборудование не поддерживает обновление это часто). В частности мы добавим логики для корректного делятся и возврата к ракетка, для перемещения ракетки в соответствии с входных данных и обновлять баллы игрока при каждом шар царить ракетка шар.

`Schedule` Метод, предоставляемый `CCNode` класса, позволит нам добавить логику каждый кадр в игре. Мы добавим код после `// New code` GameLayer конструктору:

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

Теперь создайте `RunGameLogic` метод в `GameLayer` класс, который размещается вся логика каждого кадра:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

Параметр float определяет, сколько кадр задается в секундах. Мы будем использовать это значение при реализации перемещения мяча.

### <a name="making-the-ball-fall"></a>Что делает делятся шар

Мы можем сделать делятся либо вручную реализации тяжести кодом или с помощью встроенных функций программы Box2D в CocosSharp шар. Механизм моделирования физики программы Box2D, позволяющих CocosSharp игры. Он очень широкие возможности и эффективной, но требует написания кода установки. Так как физическое моделирование достаточно прост, здесь он будет реализован вручную.

Для реализации gravity нам потребуется для хранения текущего X и Y-скорость шара. Мы добавим два члена к `GameLayer` класса:

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

Далее мы можем реализовать логику передачи управления в `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>Перемещение ракетка с сенсорного ввода

Теперь, когда шар падает, мы добавим перемещение по горизонтали ракетка с помощью `CCEventListenerTouchAllAtOnce` объекта. Этот объект предоставляет ряд событий. В этом случае мы хотим получать уведомления, если переместить все сенсорные точки, можно настроить положение ракетка. `GameLayer` Уже создает `CCEventListenerTouchAllAtOnce`, поэтому нам просто нужно назначить `OnTouchesMoved` делегировать. Чтобы сделать это, измените метод AddedToScene следующим образом:

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

Затем мы реализуем `HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>Реализация шар конфликтов

Если мы запустить игру, теперь мы Обратите внимание, что шар падает через ракетка. Мы реализуем *конфликт* (логики для реагирования на перекрывающиеся игровым объектам) в коде каждый кадр. Так как перемещение объектов замена помещает каждый кадр, проверка конфликтов обычно является также выполняется каждый кадр. Мы также добавим скорость по оси X при шар ракеткой добавьте в игру, но это означает, что нам нужно удержания шара в перемещении за границы экрана. Мы сделаем это `RunGameLogic` кода после применения скорости для `ballSprite` после `// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>Добавление оценки

Теперь, когда игра воспроизведения, последним шагом является Добавление логики для оценки. Во-первых, мы добавим элемент оценка GameLayer класс с именем `score`:

```csharp
int score;
```

Мы будем использовать эту переменную для отслеживания баллы игрока и отображать его с помощью `scoreLabel`. Для этого добавьте следующий код в инструкции if в `RunGameLogic` при перекрытии шар и ракетка:

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

Теперь можно запустить игру и см. в разделе, что игра отображает оценку, увеличит как шар отскакивает от ракетка:

![Завершенные игры с увеличивающимся оценкой](bouncing-game-images/image1.png "завершенного игры с увеличивающимся оценкой")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве представлены создание кросс платформенной игры с графикой, физики и входные данные с помощью CocosSharp. Он является первым шагом при начале работы с CocosSharp разработки игр. Мы рассмотрели некоторые из наиболее распространенных классов в CocosSharp, как создать визуальное дерево, поэтому объекты визуализировано надлежащим образом и как реализовать логику игры каждого кадра.

В этом пошаговом руководстве рассматривается только небольшая часть предлагает CocosSharp игровой подсистемы. Сведения и пошаговые руководства на другие темы CocosSharp, см. в разделе [остальной части руководства CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Связанные ссылки

- [Завершенные игры (пример)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Игр (пример)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
