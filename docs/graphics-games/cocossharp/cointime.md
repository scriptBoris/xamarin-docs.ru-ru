---
title: Монеты сведениями на время игр
description: Здесь вы найдете сведения о реализации в игре времени разработки, включая работе с картами плитки, создание сущностей, анимация спрайтов и реализации эффективного конфликтов.
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112628"
---
# <a name="coin-time-game-details"></a>Монеты сведениями на время игр

_Здесь вы найдете сведения о реализации в игре времени разработки, включая работе с картами плитки, создание сущностей, анимация спрайтов и реализации эффективного конфликтов._

Время монеты — полный платформер, игры для iOS и Android. Цель игры — собрать все монеты уровня и, обратитесь к двери выхода избегая монстров и препятствия.

![](cointime-images/image1.png "Цель игры — собрать все монеты уровня и, обратитесь к двери выхода избегая монстров и препятствия")

Здесь вы найдете сведения о реализации монеты времени, будут рассмотрены следующие темы:

- [Работа с файлами tmx](#working-with-tmx-files)
- [Уровень загрузки](#level-loading)
- [Добавление новых сущностей](#adding-new-entities)
- [Анимированный сущностей](#animated-entities)


## <a name="content-in-coin-time"></a>Содержимого вовремя монеты

Время монеты — пример проекта, который представляет, как может быть организовано полного проекта CocosSharp. Coin Time's структуры призван упростить добавление и обслуживания содержимого. Она использует **.tmx** файлы, созданные [копиями](http://www.mapeditor.org) для уровней и XML-файлы для определения анимаций. Изменения или добавления нового содержимого может осуществляться с минимальными усилиями. 

Хотя такой подход делает время монеты проекте, действующие для обучения и службы "Экспериментирование", они также отражают как профессиональных игр выполняются. В этом руководстве описываются некоторые подходы, необходимое для упрощения добавления и изменения содержимого.


## <a name="working-with-tmx-files"></a>Работа с файлами tmx

Временные уровни монеты определяются с помощью форматом файла .tmx выходной [копиями](http://www.mapeditor.org) плитки карты редактора. Подробное описание работы с копиями, см. в разделе [использование Tiled с Cocos четкие руководства](~/graphics-games/cocossharp/tiled.md). 

Каждый уровень определяется в отдельном файле .tmx, содержащихся в **CoinTime/ресурсы/Content/уровни** папки. Все уровни монеты время совместно использовать один файл tileset, который определен в **mastersheet.tsx** файла. Этот файл определяет пользовательские свойства для каждого мозаичного элемента, например сплошная конфликтов плитки, имеет ли или ли плитку следует заменить экземпляр сущности. Файл mastersheet.tsx позволяет свойства определен только один раз и использовать их на всех уровнях. 


### <a name="editing-a-tile-map"></a>Редактирование плитки карты

Чтобы изменить карту плитку, откройте файл .tmx в копиями, дважды щелкнув файл .tmx или открыв его через меню "файл" в копиями. Coin Time уровня плитки карты содержат три уровня: 

- **Сущности** – этот уровень содержит плитки, которые будут заменены экземпляров сущностей во время выполнения. Примеры включают проигрыватель, монет, противников и окончания из уровня на двери.
- **Ландшафта** – этот уровень содержит плитки, которые обычно имеют сплошной конфликтов. Сплошной конфликтов игрок может пройти на этих плитках без дальше. Плитки с сплошной конфликтов также может служить стен и перекрытия.
- **Фон** — фон содержит плитки, которые используются в качестве статического фона. Этот уровень не прокручивается при перемещении камеры на протяжении всего уровне, создается внешний вид глубины через фокусировки.

Мы рассмотрим позже, уровень загрузки кода ожидает, что эти три слоя на всех уровнях монеты времени.

#### <a name="editing-terrain"></a>Редактирование ландшафта

Плитки можно поместить, щелкнув в **mastersheet** tileset, а затем щелкните плитку, схемы. Например, для закрашивания новые поверхности в пределах уровня:

1. Выберите слой ландшафта
1. Щелкните плитку для рисования
1. Нажмите кнопку или Push-уведомлений и перетащите на карту, чтобы рисовать плитки

    ![](cointime-images/image2.png "Щелкните плитку, чтобы нарисовать 1")

Левого верхнего угла tileset содержит все ландшафта монеты времени. Включает в себя ландшафта, который является сплошная заливка, **SolidCollision** свойства, как показано в свойства плитки в левой части экрана:

![](cointime-images/image3.png "Ландшафта, являющийся сплошной, включает свойство SolidCollision, как показано в свойства плитки в левой части экрана")

#### <a name="editing-entities"></a>Редактирование сущностей

Сущности можно добавлять или удалять из уровня — так же, как ландшафта. **Mastersheet** tileset имеет все сущности, поместить о посередине по горизонтали, поэтому они могут не отображаться без прокрутки вправо:

![](cointime-images/image4.png "Mastersheet tileset имеет все сущности, поместить о посередине по горизонтали, поэтому они могут не отображаться без прокрутки вправо")

Новые сущности должны размещаться на **сущностей** слоя.

![](cointime-images/image5.png "Новые сущности должны размещаться на уровне сущности")

Ищет код CoinTime **EntityType** при загрузке уровнем для идентификации плитки, которые должны быть заменены сущностей: 

![](cointime-images/image6.png "Ищет EntityType CoinTime кода при загрузке уровнем для идентификации плитки, которые должны быть заменены сущностей")

После изменения и сохранения файла, изменения будут автоматически отображаться построения и запуска проекта:

![](cointime-images/image7.png "После изменения и сохранения файла, изменения будут автоматически отображаться построения и запуска проекта")


### <a name="adding-new-levels"></a>Добавление новых уровней

Процесс добавления уровней монеты время требуется без изменения кода, а только небольшие изменения в проект. Чтобы добавить новый уровень:

1. Откройте папку уровня, расположенный <**CoinTime корневой > \CoinTime\Assets\Content\levels**
1. Скопируйте и вставьте один из уровней, такие как **level0.tmx**
1. Присвойте новому файлу .tmx, поэтому она продолжает уровня последовательности чисел с существующие уровни, такие как **level8.tmx**
1. В Visual Studio или Visual Studio для Mac добавьте новый файл .tmx в папку уровнях Android. Убедитесь, что файл использует **AndroidAsset** действие при сборке.

    ![](cointime-images/image8.png "Убедитесь, что файл использует действие построения AndroidAsset")

1. Добавьте новый файл .tmx в папку iOS уровней. Убедитесь, что связывание файла из исходного расположения и убедитесь, что он использует **BundleResource** действие при сборке.

    ![](cointime-images/image9.png "Убедитесь, что связывание файла из исходного расположения и убедитесь, что он использует действие построения BundleResource")

Новый уровень должны отображаться на экране выберите уровня как уровень 9 (имена файлов на уровне начинаются с 0, но кнопки уровня начинается с номера 1):

![](cointime-images/image10.png "Новый уровень должны отображаться на экране выберите уровня как уровень 9 уровня файла начинаются с 0, но кнопки уровня начинается с номера 1")


## <a name="level-loading"></a>Уровень загрузки

Как было показано ранее, новые уровни не требуют изменений в коде – игра автоматически обнаруживает уровни, если они будут правильно назван и добавлены **уровни** папки с действием правильную сборку (**BundleResource**или **AndroidAsset**).

Логика для определения количества уровней, содержится в `LevelManager` класса. При создании экземпляра класса `LevelManager` создается (с помощью одноэлементного шаблона), `DetermineAvailbleLevels` вызывается метод:


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

CocosSharp не предоставляет кроссплатформенный подход для обнаружения, если файлы отсутствуют, поэтому нам приходится полагаться на `TitleContainer` классы к попытке открыть поток. Если код для открытия потока порождает исключение, то файл существует и разрывов цикл while. После завершения цикла `NumberOfLevels` свойство сообщает, сколько допустимые уровни: часть проекта.

`LevelSelectScene` Класс использует `LevelManager.NumberOfLevels` чтобы определить, сколько кнопок для создания в `CreateLevelButtons` метод:


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

`NumberOflevels` Свойство используется для определения того, кнопки, которые должен будет создан. Этот код считает, что страницу пользователь просматривает в настоящее время и создает только до шести кнопок на одной странице. При нажатии кнопки экземпляров вызов `HandleButtonClicked` метод:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Этот метод назначает `CurrentLevel` свойство, которое используется `GameScene` при загрузке уровнем. После задания `CurrentLevel`, `GoToGameScene` вызывается метод, переключение сцены `LevelSelectScene` для `GameScene`.

`GameScene` Конструктор вызывает `GoToLevel`, который выполняет логику уровень загрузки:


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

Далее мы рассмотрим краткий обзор методов, вызываемых в `GoToLevel`.


### <a name="loadlevel"></a>LoadLevel

`LoadLevel` Метод отвечает за загрузку файла .tmx и добавления его в `GameScene`. Этот метод не создает любые интерактивные объекты, такие как конфликт или сущности — он просто создает визуальные элементы для уровня, называемый также *среды*.


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

`CCTileMap` Конструктор принимает имя файла, который создается с помощью номер уровня, передаваемые в `LoadLevel`. Дополнительные сведения о создании и работе с `CCTileMap` экземпляров, см. в разделе [использование Tiled с CocosSharp руководство](~/graphics-games/cocossharp/tiled.md).

В настоящее время CocosSharp не допускает изменение порядка слоев без удаления и повторного добавления их к родительскому `CCScene` (который является `GameScene` в данном случае), поэтому для изменения порядка слоев требуются последние несколько строк метода.


### <a name="createcollision"></a>CreateCollision

`CreateCollision` Конструкции метод `LevelCollision` экземпляр, который используется для выполнения *сплошной конфликтов* между проигрывателя и средой.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Без такой коллизии проигрыватель будет передаваться уровень и игра будет воспроизводиться. Сплошной конфликтов позволяет стека на землю проигрывателя и запрещает проигрывателю прохода сквозь стены или переходить вверх через перекрытия.

Конфликт монеты времени могут добавляться без дополнительных кода — только изменения в файлы мозаики. 


### <a name="processtileproperties"></a>ProcessTileProperties

После того как уровень загружается и создания конфликт `ProcessTileProperties` вызывается для выполнения операции на основании свойства мозаики. Включает в себя время монеты `PropertyLocation` структуры для определения свойств и координаты плитки со следующими свойствами:


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

Эта структура используется для создания создания экземпляров сущности и удалить ненужные плитки в `ProcessTileProperties` метод:


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

Цикл по каждому элементу оценивает каждого свойства плитки, проверки, если ключ `EntityType` или `RemoveMe`. `EntityType` Указывает, что должен создаваться экземпляр сущности. `RemoveMe` Указывает, что элемент следует полностью удалить во время выполнения.

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

Время монеты использует шаблон сущности для своих игр объектов (который рассматривается в [руководство по сущности в CocosSharp](~/graphics-games/cocossharp/entities.md)). Все сущности являются производными от `CCNode`, что означает, что они добавляются как дочерние элементы `gameplayLayer`.

Каждый тип сущности также указывается напрямую через список или одного экземпляра. Например `Player` ссылается `player` поля и все `Coin` экземпляры указываются в `coins` списка. Сохранение прямые ссылки на сущности (в отличие от создания ссылок на них через `gameLayer.Children` списка) позволяет выполнять код, который обращается к эти сущности более удобными для чтения и позволяет избежать потенциально затратной тип приведения.

Существующий код предоставляет несколько типов сущностей в качестве примеров того, как создавать новые сущности. Чтобы создать новую сущность можно использовать следующие действия:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1 — Определение нового класса, используя шаблон сущности

Единственным требованием для создания сущности является создание класса, который наследуется от `CCNode`. Большинство сущностей имеют некоторые визуальный элемент, такой как `CCSprite`, который необходимо добавить в качестве дочернего объекта в его конструктор.

Предоставляет CoinTime `AnimatedSpriteEntity` класс, который упрощает создание анимированных сущностей. Анимация будет рассматриваться более подробно в [раздела анимировано сущностей](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 — добавьте новую запись в блоке switch TryCreateEntity

Надо создавать экземпляры новой сущности в `TryCreateEntity`. Если сущность требует логики каждый кадр как конфликт, искусственного Интеллекта или чтения входных данных, а затем `GameScene` необходимо хранить ссылку на объект. Если требуется несколько экземпляров (такие как `Coin` или `Enemy` экземпляров), затем новый `List` должны добавляться к `GameScene` класса.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 — изменение свойства мозаики новой сущности

После кода поддерживает создание новой сущности, новая сущность должен быть добавлен к tileset. Tileset можно изменить, открыв любого уровня `.tmx` файл. 

Tileset хранится отдельно от .tmx в **mastersheet.tsx** файла, поэтому он должен быть импортирован, прежде чем его можно изменить:

![](cointime-images/image11.png "Tileset хранится отдельно от файла .tsx, поэтому он должен быть импортирован, прежде чем его можно редактировать")

После импорта свойства выбранных плиток можно редактировать, а тип EntityType могут быть добавлены:

![](cointime-images/image12.png "После импорта свойства выбранных плиток можно редактировать, а можно добавить EntityType")

После создания свойства его значение можно задать в соответствии с новой `case` в `TryCreateEntity`:

![](cointime-images/image13.png "После создания свойства его значение может быть присвоено совпадает с регистром новый TryCreateEntity")

После изменения tileset, его необходимо экспортировать – это сделает изменения доступными для всех уровней:

![](cointime-images/image14.png "После изменения tileset, его необходимо экспортировать")

Перезаписать существующую tileset **mastersheet.tsx** tileset:

![](cointime-images/image15.png "Перезаписать существующий tileset mastersheet.tsx HE tileset")


## <a name="entity-tile-removal"></a>Удаление плитки сущности

При загрузке мозаики карты в игру, отдельные плитки — это статические объекты. Поскольку сущности требуется пользовательское поведение, такие как перемещение, кода во время монеты удаляет плитки при создании сущности.

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

Это автоматическое удаление плиток достаточно для сущностей, которые занимают только одна Плитка в tileset, такие как монеты и противников. Больших сущностей требуется дополнительная логика и свойства.

Дверца требуются две плитки для отображения полностью:

![](cointime-images/image16.png "Дверца требуется две плитки для отображения полностью")

На плитке нижней в дверь содержит свойства для создания сущности (**EntityType** присвоено **дверь**):

![](cointime-images/image17.png "На плитке нижней в дверь содержит свойства для создания сущности EntityType, имеющим в дверь")

Так как только на нижней плитку в дверь удаляется при создании экземпляра двери, дополнительной логики необходим для удаления в первом элементе, во время выполнения. Top Плитка содержит **RemoveMe** свойство значение **true**:

![](cointime-images/image18.png "В первом элементе RemoveMe свойство имеет значение true")



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

Сущности, созданные из плиток располагаются путем выравнивания center сущности с помощью центра плитки. Большего размера сущности, такие как `Door`, использовать дополнительные свойства и логика должна быть помещена правильно. 

Плитке двери, нижней, определяющий `Door` указывает размещение сущности **смещение** значение 4. Без этого свойства `Door` экземпляр помещается в центре плитки:

![](cointime-images/image19.png "Без этого свойства экземпляра дверцу помещается в центре плитки")

 

Это исправляется путем применения **смещение** значение в `ProcessTileProperties`:


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

Время монеты включает несколько анимированных сущностей. `Player` И `Enemy` сущностей воспроизводить анимацию стека и `Door` сущности играет анимации после собраны все монеты.


### <a name="achx-files"></a>файлы .achx

В файлах .achx определяются монеты время анимации. Каждой анимации определяется между `AnimationChain` тегов, как показано в следующем анимации, определенные в **propanimations.achx**:


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

Эта анимация содержит только один кадр, приводит к сущности пик, отображение статическое изображение. Сущности можно использовать файлы .achx ли они отображают одним или несколькими кадрами анимации. Дополнительные кадры могут добавляться к файлам .achx без каких-либо изменений в коде. 

Кадры определить какие изображения для отображения в `TextureName` параметра и координаты, отображаемое в `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, и `BottomCoordinate` теги. Они представляют собой координаты точки кадра анимации, в которой используется — **mastersheet.png** в данном случае.

![](cointime-images/image20.png "Они представляют собой координаты точки кадра анимации в образе")

`FrameLength` Свойство определяет количество секунд, которые должны отображаться кадр анимации. Это значение будет игнорироваться однокадровой анимации.

Все остальные свойства AnimationChain в файле .achx игнорируются монеты времени.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Анимация логика содержится в `AnimatedSpriteEntity` класс, который служит в качестве базового класса для большинства объектов, используемой в `GameScene`. Эта служба предоставляет следующие возможности:

 - Загрузка `.achx` файлов
 - Анимация кэша загруженных анимаций
 - Экземпляр CCSprite для отображения анимации
 - Логика для изменения текущего кадра

Конструктор пики приведен простой пример того, как загрузить и использовать анимации:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx** содержит только одна анимация, поэтому конструктор обращается к данной анимации по индексу. Если файл .achx содержит несколько анимаций, а затем анимации можно ссылаться по имени, как показано в `Enemy` конструктор:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Сводка

Данное руководство содержит сведения о времени монеты реализации. Время монеты создается как полный игру, но также является проектом, который можно легко изменить и развернуть. Читатели, рекомендуется потратить время внесением изменений уровней, добавление новых уровней и создание новых сущностей, чтобы лучше разобраться, как реализуется монеты времени.

## <a name="related-links"></a>Связанные ссылки

- [Игровой проект (пример)](https://developer.xamarin.com/samples/mobile/CoinTime/)
