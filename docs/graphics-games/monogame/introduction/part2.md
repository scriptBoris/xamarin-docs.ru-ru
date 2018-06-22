---
title: Часть 2 — реализация WalkingGame
description: В этом пошаговом руководстве показано, как добавить логику игры и содержимого в пустой проект для создания демонстрационную версию анимированных sprite, перемещение с MonoGame сенсорный ввод.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 87678d9d77f75bccc68a667d3fb0f35b641b937c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921668"
---
# <a name="part-2--implementing-the-walkinggame"></a>Часть 2 — реализация WalkingGame

_В этом пошаговом руководстве показано, как добавить логику игры и содержимого в пустой проект для создания демонстрационную версию анимированных sprite, перемещение с MonoGame сенсорный ввод._

Предыдущей части в данном пошаговом руководстве было показано, как создать пустой MonoGame проектов. Будет создан на эти предыдущей части, делая простой демонстрационный игры. В этом разделе содержатся следующие подразделы:

- Распаковки содержимого нашей игр
- Общие сведения о классе MonoGame
- Подготовка к просмотру нашей первой Sprite
- Создание CharacterEntity
- Добавление CharacterEntity игры
- Создание класса анимации
- Добавление CharacterEntity первой анимации
- Добавление символа перемещения
- Перемещение и анимации


## <a name="unzipping-our-game-content"></a>Распаковки содержимого нашей игр

Прежде чем начать создание кода, нужно распаковать нашей игры *содержимого*. Игры разработчики часто используют термин *содержимого* для ссылки на файлы без кода, которые обычно создаются средой visual исполнители, игры конструкторы или конструкторы аудио. Общие типы содержимого включают файлы, используемые для отображения визуальных элементов, воспроизведение звука или управлять поведением искусственный интеллект (AI). От разработки игр Перспектива содержимое команды обычно создается путем не программистов.

Содержимое, используемое здесь можно найти [на github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Нам потребуется этих файлов, загруженных в расположении, которое мы будет обращаться к далее в этом пошаговом руководстве.

## <a name="monogame-class-overview"></a>Общие сведения о классе MonoGame

Во-первых, мы рассмотрим MonoGame классы, используемые в основные визуализации:

- `SpriteBatch` — используется для вывода на экран двумерной графики. *Спрайтов* являются 2D визуальные элементы, которые используются для отображения изображения на экране. `SpriteBatch` Объекта можно нарисовать один sprite во время между его `Begin` и `End` методов или несколько спрайтов могут быть сгруппированы вместе, или *пакетные*.
- `Texture2D` — Представляет объект изображения во время выполнения. `Texture2D` экземпляры часто создаются из форматов, например .png или .bmp, несмотря на то, что они могут также создаваться динамически во время выполнения. `Texture2D` экземпляры используются при подготовке к просмотру с `SpriteBatch` экземпляров.
- `Vector2` — представляет позицию в двумерной системе координат, которая часто используется для размещения визуальных объектов. Также включает MonoGame `Vector3` и `Vector4` , но мы будет использовать только `Vector2` в этом пошаговом руководстве.
- `Rectangle` — четырех сторон области с положение, ширину и высоту. Мы будем использовать это для определения, какая часть наших `Texture2D` для подготовки, если мы приступаем к работе с анимацией.

Также следует отметить, что MonoGame не поддерживается корпорацией Майкрософт — несмотря на его пространство имен. `Microsoft.Xna` В MonoGame для упрощения переноса существующих проектов XNA в MonoGame используется пространство имен.

## <a name="rendering-our-first-sprite"></a>Подготовка к просмотру нашей первой Sprite

Далее мы будем привлекать один sprite на экране, чтобы показать, как выполнять двухмерной отрисовки в MonoGame.

### <a name="creating-a-texture2d"></a>Создание Texture2D

Необходимо ли создавать `Texture2D` экземпляр для использования при подготовке к просмотру нашей sprite. Все содержимое игры в конечном счете находится в папке с именем **содержимое,** находится в проекте конкретную платформу. MonoGame общих проектов не может содержать содержимое, как содержимое необходимо использовать платформы действия построения. Разработчики CocosSharp найти папку содержимого привычная структура — они находятся в той же позиции в проектах CocosSharp и MonoGame. Папки содержимого можно найти в проекте iOS и в папку Assets в проекте Android.

Для добавления содержимого нашей игры, щелкните правой кнопкой мыши **содержимого** папку и выберите **Добавить > Добавить файлы...** Перейдите к расположению, где были извлечены content.zip файл и выберите **charactersheet.png** файла. Если будет задан вопрос о том, как добавить файл в папку, следует выбрать **копирования** параметр:

![](part2-images/image1.png "Если будет задан вопрос о том, как добавить файл в папку, выберите параметр Копировать")

Эту папку контента теперь содержит файл charactersheet.png:

![](part2-images/image2.png "Эту папку контента теперь содержит файл charactersheet.png")

Далее мы добавим код, чтобы загрузить файл charactersheet.png и создать `Texture2D`. Для этого откройте `Game1.cs` файл и добавьте в класс Game1.cs следующее поле:

```csharp
Texture2D characterSheetTexture;
```

Далее мы создадим `characterSheetTexture` в `LoadContent` метод. До внесения изменений `LoadContent` метод выглядит следующим образом:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Следует отметить, что проект по умолчанию уже создает `spriteBatch` экземпляра для нас. Мы будем использовать это позже, но мы не будет добавлять дополнительный код для подготовки `spriteBatch` для использования. С другой стороны наши `spriteSheetTexture` требует инициализации, поэтому мы будет инициализировать его после `spriteBatch` создается:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Теперь, когда у нас есть `SpriteBatch` экземпляра и `Texture2D` экземпляр, можно добавить наш код отрисовки для `Game1.Draw` метод:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Выполнение игры теперь показывает один sprite отображение текстуры, созданные на основе charactersheet.png:

![](part2-images/image3.png "Выполнение игры теперь показывает один sprite отображение текстуры, созданные на основе charactersheet.png")

## <a name="creating-the-characterentity"></a>Создание CharacterEntity

Пока мы добавили кода для отображения одного sprite на экран. Тем не менее мы бы разработать полный игры без создания классов, мы будет выполняться в проблемы с кодом организации.

### <a name="what-is-an-entity"></a>Что такое сущности

Для организации кода игры распространенный подход заключается в том, чтобы создать новый класс для каждой игры *сущности* типа. Сущности в разработки игр — это объект, который может содержать некоторые следующие характеристики (не являются обязательным):

- Визуальный элемент, например sprite, текст или трехмерной модели
- Физический или каждый кадр поведению, например единое patrolling путь набора или отвечает на входной символ проигрывателя
- Может быть динамически создаваться и уничтожаться, такие как появление питания и собираемых проигрывателем

Систем организации сущности могут быть сложными, и многие игры ядер предлагать классы для управления сущностями. Мы надо будет реализовать систему очень простого объекта, поэтому стоит отметить, что полный игр обычно требуется несколько организации со стороны разработчика.


### <a name="defining-the-characterentity"></a>Определение CharacterEntity

Наш сущности, которые мы будем называть `CharacterEntity`, будет иметь следующие характеристики:

- Возможность загружать собственные `Texture2D`
- Возможность отображения, включая содержащий вызова методов для обновления анализирующего анимации
- `X` и Y свойства, управляющие позицию символа.
- Возможности для самообновления — в частности, для чтения значения из сенсорный экран и соответствующим образом изменить положение.

Чтобы добавить `CharacterEntity` нашей игры, щелкните правой кнопкой мыши или элемент управления, щелкните **WalkingGame** проект и выберите **Добавить > новый файл...** . Выберите **пустым классом** параметр и присвойте новому файлу **CharacterEntity**, нажмите кнопку **New**.

Сначала мы добавим возможность `CharacterEntity` для загрузки `Texture2D` а также для рисования самого себя. Нам предстоит изменить только что добавленную `CharacterEntity.cs` следующим образом:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

Этот код добавляет ответственность за позиционирование, визуализации и загружает содержимое для `CharacterEntity`. Давайте немного указание на некоторые аспекты, в приведенном выше коде.

### <a name="why-are-x-and-y-floats"></a>Почему являются X и Y значений с плавающей запятой?

Разработчиков, которые не знакомы с программированием игры может возникнуть вопрос, почему `float` используется тип, в отличие от `int` или `double`. Причина заключается в наиболее распространенных для позиционирования в коде низкоуровневой прорисовки, 32-разрядное значение. Тип double занимает 64 бит для точности, который необходим для размещения объектов редко. 32-разрядная версия различие может показаться незначащие, многие современные игры включают графики, требующий обработки десятков тысяч значения положения каждого кадра (30 или 60 раз в секунду). Вырезание объем памяти, который необходимо переместить через графики конвейер вдвое может иметь значительное влияние на производительность игры.

`int` Тип данных может быть соответствующие единицы измерения для позиционирования, но его задания ограничений на способ располагаются сущностей. Например, использование целочисленных значений усложняет реализовать smooth перемещения для игр, поддерживающих увеличение масштаба или 3D камеры (который разрешены `SpriteBatch`).

Наконец мы увидим, что логики, которая перемещает нашей символ по экрану будет сделать с помощью значений времени выполнения игры. Результат умножения скорости, сколько времени прошло через заданный интервал редко приведет целым числом, поэтому необходимо использовать `float` для `X` и `Y`.

### <a name="why-is-charactersheettexture-static"></a>Почему является characterSheetTexture статических?

`characterSheetTexture` `Texture2D` Экземпляр является представление charactersheet.png файла во время выполнения. Другими словами, он содержит значения цвета для каждого пикселя, извлеченное из источника **charactersheet.png** файла. Наш игры, будто для создания двух `CharacterEntity` экземпляров, то каждый из них требуется доступ сведения из charactersheet.png. В этом случае мы хотим не вызывают снижение производительности при загрузке charactersheet.png дважды, а также бы мы хотим использовать повторяющиеся текстуры памяти, хранятся в оперативной памяти. С помощью `static` позволяет одного и того же `Texture2D` по всем `CharacterEntity` экземпляров.

С помощью `static` члены для представление во время выполнения содержимого — это упрощенный решение и не устраняет проблемы, возникшие при больших игры, например выгрузки содержимого при переходе из одного уровня. Более сложные решения, которые выходят за рамки этого пошагового руководства, включать с помощью конвейера содержимого элемента MonoGame или создания системы управления содержимым.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Почему необходимо SpriteBatch передается как аргумент Instead of создаются сущности?

`Draw` Метод реализации выше принимает `SpriteBatch` аргумент, но в отличие от этого, `characterSheetTexture` создается с `CharacterEntity`.

Причина этого — так как наиболее эффективный отрисовки возможен, если же `SpriteBatch` экземпляра используется для всех `Draw` вызовов и когда все `Draw` вызовы выполняются в одном наборе `Begin` и `End` вызовов. Конечно, нашей игры будут содержать только единственный экземпляр сущности, но шаблон, который позволяет нескольким сущностям, чтобы использовать те же преимущества более сложных игр `SpriteBatch` экземпляра.


## <a name="adding-characterentity-to-the-game"></a>Добавление CharacterEntity игры

Мы добавили нашей `CharacterEntity` с кодом для подготовки к просмотру сам может заменить код в `Game1.cs` экземпляра эта новая сущность. Для этого мы заменим `Texture2D` с `CharacterEntity` в `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Далее мы добавим создания экземпляра этой сущности в `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Необходимо также удалить `Texture2D` создания из `LoadContent` , так как сейчас выполняется внутри наших `CharacterEntity`. `Game1.LoadContent` должна выглядеть следующим образом:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Стоит отметить, что несмотря на свое название `LoadContent` метод не является единственным местом, где можно загрузить содержимое — многие реализация игры содержимого загрузке их метод инициализации, или даже в свой код обновления, как содержимое, может не понадобиться пока не достигнет проигрыватель Некоторые точки игры.

Наконец мы можно изменить метод Draw следующим образом:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Если мы запустим игры, вы узнаете символ. Поскольку X и Y по умолчанию 0, символ располагается от верхнего левого угла экрана:

![](part2-images/image4.png "Поскольку X и Y по умолчанию 0, затем символ располагается от верхнего левого угла экрана")

## <a name="creating-the-animation-class"></a>Создание класса анимации

В настоящее время наши `CharacterEntity` отображает полную **charactersheet.png** файла. Такой подход несколько изображений в один файл называется *лист sprite*. Как правило спрайт подготавливается к просмотру только часть sprite листа. Мы изменит `CharacterEntity` для подготовки к просмотру часть данного **charactersheet.png**, и эта часть будет меняться со временем для отображения анализирующего анимации.

Мы создадим `Animation` класса для управления логикой и состоянии CharacterEntity анимации. Класс анимации будет общим классом, который может использоваться для любого объекта, не только `CharacterEntity` анимации. Ultimate `Animation` класс будет предоставлять `Rectangle` которого `CharacterEntity` будет использовать при рисовании сам. Также мы создадим `AnimationFrame` класс, который будет использоваться для определения анимации.


### <a name="defining-animationframe"></a>Определение AnimationFrame

`AnimationFrame` не будет содержать все процедуры, относящиеся к анимации. Мы будем использовать его только для хранения данных. Чтобы добавить `AnimationFrame` класса, щелкните правой кнопкой мыши или элемент управления, щелкните **WalkingGame** общего проекта и выберите **Добавить > новый файл...** Введите имя **AnimationFrame** и нажмите кнопку **New** кнопки. Мы изменим `AnimationFrame.cs` файл, чтобы он содержал следующий код:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame` Класс содержит два элемента данных:

- `SourceRectangle` — Определяет область `Texture2D` отображаемые по `AnimationFrame`. Это значение измеряется в пикселях с верхней левой, (0, 0).
- `Duration` — Определяет продолжительность `AnimationFrame` отображается при использовании в `Animation`.

### <a name="defining-animation"></a>Определение анимации

`Animation` Будет содержать класс `List<AnimationFrame>` и логику для переключения рамка в данный момент отображается в соответствии с сколько времени прошло.

Чтобы добавить `Animation` класса, щелкните правой кнопкой мыши или элемент управления, щелкните **WalkingGame** общего проекта и выберите **Добавить > новый файл...** . Введите имя **анимации** и нажмите кнопку **New** кнопки. Мы изменим `Animation.cs` файл, поэтому он содержит следующий код:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Рассмотрим некоторые из сведений о `Animation` класса.

### <a name="frames-list"></a>Список кадров

`frames` Член в том, что хранит данные для анимации. Будет добавлен код, который создает экземпляры анимации `AnimationFrame` экземпляры `frames` список через `AddFrame` метод. Более полную реализацию может предложить `public` методы или свойства для изменения `frames`, но мы будем ограничивать его функциональные возможности по добавлению кадров в данном пошаговом руководстве.

### <a name="duration"></a>Длительность

Возвращает значение общей длительности `Animation,` которого получен путем добавления в течение все содержащиеся в нем `AnimationFrame` экземпляров. Это значение может быть кэширован, если `AnimationFrame` были постоянный объект, но так как мы реализован AnimationFrame как класс, который может быть изменен после добавления анимации, необходимые для вычисления этого значения при каждом доступе к этому свойству.

### <a name="update"></a>Обновление

`Update` Метод должен вызываться каждый кадр (то есть каждый раз обновить всю игру). Он предназначен для повышения `timeIntoAnimation` член, который используется для возврата текущих отображаемых кадров. Логика `Update` предотвращает `timeIntoAnimation` никогда не превышает длительность всей анимации.

Так как мы будем использовать `Animation` для отображения анализирующего анимации, то требуется, чтобы повторить при достижении конца анимации. Это можно сделать, проверив, если `timeIntoAnimation` больше, чем `Duration` значение. В этом случае он к началу цикла и сохранить остаток, цикла анимации.

### <a name="obtaining-the-current-frames-rectangle"></a>Получение текущего кадра прямоугольника

Назначение `Animation` класс находится в конечном счете обеспечивают `Rectangle` может использоваться при рисовании спрайт. В настоящее время `Animation` класс содержит логику для изменения `timeIntoAnimation` член, который будет использоваться для получения которого `Rectangle` должны отображаться. Это нужно сделать, создав `CurrentRectangle` свойства `Animation` класса. Скопируйте это свойство в `Animation` класса:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Добавление CharacterEntity первой анимации

`CharacterEntity` Будет содержать анимации для проходу постоянными, а также ссылку на текущий `Animation` отображение.

Во-первых, мы добавим наш первый `Animation,` которой мы будем использовать для тестирования out функциональные возможности, которые записаны на данный момент. Давайте добавим класс CharacterEntity следующие члены:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Затем определим `walkDown` анимации. Измените параметр `CharacterEntity` конструктор следующим образом:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Как упоминалось ранее, нам потребуется вызвать `Animation.Update` для воспроизведение анимации на основе времени. Кроме того, необходимо назначить `currentAnimation`. Для теперь нам предстоит присвоить `currentAnimation` для `walkDown`, но мы будем заменить этот код позже при мы реализовали логику перемещения. Мы добавим `Update` метод `CharacterEntity` следующим образом:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Теперь, когда у нас есть `currentAnimation` , задания и обновления, мы можно использовать для выполнения наших рисования. Мы изменим `CharacterEntity.Draw` следующим образом:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

Последний шаг по получении `CharacterEntity` анимации является вызов только что добавленном `Update` метод `Game1`. Изменить `Game1.Update` следующим образом:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Теперь `CharacterEntity` воспроизводит его `walkDown` анимации:

![](part2-images/image5.gif "Теперь CharacterEntity воспроизводится анимация walkDown")

## <a name="adding-movement-to-the-character"></a>Добавление символа перемещения

Далее мы будем добавлять перемещения нашей символ, с помощью элементов управления сенсорный ввод. Когда пользователь касается экрана, символ будет перейти к точке, где затронутых экрана. Если изменения не обнаружены, символ будет стоять на месте.

### <a name="defining-getdesiredvelocityfrominput"></a>Определение GetDesiredVelocityFromInput

Мы будем использовать его MonoGame `TouchPanel` класс, предоставляющий сведения о текущем состоянии сенсорного экрана. Давайте добавим в метод, который будет проверять `TouchPanel` и возвращать нужные скорости нашей символ:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState` Возвращает `TouchCollection` которой содержатся сведения о которой пользователь касается экрана. Если пользователь не касается экрана, а затем `TouchCollection` будет пустым, в этом случае не следует перемещать символ.

Если пользователь касается экрана, будут перенесены символ сторону первого касания, другими словами, `TouchLocation` с индексом 0. Сначала мы настроим требуемой скорости одинаковые разницу между символа и расположение первого касания для:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Ниже приведен немного математические, которая будет хранить символ перемещения с той же скоростью. Чтобы объяснить, почему это важно, рассмотрим ситуацию, когда пользователь касается экрана 500 пикселей от где находится символ. Первая строка, в которой `desiredVelocity.X` является набор назначается значение 500. Тем не менее, если пользователь были касаясь экрана на расстоянии только 100 единиц от символа, то `desiredVelocity.X `будет иметь значение 100. Результат бы, что скорость перемещения символа может отвечать на то, как далеко что из символ точки касания. Поскольку мы хотим символа всегда перемещать с той же скоростью, нам нужно изменить desiredVelocity.

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` Инструкция выполняет проверку при скорости не-нулевой — другими словами, он проверяет, чтобы убедиться в том, что пользователь не касается же позицию в текущей позиции. Если нет, необходимо задать символ скорость должна быть константой, независимо от того, как далеко сенсорный не. Для этого, нормализация вектора скорости, что приводит к ее длина, равная 1. Вектора скорости 1 означает, что символ будут перемещены в 1 пиксель в секунду. Мы будем ускорить выполнение путем умножения значения желаемая скорость 200.


### <a name="applying-velocity-to-position"></a>Применение скорости в положение

Скорость, возвращенные `GetDesiredVelocityFromInput` необходимо применить к символу `X` и `Y` значения в действие во время выполнения. Мы изменим `Update` метод следующим образом:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Что мы используем здесь называется *синхронизированного* перемещения (в отличие от *основанных на кадре* перемещения). Перемещение на основе времени Умножает значение скорости (в нашем случае значения хранятся в `velocity` переменная), сколько времени прошло с момента последнего обновления, который хранится в `gameTime.ElapsedGameTime.TotalSeconds`. Если игра работает на меньшее число кадров в секунду, время, прошедшее между кадрами поднимается — конечным результатом является то, что объекты с помощью перемещения на основе времени всегда будут перемещены в той же скоростью, независимо от частоты кадров.

Теперь запускаем нашей игры мы увидеть, что символ переходит расположение сенсорный ввод:

![](part2-images/image6.gif "Символ перемещается по направлению к расположение сенсорного ввода")

## <a name="matching-movement-and-animation"></a>Перемещение и анимации

После получения нашей символ перемещения и их воспроизведении одна анимация, мы можно определить в оставшейся части нашей анимации, а затем использовать их в соответствии с движениями объекта. При завершении мы будет иметь восемь анимации в целом:

- Анимации для проходу вверх, вниз, влево и вправо
- Анимации для имеющихся по-прежнему и лицевой стороной вверх, вниз, влево и вправо

### <a name="defining-the-rest-of-the-animations"></a>Определение остальная часть анимации

Сначала мы добавим `Animation` экземпляры `CharacterEntity` класс для всех наших анимации в том же месте, где мы добавили `walkDown`. После этого мы, `CharacterEntity` будет иметь следующие `Animation` члены:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Теперь мы определим анимации в `CharacterEntity` конструктор следующим образом:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Следует отметить, что приведенный выше код добавлен `CharacterEntity` конструктор для упрощения этого пошагового руководства будет короче. Обычно игры будет отделить определение символа анимации в собственные классы или загрузить эту информацию из в формат данных, например XML или JSON.

Далее мы будем настроить алгоритм, используемый анимации направлению, что при перемещении символ, или в соответствии с последней анимации, если символ лишь остановлено. Чтобы сделать это, мы изменим `Update` метод:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

Код для переключения анимации разделяется на два блока. Сначала проверяется, если скорость не равно `Vector2.Zero` — это указывает, передаются ли символ. Если символ является перемещение, то можно взглянуть на `velocity.X` и `velocity.Y` значения, чтобы определить, какие анализирующего анимации для воспроизведения.

Если символ не перемещается, а затем нам нужно выбрать символ `currentAnimation` постоянный анимации — но мы только делать это, если `currentAnimation` является проходу анимации или не было установлено анимации. Если `currentAnimation` не является одним из четырех анализирующего анимации, а затем символ уже основано, нам не нужно изменять `currentAnimation`.

Результат выполнения этого кода является правильно анимация во время прохода символ и затем сталкиваются последнего направление, он проходу при каждой остановке:

![](part2-images/image7.gif "Результат выполнения этого кода является правильно анимация во время прохода символ и затем сталкиваются последнего направление, он проходу при каждой остановке")

## <a name="summary"></a>Сводка

В данном пошаговом руководстве было показано, как работать с MonoGame создание кросс платформенной игры с спрайтов, перемещение объектов, входной обнаружения и анимации. В нем описывается, создав класс общего назначения анимации. Он также было рассмотрено создание сущности знака для организации логики кода.

## <a name="related-links"></a>Связанные ссылки

- [Ресурс изображения CharacterSheet (пример)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Обход завершения игры (пример)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
