---
title: С помощью мозаикой с CocosSharp
description: Мозаичное заполнение мощный, гибкий и сопоставляет отлаженное приложение для создания плитки ортогональных и изометрическая игр для устройств. CocosSharp предоставляет встроенной интеграции для копиями в собственном формате.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 8a7782097324829b8150b968c5658a864d1fab4a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921304"
---
# <a name="using-tiled-with-cocossharp"></a>С помощью мозаикой с CocosSharp

_Мозаичное заполнение мощный, гибкий и сопоставляет отлаженное приложение для создания плитки ортогональных и изометрическая игр для устройств. CocosSharp предоставляет встроенной интеграции для копиями в собственном формате._

*Копиями* приложения — это стандарт для создания *плитки карт* для использования при разработке игры. Это руководство посвящено берется существующий файл .tmx (файл, созданный копиями) и использовать его в игре CocosSharp. В частности, в этом руководстве рассматривается:

 - Назначение плитки карт
 - Работа с файлами .tmx
 - Рекомендации для отрисовки рисунка пикселей
 - С помощью свойства мозаичного во время выполнения

При завершении у нас будет следующие демонстрации:

![](tiled-images/image1.png "Создать, выполнив действия, описанные в этом руководстве нашего примера приложения")


## <a name="the-purpose-of-tile-maps"></a>Назначение плитки карт

Плитки карт существуют в разработки игр для десятилетия, но по-прежнему часто применяются в игр 2D их эффективность и esthetics. Плитки карт имеют возможность достичь очень высокую эффективность с помощью их использование наборов плитки — используемый плитки соответствует исходному изображению. Плитка набор представляет собой коллекцию изображений, объединенные в один файл. Несмотря на то, что наборы плитки ссылаются изображений, используемых в сопоставлениях плитки, файлы, содержащие несколько небольших образов, также называются sprite листов или sprite карты в разработки игр. Можно представить как наборы плитки используются в добавление к набору плитки, мы будем использовать в наших демонстрации сетки:

![](tiled-images/image2.png "Визуализируемого представления использования наборов плитки, добавление сетки плитки набор, который будет использоваться в демонстрации")

Плитки карт размещения отдельных плиток из наборов плитки. Следует отметить, что каждая карта плитки не нужно сохранить собственную копию плитки задать — вместо нескольких карт плитки могут ссылаться на один и тот же набор плитки. Это означает, что помимо наборе плитки плитки maps требуют очень мало памяти. Это позволяет создавать большое количество плитки карт, даже в том случае, если они используются для создания для области больших игры, например [прокрутка platformer](http://en.wikipedia.org/wiki/Platform_game) среды. Ниже приведен возможных порядков, используя один и тот же набор плитки.

![](tiled-images/image3.png "На данном рисунке показан возможных порядков, используя один и тот же набор плитки")

![](tiled-images/image4.png "На данном рисунке показан возможных порядков, используя один и тот же набор плитки")


## <a name="working-with-tmx-files"></a>Работа с файлами .tmx

Формат файла .tmx — это файл XML, созданных приложением копиями, который может быть [для бесплатной загрузки на веб-сайте копиями](http://www.mapeditor.org/). Формат файла .tmx хранит сведения для плитки карт. Обычно игра будет иметь один файл .tmx для каждой отдельной или уровня области.

В этом руководстве основное внимание уделено использовать существующие файлы .tmx в CocosSharp; Однако другие учебники можно найти в Интернете, включая [вводном разделе о редакторе карты копиями](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Необходимо распаковать [содержимого ZIP-файл](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) ее использования в нашем игры. Первый важно отметить, что плитки карт использовать оба .tmx файла (dungeon.tmx), а также одно или дополнительные файлы изображений, которые определяют плитки набор данных (dungeon_1.png). Игры должно включать оба этих файлов для загрузки .tmx во время выполнения, поэтому добавьте оба в проект **содержимого** папку (который содержится в **активы** папку проектов Android). В частности, добавить файлы в папку с именем **tilemaps** внутри **содержимого** папки:

![](tiled-images/image5.png "Добавить файлы в папку с именем tilemaps внутри содержимого папки")

**Dungeon.tmx** теперь можно загрузить файл во время выполнения в `CCTileMap` объекта. Затем измените `GameLayer` (или эквивалентный корневого контейнера объекта) содержать `CCTileMap` экземпляра и добавить его в качестве дочернего:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Если мы запустим игры, которую мы увидим карты плитки отображаются в левом нижнем углу экрана:

![](tiled-images/image6.png "При запуске игры карты плитки отображаются в левом нижнем углу экрана")


## <a name="considerations-for-rendering-pixel-art"></a>Рекомендации для отрисовки рисунка пикселей

Искусство пикселей, в контексте разработки игровые ссылается 2D visual рисунка, который обычно создается при наличии и часто является низкое разрешение. Рисунок пиксель может быть restrictively времени для создания, поэтому наборы плитку рисунка пикселей часто содержат плитки с низким разрешением, например 16 или 32 пикселя ширину и высоту. Не масштабируется во время выполнения, пиксель изображения часто является слишком мало для большинства современных телефоны и планшетные ПК.

Можно настроить отображенные аналитики в файле GameAppDelegate.cs нашей игры, где мы добавим вызов `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Дополнительные сведения о `CCSceneResolutionPolicy`, см. в нашем руководстве [обработки разрешения в CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Теперь запускаем игры мы рассмотрим игры величиной устройство во весь экран:

![](tiled-images/image7.png "Игры взять полный экран устройства")

Наконец мы потребуется отключить сглаживания на нашем плитки карты. `Antialiased` Свойство применяет размывание эффект при обработке объектов, которые с увеличением. Сглаживание можно использовать для уменьшения некачественно вида графических объектов, но также может внести свой собственный артефакты для подготовки к просмотру. В частности сглаживания размывает содержимое каждого элемента мозаики. Однако границы каждого элемента мозаики не размываются, заставляя квадратики выделялись вместо наложения с рядом плитки. Также следует отметить, что пиксель изображения игры часто сохранять их внешний вид некачественно для поддержания *ретро* чувствовать себя.

Задать `Antialiased` для `false` после построения `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Теперь наши плитки карты не будет отображаться нечетким:

![](tiled-images/image8.png "Теперь плитки карты не будет отображаться нечетким")


## <a name="using-tile-properties-at-runtime"></a>С помощью свойства мозаичного во время выполнения

Пока у нас есть `CCTileMap` Загрузка файла .tmx и отображение его, но мы не имеют возможности взаимодействия с ней. В частности некоторые плитки (например, нашей грудной должным образом) должны иметь настраиваемую логику. Мы будем пошагово проходить по обнаружению свойства настраиваемые плитки и различные способы реагирования на эти свойства, определенные один раз во время выполнения.

Прежде чем мы писать код, нам нужно будет добавлять свойства к нашей плитки карты через копиями. Чтобы сделать это, откройте файл dungeon.tmx в программе копиями. Необходимо открыть файл, который используется в проекте игры.

Когда открыт, выберите грудной должным образом на плитке, чтобы просмотреть его свойства:

![](tiled-images/image9.png "После открытия выберите грудной должным образом на плитке, чтобы просмотреть его свойства")

Если свойства грудной должным образом не отображаются, щелкните правой кнопкой мыши на грудной должным образом и выберите **свойства мозаичного**:

![](tiled-images/image10.png "Если свойства грудной должным образом не отображаются, щелкните правой кнопкой мыши на грудной должным образом и выберите свойства мозаичного")

Мозаичная свойства реализуются с именем и значением. Чтобы добавить свойство, нажмите кнопку **+** кнопку, введите имя **IsTreasure**, нажмите кнопку **ОК**, затем введите значение **true**: 

![](tiled-images/image11.png "Чтобы добавить свойство, нажмите кнопку, введите имя IsTreasure, нажмите кнопку ОК, а затем введите значение true")

Не забудьте сохранить файл .tmx после изменения свойств.

Наконец мы добавим код для поиска для наших добавляются свойства. Мы перебирает каждый `CCTileMapLayer` (нашей карта содержит 2 слоев), затем по каждой строки и столбца для поиска любой плитки, которые имеют `IsTreasure` свойство:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

Большая часть кода описательные имена, но следует рассмотрена обработка плиток должным образом. В этом случае мы удаляем любой плитки, которые определены как ним должным образом. Это потому, что пользовательский код во время выполнения влияет конфликтов, а также вознаграждения проигрыватель содержимого должным образом, при открытии скорее всего, потребуется ним должным образом. Кроме того, может потребоваться должным образом реагировать на время открыт (изменения внешнего вида) и, возможно, логику для только отображаться при всех враги была отменена.

Другими словами, грудной должным образом будет обеспечен выполняется сущности, а не простой плитки в `CCTileMap`. Дополнительные сведения о сущностях игры см. в разделе [руководства сущностей в CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Сводка

В этом пошаговом руководстве описывается, как загрузить .tmx файлы, созданные в ходе копиями в CocosSharp приложение. Показано, как изменить разрешения приложения с учетом art пикселей с низким разрешением и как искать плитки, их свойства, чтобы реализовать пользовательскую логику, таких как создание экземпляров сущности.

## <a name="related-links"></a>Связанные ссылки

- [Мозаичная веб-сайта](http://www.mapeditor.org/)
- [Содержимого zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
