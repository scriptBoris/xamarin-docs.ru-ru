---
title: Игры подробностями времени разработки
description: Здесь вы найдете сведения о реализации в игре времени разработки, включая работе с картами плитки, создание сущностей, анимация спрайтов и реализации эффективного конфликтов.
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 2687b1c8eca8cfb68660c8a622278aa628459d07
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921941"
---
# <a name="coin-time-game-details"></a>Игры подробностями времени разработки

_Здесь вы найдете сведения о реализации в игре времени разработки, включая работе с картами плитки, создание сущностей, анимация спрайтов и реализации эффективного конфликтов._

Время разработки — полный platformer, игры для iOS и Android. Игра предназначена для того, чтобы собрать все монеты уровня и затем достичь дверца выхода избегая враги и другими препятствиями.

![](cointime-images/image1.png "Игра предназначена для того, чтобы собрать все монеты уровня и затем достичь дверца выхода избегая враги и другими препятствиями")

Здесь вы найдете сведения о реализации времени разработки, на следующие темы:

- [Работа с файлами tmx](#working-with-tmx-files)
- [Уровень загрузки](#level-loading)
- [Добавление новых сущностей](#adding-new-entities)
- [Анимированный сущностей](#animated-entities)


## <a name="content-in-coin-time"></a>Содержимое по времени разработки

Время разработки — образец проекта, который представляет как полного CocosSharp проекта может быть организована. Таблетка времени структуры задачи для упрощения добавления и обслуживания содержимого. Она использует **.tmx** файлы, созданные [копиями](http://www.mapeditor.org) для уровней и XML-файлов для определения анимации. Изменение или Добавление нового содержимого может осуществляться с минимальными усилиями. 

Хотя такой подход делает время монеты проекте, действующие для обучения и эксперименты, она отражает как специалистов игры выполняются. В этом руководстве описываются некоторые из подходов, необходимое для упрощения добавления и изменения содержимого.


## <a name="working-with-tmx-files"></a>Работа с файлами tmx

Уровни монеты времени определяются с помощью форматом файла .tmx выходной [копиями](http://www.mapeditor.org) плитки карты редактора. Подробное рассмотрение работы с копиями см. в разделе [с помощью мозаикой Cocos острым направляющей](~/graphics-games/cocossharp/tiled.md). 

Каждый уровень определяется в файле .tmx, содержащихся в **CoinTime/активы или содержимое и уровни** папки. Все уровни монеты время совместно использовать один файл tileset, который определен в **mastersheet.tsx** файла. Этот файл определяет пользовательские свойства для каждого мозаичного элемента, например сплошная конфликтов плитки, имеет ли или ли плитки следует заменить на экземпляр сущности. Файл mastersheet.tsx позволяет свойства определен только один раз и использовать их на всех уровнях. 


### <a name="editing-a-tile-map"></a>Изменение плитки карты

Чтобы изменить карту плитку, откройте файл .tmx в копиями, дважды щелкнув файл .tmx или открыв его через меню «файл» в копиями. Время монеты уровня плитки карты содержат три уровня: 

- **Сущности** – этот уровень содержит плитки, которые будет заменен экземплярами сущностей во время выполнения. Примеры проигрыватель, монеты, враги и дверца конечного элемента уровня.
- **Рельефа** – этот уровень содержит плитки, которые обычно имеют сплошной конфликтов. Сплошной конфликтов проигрыватель стека на этих плитках без передачи управления дальше. Плитки с сплошной конфликтов могут действовать как стен и перекрытия.
- **Фон** — фон содержит плитки, которые используются в качестве статического фона. Этот уровень не прокручивается при перемещении камеры в уровне, создается внешний глубины через фокусировки.

Мы рассмотрим позже, уровень загрузки кода ожидает, что эти три слоя на всех уровнях времени разработки.

#### <a name="editing-terrain"></a>Редактирование рельефа

Можно поместить плитки, щелкнув в **mastersheet** tileset, а затем щелкнув плитку сопоставления. Например, для рисования новый рельефа уровня:

1. Выберите слой рельефа
1. Щелкните плитку для рисования
1. Щелкните или push и перетащите на схему для закрашивания плитки

    ![](cointime-images/image2.png "Щелкните плитку, чтобы нарисовать 1")

Левой верхней части tileset содержит все рельефа времени разработки. Включает рельефа, являющийся сплошная, **SolidCollision** свойства, как показано в свойствах плитки в левой части экрана:

![](cointime-images/image3.png "Рельефа, являющийся сплошная, включает свойство SolidCollision, как показано в свойствах плитки в левой части экрана")

#### <a name="editing-entities"></a>Изменение сущности

Можно добавлять или удалять из уровня — так же, как рельефа сущностей. **Mastersheet** tileset имеет разместить о посередине по горизонтали, все сущности, поэтому они не будут видны без прокрутки вправо:

![](cointime-images/image4.png "Mastersheet tileset имеет разместить о посередине по горизонтали, все сущности, поэтому они не будут видны без прокрутки вправо")

Новые сущности должны размещаться на **сущностей** слоя.

![](cointime-images/image5.png "Новые сущности должны размещаться на уровне сущности")

Ищет код CoinTime **EntityType** при загрузке уровнем для идентификации плитки, которые следует заменить на сущности: 

![](cointime-images/image6.png "Ищет EntityType CoinTime кода при загрузке уровнем для идентификации плитки, которые следует заменить на сущности")

Когда файл был изменен и сохранен, изменения будут автоматически отображаться построения и запуска проекта:

![](cointime-images/image7.png "Когда файл был изменен и сохранен, изменения будут автоматически отображаться построения и запуска проекта")


### <a name="adding-new-levels"></a>Добавление новых уровней

Процесс добавления уровней для времени разработки необходимо вносить изменения в код, а только небольшие изменения в проект. Чтобы добавить новый уровень:

1. Откройте папку уровня, расположенный в <**CoinTime корневой > \CoinTime\Assets\Content\levels**
1. Скопируйте и вставьте один из уровней, например **level0.tmx**
1. Присвойте новому файлу .tmx, поэтому он по-прежнему уровня последовательность номеров с существующие уровни, такие как **level8.tmx**
1. В Visual Studio или Visual Studio для Mac добавьте новый файл .tmx в папку Android уровней. Убедитесь, что файл использует **AndroidAsset** действие построения.

    ![](cointime-images/image8.png "Убедитесь, что файл использует действие построения AndroidAsset")

1. Добавьте новый файл .tmx в папку iOS уровней. Убедитесь, что для связывания файл из исходного расположения и убедитесь, что он использует **BundleResource** действие построения.

    ![](cointime-images/image9.png "Убедитесь, что для связывания файл из исходного расположения и убедитесь, что он использует действие построения BundleResource")

Новый уровень должны отображаться на экране выбора уровня как уровень 9 (имена файлов на уровне начинаются с 0, но кнопки уровня начинается с номера 1):

![](cointime-images/image10.png "Новый уровень должны отображаться на экране выбора уровня как уровень 9 уровня файла начинаются с 0, но кнопки уровня начинается с номера 1")


## <a name="level-loading"></a>Уровень загрузки

Как показано выше, новые уровни требуют изменений в коде — игры автоматически определяет уровни, если они именуются правильно и добавлены **уровни** папки с действием правильное построение (**BundleResource**или **AndroidAsset**).

Логика для определения количества уровней, содержащихся в `LevelManager` класса. При создании экземпляра `LevelManager` (с использованием единый шаблон) `DetermineAvailbleLevels` вызывается метод:


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp не обеспечивают кросс платформенных подход для обнаружения, если имеются файлы, чтобы мы могли полагаться на `TitleContainer` класса, чтобы попытаться открыть поток. Если код для открытия потока, вызывает исключение, то файл существует и разрывов цикл while. После завершения выполнения цикла, `NumberOfLevels` свойство сообщает, сколько уровней допустимым являются частью проекта.

`LevelSelectScene` Класс использует `LevelManager.NumberOfLevels` чтобы определить, сколько для создания в `CreateLevelButtons` метод:


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

`NumberOflevels` Свойство используется для определения, кнопки, которые должны быть созданы. Этот код считает страницу пользователь просматривает в настоящее время и создает только до шести кнопок на каждой странице. При нажатии кнопки экземпляров вызов `HandleButtonClicked` метод:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Этот метод назначает `CurrentLevel` свойство, используемое по `GameScene` при загрузке уровень. После установки параметра `CurrentLevel`, `GoToGameScene` вызывается метод, переключение сцены из `LevelSelectScene` для `GameScene`.

`GameScene` Вызовы конструктора `GoToLevel`, который выполняет логику уровень загрузки:


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

Далее мы будем взгляните на методы, вызываемые `GoToLevel`.


### <a name="loadlevel"></a>LoadLevel

`LoadLevel` Метод отвечает за загрузку файла .tmx и его добавления в `GameScene`. Этот метод создает любые интерактивные объекты, такие как конфликт или сущностей — он просто создает визуализации уровень также называют *среды*.


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

`CCTileMap` Конструктор принимает имя файла, который создается с помощью номер уровня, передаваемые в `LoadLevel`. Дополнительные сведения о создании и работе с `CCTileMap` экземпляры, в разделе [с помощью мозаикой с руководством CocosSharp](~/graphics-games/cocossharp/tiled.md).

В настоящее время CocosSharp не допускает изменение порядка слоев без удаления и повторного добавления родительских `CCScene` (который является `GameScene` в данном случае), поэтому для изменения порядка слоев требуются последние три строки метода.


### <a name="createcollision"></a>CreateCollision

`CreateCollision` Конструкции метод `LevelCollision` экземпляр, который используется для выполнения *сплошной конфликтов* между проигрывателем и среды.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Без этого конфликта проигрыватель будет передаваться уровень и игра будет невозможно воспроизвести. Сплошной конфликтов позволяет пройти на землю проигрывателя и запрещает проигрывателю прохождение через стен или переход вверх через перекрытия.

Конфликт времени разработки можно добавить с без дополнительного кода — только изменения в мозаичный файлы. 


### <a name="processtileproperties"></a>ProcessTileProperties

После загрузки уровнем и создается коллизии, `ProcessTileProperties` вызывается для выполнения логика, основанная на плитке свойств. Время разработки включает `PropertyLocation` структуры для определения свойств и координаты плитки со следующими свойствами:


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

Эта структура используется для создания экземпляров сущности создавать и удалять ненужные плитки в `ProcessTileProperties` метод:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

Цикл вычисляет каждое свойство плитки, проверки, если ключ `EntityType` или `RemoveMe`. `EntityType` Указывает, что следует создавать экземпляр сущности. `RemoveMe` Указывает, что плитки должны быть полностью удалены во время выполнения.

Если свойство с `EntityType` ключ найден, затем `TryCreateEntity` вызывается которых пытается создать сущность с помощью соответствующего свойства `EntityType` ключ:


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


## <a name="adding-new-entities"></a>Добавление новых сущностей

Время монеты использует шаблон сущности для своих объектов игры (который рассматривается в [сущностей в CocosSharp руководство по](~/graphics-games/cocossharp/entities.md)). Все сущности являются производными от `CCNode`, что означает, что они могут быть добавлены в качестве дочерних элементов `gameplayLayer`.

Каждого типа сущности также ссылаться непосредственно с помощью списка или один экземпляр. Например `Player` ссылается `player` поля и все `Coin` экземпляров имеются ссылки в `coins` списка. Поддержание прямые ссылки на сущности (в отличие от создания ссылок на них через `gameLayer.Children` список) позволяет создать код, который обращается к этих сущностей, более удобным для чтения и позволяет избежать потенциально затратных тип приведения.

Существующий код предоставляет несколько типов сущностей в качестве примеров того, как создавать новые сущности. Для создания новой сущности можно использовать следующие шаги:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1 — определите новый класс с помощью шаблона сущности

Для создания сущности единственное требование заключается в том, чтобы создать класс, унаследованный от класса `CCNode`. Большая часть объектов имеет некоторые visual c#, такие как `CCSprite`, который необходимо добавить в качестве дочернего объекта в его конструктор.

Предоставляет CoinTime `AnimatedSpriteEntity` класс, который упрощает создание анимированных сущностей. Более подробно рассматриваются анимации [разделе анимированы сущности](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 — добавьте новую запись в операторе switch TryCreateEntity

Экземпляры нового объекта должен создаваться в `TryCreateEntity`. Если сущность требует каждые кадра логику как конфликт, AI или чтение входных данных, а затем `GameScene` должен хранить ссылку на объект. Если требуется несколько экземпляров (таких как `Coin` или `Enemy` экземпляры), затем новый `List` должны добавляться к `GameScene` класса.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – изменение свойств плитки новой сущности

После кода поддерживает создание новой сущности, для добавления tileset требуется новая сущность. Можно изменить, открыв любой уровень tileset `.tmx` файл. 

Tileset хранится отдельно от .tmx в **mastersheet.tsx** файла, и его необходимо импортировать перед его можно изменять:

![](cointime-images/image11.png "Tileset хранится отдельно от файла .tsx, поэтому он должен быть импортирован прежде чем его можно изменять")

После импорта выбранных плиток свойства доступны для редактирования и могут быть добавлены EntityType:

![](cointime-images/image12.png "После импорта выбранных плиток свойства доступны для редактирования и могут быть добавлены EntityType")

После создания свойства его значение можно задать, чтобы соответствовать новому `case` в `TryCreateEntity`:

![](cointime-images/image13.png "После создания свойства его значение можно задать в соответствии с новой регистром TryCreateEntity")

После изменения tileset должны быть экспортированы — это делает доступными для всех уровней изменения:

![](cointime-images/image14.png "После изменения tileset, его необходимо экспортировать")

Перезаписать существующую tileset **mastersheet.tsx** tileset:

![](cointime-images/image15.png "перезаписывать существующие tileset mastersheet.tsx HE tileset")


## <a name="entity-tile-removal"></a>Удаление плитки сущности

При загрузке мозаики карты в игру, отдельные элементы мозаики являются статическими объектами. Поскольку сущности требуется пользовательское поведение, такие как перемещение, кода во время разработки удаляет плитки при создании сущности.

`ProcessTileProperties` содержит логику для удаления плитки, которые создают сущностей с помощью `RemoveTile` метод:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

Это автоматическое удаление плитки достаточно для сущностей, которые занимают в tileset, такие как монеты и враги только одна Плитка. Больших сущностей требуется дополнительная логика и свойства.

Дверца требуются две плитки для полного отображения:

![](cointime-images/image16.png "Дверца требует двух плитки для отображения полностью")

Плитка нижней в дверца содержит свойства для создания сущности (**EntityType** значение **дверцу**):

![](cointime-images/image17.png "Плитка нижней в дверца содержит свойства для создания сущности EntityType, равным дверца")

Так, как только нижней плитке дверца удаляется при создании экземпляра дверца, дополнительная логика необходим для удаления top плитки во время выполнения. Top Плитка содержит **RemoveMe** свойство **true**:

![](cointime-images/image18.png "Top плитки RemoveMe свойство имеет значение true")



Это свойство используется для удаления плитки в `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


## <a name="entity-offsets"></a>Смещения сущности

Сущности, созданные из плиток располагаются путем выравнивания center сущности с помощью центра плитки. Больше сущности, такие как `Door`, используйте дополнительные свойства и логику должен располагаться правильно. 

Плитки дверца нижней определяет `Door` указывает размещение сущности **смещение** значение 4. Без этого свойства `Door` экземпляр помещается по центру плитки:

![](cointime-images/image19.png "Без этого свойства экземпляра дверца помещается по центру плитки")

 

Это устранить, применяя **смещение** значение в `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


## <a name="animated-entities"></a>Анимированный сущностей

Время монеты включает несколько анимированных сущностей. `Player` И `Enemy` сущностей воспроизведение анимации стека и `Door` сущности играет Анимация открытия после сбора всех монеты.


### <a name="achx-files"></a>файлы .achx

Время анимации монеты определяются в файлах .achx. Каждой анимации определяется между `AnimationChain` теги, как показано в следующем анимации, определенные в **propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

Эту анимацию содержит только один кадр, возникающие в сущности пик отображение статическое изображение. Сущности можно использовать файлы .achx ли они отображают одного или нескольких кадров анимации. Можно добавить дополнительные кадры .achx файлы без каких-либо изменений в коде. 

Кадры определяют, какие изображения для отображения в `TextureName` параметра и координаты, отображаемое в `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, и `BottomCoordinate` тегов. Они представляют координаты точки кадра анимации, в которой используется — **mastersheet.png** в этом случае.

![](cointime-images/image20.png "Они представляют координаты точки кадра анимации в образе")

`FrameLength` Свойство определяет количество секунд, которые должны отображаться кадр анимации. Это значение будет игнорироваться одного кадра анимации.

Все остальные свойства AnimationChain в файле .achx учитываются средой разработки.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Анимация логика содержится в `AnimatedSpriteEntity` класс, который используется как базовый класс для большинства объектов используется в `GameScene`. Эта служба предоставляет следующие возможности:

 - Загрузка `.achx` файлов
 - Анимация кэша загруженных анимаций
 - Экземпляр CCSprite для отображения анимации
 - Логику для изменения текущего кадра

Конструктор пики предоставляет простой пример того, как загрузка и использование анимации.


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx** содержит только одна анимация, конструктор получает доступ к данной анимации по индексу. Если файл .achx содержит несколько анимации, то анимации можно ссылаться по имени, как показано в `Enemy` конструктор:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Сводка

В настоящем руководстве приводятся сведения о реализации времени разработки. Время монеты создается полностью игры, но также можно легко изменить и развернуть проект. Читатели, рекомендуется тратить время внесения изменения в уровнях, добавления новых уровней и создания новых сущностей, чтобы лучше разобраться в реализации времени разработки.

## <a name="related-links"></a>Связанные ссылки

- [Проектом игры (пример)](https://developer.xamarin.com/samples/mobile/CoinTime/)
