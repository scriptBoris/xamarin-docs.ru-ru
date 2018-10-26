---
title: iOS API-интерфейсы игр в Xamarin.iOS
description: В этой статье рассматриваются новые усовершенствования игры, в iOS 9, который может использоваться для улучшения графических и звуковых функций игры Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116008"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>iOS API-интерфейсы игр в Xamarin.iOS

_В этой статье рассматриваются новые усовершенствования игры, в iOS 9, который может использоваться для улучшения графических и звуковых функций игры Xamarin.iOS._

Apple были усовершенствованы некоторые технологические до игр интерфейсов API в iOS 9, которые упрощают процесс для реализации игровой графики и звука в приложении Xamarin.iOS.
К ним относятся удобство разработки до высокоуровневых платформах и потенциала GPU на устройстве iOS, быстрым и графические возможности.

[![](images/flocking01.png "Пример приложения, запущенные или косяк")](images/flocking01.png#lightbox)

Сюда входят GameplayKit, ReplayKit, модель ввода-вывода, MetalKit и шейдеры производительности без операционной системы, а также добавлены новые расширенные функции операционной системы, SceneKit и SpriteKit.

В этой статье будут представлены все способы повышения Xamarin.iOS игры благодаря новым улучшениям игр iOS 9:

## <a name="introducing-gameplaykit"></a>Знакомство с GameplayKit

Новая платформа GameplayKit Apple предоставляет набор технологий, позволяет легко создавать игры для устройств iOS за счет сокращения повторяющихся, общий код, необходимый для реализации. GameplayKit предоставляет средства для разработки игровая Механика, в который можно легко объединить с графический процессор (например SceneKit или SpriteKit), чтобы быстро предоставлять завершения игры.

GameplayKit включает несколько распространенных, игра воспроизводить алгоритмы, такие как:

- Поведение в зависимости, моделирование агента, которое позволяет определить перемещений и цели, искусственного Интеллекта буду использовать автоматически.
- Minmax искусственного интеллекта для поочередного игры.
- Система правило для управляемых данными логику игры с помощью нечеткого обоснование для обеспечения поведения кризиса.

Кроме того GameplayKit использует подход стандартных блоков для разработки игр с помощью модульную архитектуру, которая предоставляет следующие возможности:

- Конечный автомат для обработки сложных, процедурного кода на основе систем в игры.
- Средства для предоставления в случайном порядке игры и непредсказуемые результаты без возникновения проблемы при отладке.
- Архитектуру с возможностью повторного использования, разделенной на основе сущности.

Дополнительные сведения о GameplayKit, см. в разделе Apple [руководство по программированию Gameplaykit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) и [ссылка на платформу GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Примеры GameplayKit

Давайте кратко рассмотрим реализации некоторые механизмы простой игры в приложении Xamarin.iOS с помощью пакета игры.

### <a name="pathfinding"></a>Pathfinding

Pathfinding является возможность работы с искусственным Интеллектом элемента игры найдут свое применение вокруг игровой доски.
Например, 2D злоумышленником, поиск образом через Лабиринт или 3D символ через ландшафта первого лица кораблей мире.

Рассмотрим следующие сопоставления:

[![](images/gkpathfindpath.png "Пример сопоставления pathfinding")](images/gkpathfindpath.png#lightbox)

С помощью pathfinding это C# кода можно найти способ через схему:

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Классические экспертная система

Следующий фрагмент C# кода показано, как GameplayKit может использоваться для реализации классические экспертная система:

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

На основе заданного набора правил (`GKRule`) и известных набор входных данных, экспертная система (`GKRuleSystem`) создаст прогнозируемые выходные данные (`fizzbuzz` для нашего примера выше).

### <a name="flocking"></a>Или косяк

Или косяк позволяет сущностей game вести себя как flock, где группы отвечает на движения и действия сущности интерес как flock птицы рейсов или школе fish swimming управляемым группу искусственного интеллекта.

Следующий фрагмент C# код реализует поведение или косяк, с помощью GameplayKit и SpriteKit для отображения графики:

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

Далее необходимо реализуйте этой сценой в контроллер представления:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

При запуске, немного анимированное _«Boids»_ будет flock вокруг наших касания пальцем:

[![](images/flocking01.png "Немного анимированных Boids будет flock вокруг касания пальцем")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Другие примеры Apple

В дополнение к примерам, представленные выше, Apple предоставляет следующие примеры приложений, которые могут быть перекодированные для C# и Xamarin.iOS:

- [FourInARow: С помощью стратегий GameplayKit Minmax для сопернику искусственного Интеллекта](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: В системе агентов в GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Разработка кроссплатформенного игры с помощью SpriteKit и GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

В iOS 9 Apple сделала некоторые изменения и дополнения для исходного состояния системы для обеспечения доступа с низкими издержками в GPU. С помощью исходного состояния системы можно увеличить, графики и вычислительных возможностей приложения iOS.

Платформа исходного состояния системы включает следующие новые функции:

- Новый частный и глубина трафарета текстур для OS X.
- Улучшенная тени качества с помощью глубины граница и отдельные передней и задней трафарета значения.
- Metal улучшения заливки языка и стандартной библиотеки без операционной системы.
- Вычислительные шейдеры поддерживают более широкий набор форматов пикселей.

### <a name="the-metalkit-framework"></a>Платформа MetalKit

MetalKit framework предоставляет набор вспомогательных классов и функций, сократить объем работ, необходимый для использования операционной системы в приложении iOS. MetalKit обеспечивает поддержку в трех ключевых областях:

1. Загрузка из различных источников, включая распространенные форматы, такие как PNG, JPEG, KTX и PVR асинхронной текстуры.
2. Удобный доступ к модели операций ввода-вывода на основе ресурсов для обработки исходного состояния системы конкретной модели. Эти функции высокой оптимизированы для обеспечения эффективной передачи данных между сетки модели операций ввода-вывода и буферы без операционной системы.
3. Предопределенные представления исходного состояния системы и представления управления, значительно сократить объем кода, необходимого для отображения графических визуализаций в приложения iOS.

Дополнительные сведения о MetalKit, см. в разделе Apple [ссылка на платформу MetalKit](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [руководство по программированию без операционной системы](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [ссылка на платформу операционной системы](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) и [исходного состояния системы Руководство по языку заливки](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Шейдеры Framework производительности без операционной системы

Платформа шейдера производительности без операционной системы представлены оптимизирован графики и вычислений на основе построители текстуры для использования в вашей операционной системы на основе приложений iOS. Каждого построителя текстуры в шейдере производительности без операционной системы, framework специально настроен для предоставления высокой производительности от операционной системы поддерживается iOS графических процессоров.

С помощью классов шейдера производительности без операционной системы, можно добиться без необходимости выбирать и ведение отдельных кодовыми максимальную производительность, возможную на каждого конкретного iOS GPU. Metal шейдеры производительности можно использовать с любым ресурсом без операционной системы, таких как текстуры и буферы.

Платформа шейдера производительности без операционной системы предоставляет набор общих шейдеров, такие как:

- **Гауссова размытия** (`MPSImageGaussianBlur`)
- **Определение границ Sobel** (`MPSImageSobel`)
- **Изображение гистограммы** (`MPSImageHistogram`)

Дополнительные сведения см. в разделе Apple [руководство по языку заливки без операционной системы](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Введение в модель ввода-вывода

Модель операций ввода-вывода платформы Apple предоставляет глубокое понимание трехмерных ресурсов (например, модели и соответствующие ресурсы). Модель операций ввода-вывода обеспечивает игры на основе физического оборудования материалов, моделей и освещения, который может использоваться с GameplayKit, исходного состояния системы и SceneKit iOS.

С помощью модели операций ввода-вывода может поддерживать следующие типы задач:

- Импорт освещения, материалов, сетка данных, параметров камеры и другие сведения с учетом сцены из различных популярных программного обеспечения и форматы игровое ядро.
- Процесс или сформировать сведения с учетом сцены, таких как создание процедурному текстурой sky domes или создание образа освещения в сетку.
- Работает с MetalKit, SceneKit и GLKit для эффективной загрузки ресурсов игры в буферы GPU для подготовки к просмотру.
- Экспортируйте сведения с учетом сцены в различные популярные программного обеспечения и форматы игровое ядро.

Дополнительные сведения о модели операций ввода-вывода, см. в разделе Apple [ссылка на платформу модель ввода-вывода](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Знакомство с ReplayKit

Новая платформа ReplayKit компании Apple позволяет легко добавить запись игры в игру iOS и пользователи могут быстро и легко изменить и поделиться этим видео из в приложении.

Дополнительные сведения см. в разделе Apple [будет социальных сетей, с помощью видео ReplayKit и Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) и их [DemoBots: создание кросс-платформенной игры с помощью SpriteKit и GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) примера приложения.

## <a name="scenekit"></a>SceneKit

Scene Kit — это граф сцены API, который упрощает работу с трехмерной графикой. Он впервые появилась в OS X 10.8 и теперь пришло iOS 8. С помощью комплекта сцены создавать впечатляющие трехмерные визуализации и случайного трехмерных игр не требуется опыт в OpenGL. Основываясь на основные понятия, связанные с граф сцены, Scene Kit упрощает OpenGL и OpenGL ES, упрощая добавление трехмерного содержимого в приложение. Тем не менее если вы являетесь экспертом OpenGL, Scene Kit имеет хорошую поддержку привязки непосредственно с также OpenGL. Он также включает в себя множество функций, которые дополняют 3D-графики, таких как физики и очень хорошо интегрируется с несколько других платформ Apple, например, Core Animation, Core образа и Sprite Kit.

Дополнительные сведения см. в разделе наших [SceneKit](~/ios/platform/gaming/scenekit.md) документации.

### <a name="scenekit-changes"></a>Изменения SceneKit

Для iOS 9 Apple добавил следующие новые функции для SceneKit:

- Xcode теперь предоставляет редактор сцены, который позволяет быстро создавать игры и интерактивные трехмерные приложения, изменив кадром непосредственно из Xcode.
- `SCNView` И `SCNSceneRenderer` классы могут использоваться для включения состояния системы подготовки отчетов (на устройствах iOS, поддерживаемые).
- `SCNAudioPlayer` И `SCNNode` классы можно использовать для добавления пространственных звуковые эффекты, которые автоматически отслеживать положение исполнителя в приложение iOS.

Дополнительные сведения см. в разделе наших [документации SceneKit](~/ios/platform/introduction-to-ios8.md#scenekit) и Apple [ссылка на платформу SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) и [Fox: разработка игры с помощью Xcode сцены редактораSceneKit](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)пример проекта.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, двухмерных игр платформы от Apple, имеет ряд интересных функций в iOS 8 и OS X Yosemite. К ним относятся интеграция с Scene Kit, поддержка построителя текстуры, освещения, теней, ограничения, создания карты нормалей и физики усовершенствования. В частности новые функции физики сделать очень легко добавить реалистичные эффекты для игры.

Дополнительные сведения см. в разделе наших [SpriteKit](~/ios/platform/gaming/spritekit.md) документации.

### <a name="spritekit-changes"></a>Изменения SpriteKit

Для iOS 9 Apple добавил следующие новые функции для SpriteKit:

- Пространственные аудио эффект, который автоматически отслеживать положение игрока с `SKAudioNode` класса.
- Xcode теперь содержит сцены редактор и редактор действий для упрощенного создания двухмерных игр и приложений.
- Просто прокрутка поддержки игры с новыми узлами камеры (`SKCameraNode`) объектов.
- На устройствах iOS, поддерживающих исходного состояния системы SpriteKit будут автоматически использовать его для подготовки к просмотру, даже если вы уже использовали пользовательские шейдеров OpenGL ES.

Дополнительные сведения см. в разделе наших [документации SpriteKit](~/ios/platform/introduction-to-ios8.md#spritekit) Apple [ссылка на платформу SpriteKit](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) и их [DemoBots: создание кросс-платформенной игры с помощью SpriteKit и GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) примера приложения.

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается новый игровые функции, iOS 9 предоставляет для приложений Xamarin.iOS.
Здесь описана концепция GameplayKit и модель ввода-вывода; основные усовершенствования для операционной системы; и новые функции SceneKit и SpriteKit.



## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 для разработчиков](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
