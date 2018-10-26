---
title: Использование Tiled с CocosSharp
description: Мозаичная мощный, гибкий и сопоставляет отлаженное приложение для создания плитки самостоятельной и изометрической для игр. CocosSharp предоставляет встроенную интеграцию для копиями в собственном формате.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4582b59a8a441c9e22761d498126898e66db08c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117932"
---
# <a name="using-tiled-with-cocossharp"></a>Использование Tiled с CocosSharp

_Мозаичная мощный, гибкий и сопоставляет отлаженное приложение для создания плитки самостоятельной и изометрической для игр. CocosSharp предоставляет встроенную интеграцию для копиями в собственном формате._

*Копиями* приложения — это стандарт, для создания *плитку карты* для использования в разработке игр. В этом руководстве показано, как как существующий файл .tmx (файл, созданный копиями) и использовать его в CocosSharp игры. В частности, в этом руководстве рассматривается:

 - Цель мозаики карты
 - Работа с файлами .tmx
 - Рекомендации для подготовки к просмотру art пикселей
 - С помощью свойства мозаики во время выполнения

При завершении у нас есть следующие демонстрации:

![](tiled-images/image1.png "Демонстрационное приложение, созданные в рамках действия, описанные в этом руководстве")


## <a name="the-purpose-of-tile-maps"></a>Цель мозаики карты

Плитка карты существуют в разработке игр в течение десятилетий, но до сих пор широко используются в двухмерных игр для их эффективности и esthetics. Плитка карты являются смогли достичь очень высокий уровень эффективности работы за счет их использования наборов плитки — исходный образ, используемый соответствует плитки. Набор плитки — это набор образов, объединены в один файл. Несмотря на то, что наборы плитки ссылки на образы, используемые в плитке maps, файлы, содержащие несколько небольших изображений, также называются спрайт листы или спрайт карты в разработке игр. Можно представить как плитку наборы используются, добавив сетку в набор плитки, мы будем использовать в нашей демоверсией в:

![](tiled-images/image2.png "Визуализированный представление использования наборов плитки, добавление сетки в набор плитки, который будет использоваться в демонстрационной версии")

Плитка карты упорядочить отдельные плитки из наборов плитки. Следует отметить, что каждая Плитка карта не требуется сохранить собственную копию плитку set – вместо этого несколько карт плитки могут ссылаться на один и тот же набор плитки. Это означает, что помимо набора плитки, Плитка maps требуют очень мало памяти. Это позволяет создавать большое количество карт плитки, даже в том случае, если они используются для создания для области больших игры, например [прокрутка платформер](http://en.wikipedia.org/wiki/Platform_game) среды. Ниже показан возможных порядков, используя один и тот же набор плитки.

![](tiled-images/image3.png "На этом изображении показаны возможных порядков, используя один и тот же набор плитки")

![](tiled-images/image4.png "На этом изображении показаны возможных порядков, используя один и тот же набор плитки")


## <a name="working-with-tmx-files"></a>Работа с файлами .tmx

Формат файла .tmx — это файл XML, созданных приложением копиями, который может быть [для бесплатной загрузки на веб-сайте копиями](http://www.mapeditor.org/). Формат файла .tmx хранит данные для плитки карты. Обычно это игра будет иметь один файл .tmx для каждой области уровня или за его пределами.

В этом руководстве описано, как использовать существующие файлы .tmx в CocosSharp; Однако дополнительные руководства можно найти в Интернете, включая [это введение в редактор карты копиями](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Вам нужно будет распаковать [содержимого ZIP-файл](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) его использования в нашей игре. Прежде всего следует отметить, — что плитки карты использовать оба .tmx файл (dungeon.tmx), а также один или несколько файлов изображений, которые определяют плитку набор данных (dungeon_1.png). Игры должно включать оба файла для загрузки .tmx во время выполнения, поэтому необходимо добавить оба проекта **содержимого** папку (содержащийся в **активы** папку проектов Android). В частности, добавьте файлы в папку с именем **tilemaps** внутри **содержимого** папки:

![](tiled-images/image5.png "Добавьте файлы в папку с именем tilemaps внутри содержимого папки")

**Dungeon.tmx** теперь можно загрузить файл во время выполнения в `CCTileMap` объекта. Затем измените `GameLayer` (или эквивалентные корневом объекте-контейнере) должен содержать `CCTileMap` экземпляра и добавить его как дочерний элемент:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Запускаем игры, которую мы увидим карты плитки отображаются в нижнем левом углу экрана:

![](tiled-images/image6.png "Если игра запускается, карты плитки отображаются в нижнем левом углу экрана")


## <a name="considerations-for-rendering-pixel-art"></a>Рекомендации для подготовки к просмотру art пикселей

Искусство пикселей, в контексте Разработка компьютерных игр, относится к 2D visual картинок, который обычно создается путем рука об руку и часто низким разрешением. Рисунок пиксель может быть так узко времени для создания, поэтому наборы плитки art пикселей часто включают в себя с низким разрешением плитки, например 16 или 32 пикселей ширины и высоты. Если не масштабируется во время выполнения, пикселей изображения часто слишком мал для большинства современных телефонах и планшетах.

Можно настроить отображенные аналитики в файле GameAppDelegate.cs нашей игре, где мы добавим вызов `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Дополнительные сведения о `CCSceneResolutionPolicy`, приведены в нашем руководстве на [обработки разрешений в CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Если мы теперь запустить игру, мы увидим игр take на полный экран наше устройство:

![](tiled-images/image7.png "Игры величиной во весь экран устройства")

Наконец, нам понадобятся для отключения сглаживания на карте наши плитки. `Antialiased` Свойство применяется эффект размывание при отрисовке объектов, которые с увеличением. Сглаживание можно использовать для уменьшения размытыми внешнего вида графических объектов, но также может вызвать свой собственный отрисовки артефактов. В частности сглаживания размывает содержимое каждой плитки. Тем не менее края каждого элемента мозаики не размываются, что делает отдельные плитки выделиться вместо наложения с использованием соседних плитки. Также следует отметить, что пиксель art игры часто сохранить их внешний вид размытыми для поддержания *ретро* кажется.

Задайте `Antialiased` для `false` после построения `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Теперь наш элемент карты не будут отображаться нечетким:

![](tiled-images/image8.png "Теперь элемент карты не будет отображаться нечетким")


## <a name="using-tile-properties-at-runtime"></a>С помощью свойства мозаики во время выполнения

У нас `CCTileMap` Загрузка файла .tmx и отображение, но мы не можем для взаимодействия с ним. В частности отдельные плитки (например, наши грудной сокровищ) должны иметь настраиваемую логику. Пройдем через по обнаружению свойства настраиваемые плитки и различные способы реагирования на эти свойства, определяемого один раз во время выполнения.

Прежде чем мы написания кода, необходимо добавить свойства для нашей карты плитки через копиями. Чтобы сделать это, откройте файл dungeon.tmx в программе копиями. Не забудьте открыть файл, который используется в проекте игр.

После открытия выберите грудной сокровищ на плитке, набор данных для просмотра его свойств:

![](tiled-images/image9.png "После открытия выберите грудной сокровищ на плитке, набор данных для просмотра его свойств")

Если свойства грудной сокровищ не отображаются, щелкните правой кнопкой мыши на грудной сокровищ и выберите **свойства мозаики**:

![](tiled-images/image10.png "Если свойства грудной сокровищ не отображаются, щелкните правой кнопкой мыши на грудной сокровищ и выберите свойства мозаики")

Мозаичный свойства реализуются с помощью имени и значения. Чтобы добавить свойство, нажмите кнопку **+** , введите имя **IsTreasure**, нажмите кнопку **ОК**, введите значение **true**: 

![](tiled-images/image11.png "Чтобы добавить свойство, нажмите кнопку, введите имя IsTreasure, нажмите кнопку ОК, а затем введите значение true")

Не забудьте сохранить файл .tmx после изменения свойств.

Наконец мы добавим код для поиска наших новых свойств. Мы перебирает каждый `CCTileMapLayer` (нашу карту имеет 2 слои), затем через каждой строки и столбца для поиска любой плитки, которые имеют `IsTreasure` свойство:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

Большая часть кода не нуждается в объяснении. Однако мы обсудить обработки сокровищ плиток. В этом случае мы удаляем любые элементы, которые определены как сокровищ набивать себе цену. Это обусловлено набивать себе цену сокровищ скорее всего, понадобится пользовательский код во время выполнения, в силу конфликтов и вознаграждения проигрыватель содержимого сокровищ при открытии. Кроме того, может потребоваться сокровищ реагировать на действия, открыт (Изменение внешнего вида) и, возможно, логику для только отображаться при всех противников было отменено.

Другими словами, грудной сокровищ выиграет от выполняется сущности, а не простая Плитка в `CCTileMap`. Дополнительные сведения о сущностей game, см. в разделе [руководство по сущности в CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Сводка

В этом пошаговом руководстве описывается, как загрузить .tmx файлов, созданных копиями в CocosSharp приложение. Он показывает, как изменить разрешения приложения с низким разрешением пикселей изображения и как найти плитки по их свойства, чтобы реализовать пользовательскую логику, таких как создание экземпляров сущности.

## <a name="related-links"></a>Связанные ссылки

- [Мозаичный веб-сайта](http://www.mapeditor.org/)
- [Содержимым zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
