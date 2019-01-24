---
title: Часть 2 – реализация WalkingGame
description: В этом пошаговом руководстве показано, как добавить логику игры и содержимое для пустого проекта MonoGame создать демонстрационную версию анимированных спрайта, перемещение с помощью сенсорного ввода.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 941b88f9109cf2f3a3485311c52b1250bd08e53f
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479775"
---
# <a name="part-2--implementing-the-walkinggame"></a>Часть 2 – реализация WalkingGame

_В этом пошаговом руководстве показано, как добавить логику игры и содержимое для пустого проекта MonoGame создать демонстрационную версию анимированных спрайта, перемещение с помощью сенсорного ввода._

Предыдущих частях в этом пошаговом руководстве было рассмотрено создание пустых проектов MonoGame. Мы возьмем за основу эти предыдущей части, сделав простую демонстрацию игр. В этом разделе содержатся следующие подразделы:

- Распаковке наши материалы и игры
- Общие сведения о классе MonoGame
- Подготовка к просмотру наш первый спрайт
- Создание CharacterEntity
- Добавление CharacterEntity игры
- Создание класса анимации
- Добавление первой анимации CharacterEntity
- Добавление перемещения на символ
- Сопоставление, перемещения и анимации


## <a name="unzipping-our-game-content"></a>Распаковке наши материалы и игры

Прежде чем начать написание кода, нужно распаковать нашей игре *содержимого*. Разработчики игр часто используется термин *содержимого* для ссылки на файлы не кода, которые обычно создаются средой visual исполнители, игр конструкторы или аудио конструкторы. Распространенных типов контента включены файлы, используемые для отображения визуальных элементов, воспроизведение звука или управлять поведением искусственного интеллекта (ИИ). От разработки игр содержимое точки зрения команды обычно создается не программистами.

Содержимое, используемое здесь можно найти [на сайте github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Нам потребуется эти файлы, скачиваемые на расположение, которое мы будет обращаться к далее в этом пошаговом руководстве.

## <a name="monogame-class-overview"></a>Общие сведения о классе MonoGame

Для начала мы изучим MonoGame классы, используемые в основных визуализации:

- `SpriteBatch` — используется для рисования двумерной графики на экране. *Спрайтами* являются 2D визуальные элементы, которые используются для отображения изображений на экране. `SpriteBatch` Объекта можно нарисовать один спрайт во время между его `Begin` и `End` методов или несколько спрайтов могут быть сгруппированы вместе, или *пакетные*.
- `Texture2D` — Представляет объект изображения во время выполнения. `Texture2D` экземпляры часто создаются из форматов файлов, например .png или .bmp, несмотря на то, что они также могут создаваться динамически во время выполнения. `Texture2D` экземпляры используются при подготовке к просмотру с `SpriteBatch` экземпляров.
- `Vector2` — представляет позицию в двумерной системе координат, которая часто используется для размещения визуальных объектов. Также включает MonoGame `Vector3` и `Vector4` , но мы будем использовать `Vector2` в этом пошаговом руководстве.
- `Rectangle` — область четырехсторонняя с положение, ширину и высоту. Мы будем использовать это для определения, какая часть нашей `Texture2D` для подготовки к просмотру, когда начнем работу с анимацией.

Также следует отметить, что MonoGame не поддерживается корпорацией Майкрософт – несмотря на его пространство имен. `Microsoft.Xna` Пространство имен используется в MonoGame, чтобы упростить перенос существующих проектов XNA в MonoGame.

## <a name="rendering-our-first-sprite"></a>Подготовка к просмотру наш первый спрайт

Далее мы будем привлекать единый спрайт на экране, чтобы показать, как выполнять Двухмерная отрисовка в MonoGame.

### <a name="creating-a-texture2d"></a>Создание Texture2D

Нам необходимо создать `Texture2D` экземпляр должен использоваться при отрисовке наших спрайт. Все игры в конечном счете содержимое в папку с именем **содержимое,** в проекте под конкретную платформу. MonoGame общих проектов не может содержать содержимое, как содержимое необходимо использовать действия построения, относящееся к платформе. Разработчики CocosSharp будет найти папку содержимого знакомые концепции — они находятся в той же позиции в проектах CocosSharp и MonoGame. Папку можно найти в проекте iOS, а также в папку Assets в проекте Android.

Чтобы добавить содержимое в нашей игре, щелкните правой кнопкой мыши **содержимого** папку и выберите **Добавить > Добавить файлы...** Перейдите в расположение, в которую было извлечено content.zip файл и выберите **charactersheet.png** файла. Если появится запрос о том, как добавить файл в папку, необходимо выбрать **копирования** параметр:

![](part2-images/image1.png "Если появится запрос о том, как добавить файл в папку, выберите параметр копирования")

Содержимое папки теперь содержит файл charactersheet.png:

![](part2-images/image2.png "Содержимое папки теперь содержит файл charactersheet.png")

Далее мы добавим код для загрузки файла charactersheet.png и создания `Texture2D`. Для этого откройте `Game1.cs` файл и добавьте следующее поле в класс Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Далее мы создадим `characterSheetTexture` в `LoadContent` метод. До внесения изменений `LoadContent` метод выглядит следующим образом:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Следует отметить, что проект по умолчанию уже создает `spriteBatch` экземпляр для нас. Мы будем использовать это позже, но мы не будет добавлять дополнительный код для подготовки `spriteBatch` для использования. С другой стороны наши `spriteSheetTexture` требует инициализации, поэтому мы будет инициализировать его после `spriteBatch` создается:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Теперь, когда у нас есть `SpriteBatch` экземпляра и `Texture2D` экземпляра, мы можем добавить наш код отрисовки для `Game1.Draw` метод:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Выполнение игры теперь показывает один спрайт отображение текстуры, созданного из charactersheet.png:

![](part2-images/image3.png "Выполнение игры теперь показывает один спрайт отображение текстуры, созданного из charactersheet.png")

## <a name="creating-the-characterentity"></a>Создание CharacterEntity

До сих пор мы добавили код для отображения одного спрайт на экран. Тем не менее если мы разработке полной версии игры без создания любые другие классы, мы сталкивались с проблемы организации кода.

### <a name="what-is-an-entity"></a>Что такое сущности?

Распространенный подход для организации кода игры — создать новый класс для каждой игры *сущности* типа. Сущности в разработке игр — это объект, который может содержать некоторые следующие характеристики (не все являются обязательными):

- Визуальный элемент, например спрайта, текст или трехмерной модели
- Физика или каждый кадр поведении, таком как единое patrolling путь набора или символа проигрывателя, отвечать на запросы на ввод
- Может быть динамически создаваться и уничтожаться, такие как появление питания и собираются для проигрывателя

Систем организации сущности могут быть сложными и множество игровых движков предлагают классах, помогающих управлять сущностями. Нам надо будет реализовать систему очень простой сущности, поэтому стоит отметить, что полный игры требуют дополнительные организации со стороны разработчика.


### <a name="defining-the-characterentity"></a>Определение CharacterEntity

Наши сущности, которые мы будем называть `CharacterEntity`, будет иметь следующие характеристики:

- Возможность загружать собственные `Texture2D`
- Возможности для своей отрисовки, включая содержащий вызова методов для обновления анализирующего анимации
- `X` и Y свойства, управляющие позицию символа.
- Возможности для самообновления — в частности, для чтения значения из сенсорный экран и соответствующим образом изменить положение.

Чтобы добавить `CharacterEntity` к нашей игре, щелкните правой кнопкой мыши или элемент управления, щелкните **WalkingGame** проекта и выберите **Добавить > новый файл...** . Выберите **пустой класс** параметр и назовите новый файл **CharacterEntity**, затем нажмите кнопку **New**.

Сначала мы добавим возможность `CharacterEntity` загрузить `Texture2D` а также для рисования самого себя. Мы будем изменять добавленный `CharacterEntity.cs` файл следующим образом:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

Приведенный выше код добавляет ответственность за позиционирования, отрисовки и загрузке содержимого `CharacterEntity`. Давайте немного, чтобы отметить некоторые вопросы, связанные в приведенном выше коде.

### <a name="why-are-x-and-y-floats"></a>Почему являются X и Y значения с плавающей запятой?

Разработчиков, незнакомых с программирование игр может возникнуть вопрос, почему `float` используется тип, в отличие от `int` или `double`. Причина в том, что чаще всего для позиционирования в коде низкого уровня подготовки отчетов происходит 32-разрядное значение. Тип double занимает 64 бит точности, которое редко требуется для размещения объектов. 32-разрядная версия разница может показаться незначащие, многие современные игры включают графики, требующий обработки десятков тысяч значения положения каждого кадра (30 или 60 раз в секунду). Сокращая объем памяти, который необходимо переместить через графический конвейер в два раза может иметь значительное влияние на производительность игр.

`int` Тип данных может быть соответствующие единица измерения для позиционирования, но его можно поместить ограничения на его пути, располагаются сущностей. Например, с помощью целочисленных значений усложняет для реализации smooth перемещения для игр, которые поддерживают увеличение масштаба или трехмерной камеры (которые допустимы по `SpriteBatch`).

Наконец мы увидим, что логика, которая перемещает наших символ по экрану будет сделать с помощью значения времени игры. Результат умножения скорости, сколько времени прошло через заданный интервал редко приведет целым числом, поэтому нам нужно использовать `float` для `X` и `Y`.

### <a name="why-is-charactersheettexture-static"></a>Почему не characterSheetTexture статический?

`characterSheetTexture` `Texture2D` Экземпляр является представление времени выполнения charactersheet.png файла. Другими словами, он содержит значения цвета для каждого пикселя, извлеченное из источника **charactersheet.png** файла. Если создать два нашей игре `CharacterEntity` экземпляров, а затем для каждого из них требуется доступ к информации из charactersheet.png. В этой ситуации мы не хотим повлечь снижение производительности при загрузке charactersheet.png дважды, а также бы нам нужна память повторяющиеся текстур, хранящихся в оперативной памяти. С помощью `static` позволяет иметь одинаковый `Texture2D` во всех `CharacterEntity` экземпляров.

С помощью `static` члены для среды выполнения представления содержимого — это упрощенный решение, и здесь не рассматриваются проблемы, возникшие при больших игры, например выгрузка содержимого при переходе из одного уровня на другой. Более сложные решения, которые выходят за рамки этого пошагового руководства, включают использование конвейера содержимого MonoGame или создание системы управления содержимым.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Почему такое SpriteBatch передается как аргумент Instead of создаются сущности?

`Draw` Метод реализованное выше принимает `SpriteBatch` аргумент, но в отличие от этого `characterSheetTexture` создается `CharacterEntity`.

Причина этого — так как наиболее эффективный отрисовку возможен, когда же `SpriteBatch` экземпляра используется для всех `Draw` вызовов и когда все `Draw` вызовы выполняются в одном наборе `Begin` и `End` вызовов. Само собой, нашей игре будет содержать только единственный экземпляр сущности, но более сложных игр выгодно использовать шаблон, который позволяет нескольким сущностям, чтобы использовать тот же `SpriteBatch` экземпляра.


## <a name="adding-characterentity-to-the-game"></a>Добавление CharacterEntity игры

Теперь, когда мы добавили наших `CharacterEntity` с кодом самовычисление мы заменим код в `Game1.cs` для использования экземпляра новой сущности. Для этого мы заменим `Texture2D` с `CharacterEntity` в `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Далее мы добавим экземпляра этой сущности в `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Необходимо также удалить `Texture2D` создания из `LoadContent` , так как теперь, обрабатывается внутри нашей `CharacterEntity`. `Game1.LoadContent` должна выглядеть следующим образом:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Стоит отметить, что несмотря на свое название `LoadContent` метод не является единственным местом, где можно загрузить содержимое — множество игр реализовать содержимого загрузки в их метод Initialize, или даже в своем коде обновления, как содержимое, не будет требоваться пока не достигнет проигрывателя создавались игры.

Наконец мы можно изменить метод Draw следующим образом:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Если запустить игру, мы увидим символ. Так как X и Y по умолчанию 0, символ занимает позицию по левом верхнем углу экрана:

![](part2-images/image4.png "Так как X и Y по умолчанию 0, символ занимает позицию по левом верхнем углу экрана")

## <a name="creating-the-animation-class"></a>Создание класса анимации

В настоящее время наши `CharacterEntity` отображает полный **charactersheet.png** файла. Такой подход несколько образов в одном файле называется *лист спрайт*. Как правило спрайт будут отображаться только часть листа спрайт. Мы будем изменять `CharacterEntity` для подготовки к просмотру часть это **charactersheet.png**, и эта часть будет изменяться со временем для отображения анализирующего анимации.

Мы создадим `Animation` класс для контроля состояния CharacterEntity анимации и логика. Класс анимации будет общим классом, который может использоваться для любой сущности, не только `CharacterEntity` анимации. Ultimate `Animation` класс будет предоставлять `Rectangle` которого `CharacterEntity` будет использовать для рисования самого себя. Мы также создадим `AnimationFrame` класс, который будет использоваться в определении анимации.


### <a name="defining-animationframe"></a>Определение AnimationFrame

`AnimationFrame` не будет содержать любую логику, относящиеся к анимации. Мы будем использовать его только для хранения данных. Чтобы добавить `AnimationFrame` класса, щелкните правой кнопкой мыши или щелчком элемента управления на **WalkingGame** общего проекта и выберите **Добавить > новый файл...** Введите имя **AnimationFrame** и нажмите кнопку **New** кнопки. Мы изменим `AnimationFrame.cs` файл, содержащий следующий код:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame` Класс содержит два элемента информации:

- `SourceRectangle` — Определяет область `Texture2D` отображаемые с `AnimationFrame`. Это значение измеряется в пикселях с верхней левой, (0, 0).
- `Duration` — Определяет продолжительность `AnimationFrame` отображается при использовании в `Animation`.

### <a name="defining-animation"></a>Определение анимации

`Animation` Класс будет содержать `List<AnimationFrame>` а также логику для переключения рамка отображается в текущий момент в соответствии с, сколько времени прошло.

Чтобы добавить `Animation` класса, щелкните правой кнопкой мыши или щелчком элемента управления на **WalkingGame** общего проекта и выберите **Добавить > новый файл...** . Введите имя **анимации** и нажмите кнопку **New** кнопки. Мы изменим `Animation.cs` файл, поэтому оно содержит следующий код:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Давайте взглянем на некоторые сведения из `Animation` класса.

### <a name="frames-list"></a>Список кадров

`frames` Член является то, что хранит данные для анимации. Будет добавлен код, который создает экземпляр анимации `AnimationFrame` экземпляры `frames` списке через `AddFrame` метод. Более полная реализация может предлагать `public` методов или свойств для изменения `frames`, но мы будем ограничивать его функциональные возможности, добавлению кадров в этом пошаговом руководстве.

### <a name="duration"></a>Длительность

Возвращает общую продолжительность `Animation,` которое было получено путем добавления длительности всех вложенных `AnimationFrame` экземпляров. Это значение можно кэшировать, если `AnimationFrame` были постоянный объект, но поскольку мы реализовали AnimationFrame как класс, который может быть изменен после добавления анимации, нам нужно для вычисления этого значения при каждом обращении к свойству.

### <a name="update"></a>Обновление

`Update` Метод должен вызываться при каждом кадре (то есть каждый раз обновляется весь игры). Его предназначение — повысить `timeIntoAnimation` член, который используется для возврата отображаемого кадра. Логика в `Update` предотвращает `timeIntoAnimation` никогда больше не больше, чем продолжительность всей анимации.

Так как мы будем использовать `Animation` для отображения анализирующего анимации, то требуется, чтобы эта анимация повторяются, если он уже истек. Это можно сделать, проверив, если `timeIntoAnimation` больше, чем `Duration` значение. Если это так он к началу цикла и сохранить остаток, в цикла анимации.

### <a name="obtaining-the-current-frames-rectangle"></a>Получение текущего кадра прямоугольника

Цель `Animation` класса, в конечном счете, заключается в предоставлении `Rectangle` который может использоваться при рисовании спрайт. В настоящее время `Animation` класс содержит логику для изменения `timeIntoAnimation` член, которая будет использоваться для получения которого `Rectangle` должны отображаться. Мы сделаем это, создав `CurrentRectangle` свойство в `Animation` класса. Скопируйте это свойство в `Animation` класса:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Добавление первой анимации CharacterEntity

`CharacterEntity` Будет содержать анимации для просмотра и положение, а также ссылку на текущий `Animation` отображение.

Во-первых, мы добавим нашей первой `Animation,` которой мы будем использовать, чтобы протестировать функциональные возможности, как записаны на данный момент. Добавим к классу CharacterEntity следующие члены:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Далее определим `walkDown` анимации. Измените `CharacterEntity` конструктор следующим образом:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Как упоминалось ранее, необходимо вызвать `Animation.Update` для воспроизведение анимации на основе времени. Нам также нужно назначить `currentAnimation`. Для теперь мы назначим `currentAnimation` для `walkDown`, но мы будем заменять этот код позже, когда мы реализуем логику перемещения. Мы добавим `Update` метод `CharacterEntity` следующим образом:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Теперь, когда у нас есть `currentAnimation` , назначен и обновлен, его можно использовать для выполнения наших операций рисования. Мы изменим `CharacterEntity.Draw` следующим образом:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

Последний шаг к `CharacterEntity` анимации является вызов только что добавленного `Update` метода из `Game1`. Изменить `Game1.Update` следующим образом:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Теперь `CharacterEntity` воспроизводит его `walkDown` анимации:

![](part2-images/image5.gif "Теперь CharacterEntity будет воспроизводиться анимация walkDown")

## <a name="adding-movement-to-the-character"></a>Добавление перемещения на символ

Далее мы добавим перемещения для наших символов, с помощью сенсорных элементов управления. Когда пользователь касается экрана, символ будет перейти к точке, где коснулись экрана. При обнаружении не штрихи, а затем обозначает символ на месте.

### <a name="defining-getdesiredvelocityfrominput"></a>Defining GetDesiredVelocityFromInput

Мы будем использовать его MonoGame `TouchPanel` класс, который предоставляет сведения о текущем состоянии сенсорный экран. Давайте добавим метод, согласно которому будет проверяться `TouchPanel` и возвращать нужные скорость наших символ:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState` Возвращает метод `TouchCollection` которой содержатся сведения о которой пользователь касается экрана. Если пользователь не касается экрана, а затем `TouchCollection` будет пустым, в этом случае не следует переместить знак.

Если пользователь касается экрана, мы Переместим символ к первому касанию, другими словами, `TouchLocation` с индексом 0. Изначально мы настроим требуемой скорость равно разности между расположение символа и первого касания:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Ниже приведен немного math, которые обеспечат символ перехода с той же скоростью. Чтобы объяснить, почему это важно, рассмотрим ситуацию, когда пользователь касается экрана 500 пикселей от где находится символ. Первая строка, в которой `desiredVelocity.X` является набор будет назначен значение 500. Тем не менее, если пользователь были прикосновения к экрану на расстоянии только 100 единиц с символа, то `desiredVelocity.X `будет иметь значение 100. Результат будет иметь что скорость перемещения символа может отвечать на то, как далеко сенсорной точки — с символа. Поскольку мы хотим символ всегда перемещается с той же скоростью, нам нужно изменить desiredVelocity.

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` Инструкции, проверяет ли скорость не-нулевой — другими словами, он проверяет, чтобы убедиться в том, что пользователь не касается начальное место в качестве текущей позиции символа. Если не так, то необходимо задать скорость линии символа остается постоянным независимо от того, как далеко сенсорное устройство является. Для этого, нормализация вектора скорости, что приводит к ее длина, равная 1. Вектора скорости 1 означает, что символ будет перемещаться на 1 пиксель в секунду. Мы будем ускорить этот процесс путем умножения значения необходимый параметр скорости 200.


### <a name="applying-velocity-to-position"></a>Применение к позиции скорости

Скорость, возвращенные `GetDesiredVelocityFromInput` необходимо применить к знаку `X` и `Y` значения в действие во время выполнения. Мы изменим `Update` метод следующим образом:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Что мы используем здесь называется *по времени* перемещения (в отличие от *фреймовые* перемещения). Перемещение на основе времени Умножает значение скорости (в нашем случае значения хранятся в `velocity` переменной), сколько времени прошло с момента последнего обновления, который хранится в `gameTime.ElapsedGameTime.TotalSeconds`. Если игра использует меньшее количество кадров в секунду, время, прошедшее между кадрами поднимается — конечным результатом является то, что объекты, с помощью перемещения на основе времени, всегда будут перемещаться с той же скоростью, независимо от частоты кадров.

Если теперь запустить нашей игре, мы увидим, что символ движется расположение сенсорной:

![](part2-images/image6.gif "Символ, двигаясь расположение сенсорного ввода")

## <a name="matching-movement-and-animation"></a>Сопоставление, перемещения и анимации

Получив наших символ, перемещение и их воспроизведении одной анимации, мы можно определить в оставшейся части нашей анимации, а затем использовать их в соответствии с движениями объекта. При завершении у нас есть восемь анимации в целом:

- Анимации для проходу вверх, вниз, влево и вправо
- Анимации для постоянного по-прежнему и лицевой стороной вверх, вниз, влево и вправо

### <a name="defining-the-rest-of-the-animations"></a>Определение остальная часть анимации

Сначала мы добавим `Animation` экземпляры `CharacterEntity` класс для всех наших анимации, в том же месте, где мы добавили `walkDown`. После этого мы, `CharacterEntity` , имеют следующий `Animation` члены:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Теперь мы определим анимации в `CharacterEntity` конструктор следующим образом:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Следует отметить, что приведенный выше код был добавлен `CharacterEntity` конструктор для упрощения этого пошагового руководства будет короче. Обычно игры будет разделить определение символа анимации в отдельные классы или загрузить эту информацию из данных формата XML или JSON.

Далее мы скорректируем алгоритм, используемый анимации в соответствии с направление, в котором перемещается знак или в соответствии с последней анимации, если символ был только что остановлен. Чтобы сделать это, мы изменим `Update` метод:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

Код для переключения анимации разбивается на два блока. Сначала проверяется, если скорость не равно `Vector2.Zero` – это указывает, движется ли символ. Если символ является перемещение, то мы могли взглянуть `velocity.X` и `velocity.Y` значения, чтобы определить, какие анализирующего анимация должна воспроизводиться.

Если символ не перемещается, а затем нам нужно выбрать символ `currentAnimation` анимации положение —, но мы только вряд ли `currentAnimation` является прохода анимации или не было установлено анимации. Если `currentAnimation` не является одним из четырех анализирующего анимации, а затем символ уже репутацию, поэтому нам не нужно изменять `currentAnimation`.

Этот код образом правильно анимации при прохождении символ и затем сталкиваются с последнего его прогуливался при остановке направление:

![](part2-images/image7.gif "Результат выполнения этого кода является правильно анимации при прохождении символ и затем сталкиваются с последнего направление его прогуливался при остановке")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве было показано, как работать с MonoGame для создания кросс платформенных игр с использованием спрайтов, перемещение объектов, определении входных данных и анимации. Здесь рассматривается создание класса анимации общего назначения. Он также было рассмотрено создание сущности знака для организации логики кода.

## <a name="related-links"></a>Связанные ссылки

- [Ресурс с изображением CharacterSheet (пример)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Walking завершения игры (пример)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
