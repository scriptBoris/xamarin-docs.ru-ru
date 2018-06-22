---
title: Производительность и визуальные эффекты с CCRenderTexture
description: CCRenderTexture позволяет разработчикам повысить производительность игр CocosSharp путем уменьшения вызовов draw и может использоваться для создания визуальных эффектов. В этом руководстве приводится образец CCRenderTexture для предоставления практический пример того, как эффективно использовать этот класс.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 82d39542b24c6b1669798a0b4e1fb14a81f6b44e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922526"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Производительность и визуальные эффекты с CCRenderTexture

_CCRenderTexture позволяет разработчикам повысить производительность игр CocosSharp путем уменьшения вызовов draw и может использоваться для создания визуальных эффектов. В этом руководстве приводится образец CCRenderTexture для предоставления практический пример того, как эффективно использовать этот класс._

`CCRenderTexture` Класс предоставляет функциональные возможности для подготовки к просмотру несколько объектов, CocosSharp одну текстуру. После создания `CCRenderTexture` экземпляров может использоваться для эффективной визуализации графики и реализации визуальные эффекты. `CCRenderTexture` позволяет несколько объектов, который отображается на одну текстуру один раз. То, что текстуры может быть повторно использован для каждого кадра, уменьшая общее число вызовов draw.

В этом руководстве рассматривается использование `CCRenderTexture` объекта для повышения производительности отрисовки карты в подлежащего сбору карточных играх (CCG). Он также демонстрирует, как `CCRenderTexture` можно использовать для прозрачной всей сущности. В этом руководстве ссылается `CCRenderTexture` [образец проекта](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "В этом руководстве ссылается CCRenderTexture образца проекта")


## <a name="card--a-typical-entity"></a>Карта — типичный сущности

Перед рассмотрением способы использования `CCRenderTexture` объекта, мы будем сначала ознакомиться сами с `Card` сущности, мы будем использовать на протяжении этого проекта для изучения `CCRenderTexture` класса. `Card` Класс является сущностью типичный шаблон сущности, описанные в следующих [руководство по сущности](~/graphics-games/cocossharp/entities.md). Карта класс имеет все ее компоненты visual (экземпляры `CCSprite` и `CCLabel`) в списке полей:


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

Карта экземпляров может осуществляться с помощью `CCRenderTexture`, или путем рисования каждого компонента visual по отдельности. Несмотря на то, что каждый компонент является независимым объектом `CCNode` родительские связи в системе, используемой в сущности делает `Card` ведут себя как единый объект — по крайней мере в большинстве случаев. Например если `Card` сущности является перемещать, поворачивать, увеличивать и затем затронуто содержащихся визуальных объектов для карточки отображаются как один объект. Для просмотра карты, которые ведут себя как один объект, можно изменить `GameLayer.AddedToScene` метод, чтобы задать `useRenderTextures` переменной `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer` Кода не перемещает каждый визуальный элемент независимо друг от друга, еще каждого визуального элемента в пределах `Card` правильно расположен сущности:

![](ccrendertexture-images/image1.png "Код GameLayer не перемещает каждый визуальный элемент независимо друг от друга, но неправильно расположены каждого визуального элемента в пределах сущности карты")

Образец кодируется для предоставления две проблемы, которые могут возникнуть при каждой визуальный компонент отображает сам себя:

- Производительность может снизиться из-за несколько вызовов draw
- Некоторые визуальные эффекты, как прозрачность, не может быть реализован точно, как мы рассмотрим позже


### <a name="card-draw-calls"></a>Вызовы draw карты

Наш код — это упрощение что могут быть найдены в полной *подлежащего сбору игра* (CCG), такие как «Сбор: Magic» или «Hearthstone». Наши игры только за один раз отображает три карты и имеет небольшое количество возможных единиц (синий зеленым и оранжевым цветом). Напротив полный игры, возможно, более чем двадцати карты на экране в определенный момент времени и проигрыватели, возможно, сотни карты выбор при создании их наборы. Несмотря на то, что наши игры не страдают от проблем с производительностью, могут иметь полный игры с аналогичную реализацию.

CocosSharp дает некоторое представление о производительности отрисовки, предоставляя вызовов draw кадров. Наш `GameLayer.AddedToScene` метода задает `GameView.Stats.Enabled` для `true`, что может привести производительности сведений, отображаемых в левой нижней части экрана:

![](ccrendertexture-images/image2.png "Метод GameLayer.AddedToScene задает GameView.Stats.Enabled значение true, что приводит к производительности сведений, отображаемых в левой нижней части экрана")

Обратите внимание, что несмотря на наличие трех карт на экране, мы девятнадцать вызовов draw (результаты каждой карты в шесть вызовы draw, текста, отображение один дополнительные учетные записи сведения о производительности). Вызовы Draw имеют значительное влияние на производительность игры, поэтому CocosSharp предоставляет ряд способов снижения их. Один из способов описан в [CCSpriteSheet руководство](~/graphics-games/cocossharp/ccspritesheet.md). Другой способ состоит в использовании `CCRenderTexture` для уменьшения до одного вызова каждой сущности, как будет рассмотрен в данном руководстве.


### <a name="card-transparency"></a>Прозрачность карты

Наш `Card` сущность включает `Opacity` свойства прозрачности элемента управления, как показано в следующем фрагменте кода:


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

Обратите внимание, что метод установки поддерживает использование отрисовки текстур или по отдельности отрисовки каждого компонента. Чтобы просмотреть его результат, измените `opacity` значение `127` (примерно половину непрозрачность) в `GameLayer.AddedToScene` что приведет к необходимости каждый компонент `Opacity` значение `127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

Игра теперь будет отображен карты с некоторыми прозрачности, к ним темнее, так как фоновый — черный:

![](ccrendertexture-images/image3.png "Игра теперь будет отображаться карты с некоторыми прозрачности, к ним темнее, так как фоновый — черный")

На первый взгляд он может выглядеть, как если бы их карты были правильно внесены прозрачным. Тем не менее снимок экрана отображается ряд проблем visual.

Поскольку наш фоновый — черный, планируется, что каждая часть наших карты станут более темные из-за прозрачность. Таким образом становится более прозрачным карту, чем темнее становится. В прозрачность 0 `Card` будут полностью прозрачными (полностью черный). Тем не менее, некоторые части нашей карты не становятся более темные при изменении непрозрачности для `127`. Более того некоторые части нашей карты фактически стали светлый при они стали более прозрачным. Давайте взглянем на компоненты нашей карты, были черным *перед* прозрачными — в частности текст сведений и черным контуров график сред. Если мы разместить их на одном компьютере, мы увидим, влияние на применение прозрачности:

![](ccrendertexture-images/image4.png "Если поместить рядом друг с другом, можно увидеть влияние применение прозрачности")

Как упоминалось выше, все части карточки станет темнее, когда становится более прозрачным, но в нескольких областях это не так:

- Структура робота становится светлее (выходит из черный серый)
- Текст описания становится светлее (выходит из черный серый)
- Зеленого компонента робота становится менее пропускная способность, но не настолько темнее

Чтобы помогают визуализировать, почему это происходит, нужно помнить, что каждый компонент visual формируемого независимо друг от друга, каждая частично прозрачный. Первый компонент visual рисования является фона карты. Последующие прозрачные элементы будут отображаться поверх карт и будет затронут фон карты. Если удалить часть текста из наших карты и вниз график робот, мы видим, как робота влияет на фон карты. Обратите внимание, оранжевый строки в верхнем поле можно просмотреть на робота, и что выводится более темные области робот, который перекрывает синей полосой в центре карты:

![](ccrendertexture-images/image5.png "Обратите внимание, оранжевый строки в верхнем поле можно просмотреть на робота и что выводится более темные области робот, который перекрывает синей полосой в центре карты")

С помощью `CCRenderTexture` мы сможем прозрачной всей карты без ущерба для подготовки к просмотру отдельных компонентов внутри карточки, как будет показано далее в этом руководстве.


## <a name="using-ccrendertexture"></a>С помощью CCRenderTexture

Теперь, когда мы определили проблемы с по отдельности отрисовки каждого компонента, мы перейдем на подготовки к просмотру `CCRenderTexture` и сравните поведение.

Включение подготовки к просмотру `CCRenderTexture`, изменить `userRenderTextures` переменной `true` в `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Вызовы draw карты

Теперь запускаем игры мы рассмотрим вызовов draw, в отличие от 19 до четырех (каждая карта reduced из шести одно):

![](ccrendertexture-images/image6.png "Если сейчас запустить игры, вызовы draw сокращение от 19 до четырех каждой карточке ограниченной из шести к одному")

Как упоминалось ранее этот тип сокращения может иметь значительное влияние на игр с помощью нескольких visual сущностей на экране.


### <a name="card-transparency"></a>Прозрачность карты

Один раз `useRenderTextures` равно `true`, прозрачный карты будет отображаться по-разному:

![](ccrendertexture-images/image7.png "Как только useRenderTextures задается значение true, прозрачный карты будет отображаться по-разному")

Сравним карту прозрачный робот, с помощью визуализации текстуры (слева) и без (справа):

![](ccrendertexture-images/image8.png "Сравнение карту прозрачный робот, с помощью отрисовки текстур (слева) и без (справа)")

Наиболее очевидным различия находятся в текст сведений (черный вместо светло-серым цветом) и спрайта робот (темный вместо свет и насыщенным).


## <a name="ccrendertexture-details"></a>Сведения о CCRenderTexture

Теперь, когда мы рассмотрели преимущества использования `CCRenderTexture`, давайте взглянем на как она используется в `Card` сущности.

`CCRenderTexture` Холст, который может быть целевым объектом отрисовки. Он имеет два важных отличия по сравнению с экрана игры.

1. `CCRenderTexture` Сохраняется между открывающим кадры. Это означает, что `CCRenderTexture` должен передаваться только при внесении изменений. В нашем случае `Card` никогда не изменит сущности, таким образом, он отображается только один раз. При наличии `Card` изменить компоненты, то требуется перерисовывает себя для карточки ее `CCRenderTexture`. Например если значение HP (работоспособности точки) изменяется при атаке, карточки необходимо для подготовки к просмотру в соответствии с новым значением HP.
1. `CCRenderTexture` Пикселах не связаны с экрана. Объект `CCRenderTexture` может быть больше или меньше разрешения устройства. `Card` Код создает `CCRenderTexture` с размером его sprite фона. Карта содержит ссылку на `CCRenderTexture` вызывается `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture` Экземпляра остается `null` до `UseRenderTexture` присваивается значение true, который вызывает `SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

`SwitchToRenderTexture` Метод может вызываться всякий раз, когда необходимо обновить текстуры. Он может быть вызван ли карта уже использует его `CCRenderTexture` или перейти на `CCRenderTexture` в первый раз.

Следующие разделы посвящены `SwitchToRenderTexture` метод. 


### <a name="ccrendertexture-size"></a>Размер CCRenderTexture

Конструктор CCRenderTexture требует два набора измерений. Первый управляет размером `CCRenderTexture` при прорисовкой, а второй указывает пикселей ширину и высоту его содержимое. `Card` Создает экземпляр сущности его `CCRenderTexture` использование фона [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Наш игры `DesignResolution` 512, 384, как показано в `ViewController.LoadGame` в iOS и `MainActivity.LoadGame` на Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture` Конструктор вызывается с `background.ContentSize` как первый параметр указывает, что `CCRenderTexture` должно быть только в качестве фона `CCSprite`. С момента фон карты `CCSprite` высотой 200 пикселей, карточки займет примерно половину высоты экрана.

Второй параметр, передаваемый `CCRenderTexture` конструктор указывает на разрешение `CCRenderTexture`. Как было сказано в [CocosSharp разрешение руководство](~/graphics-games/cocossharp/resolutions.md), ширину и высоту области можно просматривать в игры ед часто не является таким же, как на разрешение экрана. Аналогичным образом CCRenderTexture может использовать больше разрешения, чем размер, поэтому визуальные элементы отображаются четче на устройствах с высоким разрешением.

Разрешение пикселей — вдвое CCRenderTexture для предотвращения текст из привлекательных некачественно:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Для сравнения, можно изменить значение для сопоставления pixelResolution `background.ContentSize` (без удвоением) и сравните результат: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Для сравнения, можно изменить значение pixelResolution в соответствии с фоном. ContentSize без удвоением и сравните результат")


### <a name="rendering-to-a-ccrendertexture"></a>Подготовка к просмотру в CCRenderTexture

Как правило визуальных объектов в CocosSharp не подготавливаются к просмотру явным образом. Вместо этого visual объекты добавляются в `CCLayer` компоненте `CCScene`. Автоматически отображает CocosSharp `CCScene` и его визуальной иерархии, в каждом кадре без вызова кода отрисовки. 

В отличие от этого `CCRenderTexture` необходимо явно рисования в. Можно выделить этот подготовки к просмотру в три этапа:

1. `CCRenderTexture.BeginWithClear` вызывается, указывающее, что все последующие операции обработки, будут обрабатываться вызывающего `CCRenderTexture`.
1. Наследование от объектов `CCNode` (как `Card` сущности) отображаются `CCRenderTexture` путем вызова `Visit`.
1. `CCRenderTexture.End` вызывается, указывающее, что подготовки к просмотру `CCRenderTexture` завершения.

Любое число объектов могут быть визуализированы `CCRenderTexture` между его `Begin` и `End` вызовов. Перед отрисовкой, все необходимые видимые объекты добавляются как дочерние элементы:


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

`renderTexture` Необходимо части карточки при подготовке к просмотру, поэтому оно будет удалено:


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

Теперь `Card` экземпляр самого себя может отображать `CCRenderTexture` экземпляр:


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

После завершения подготовки к просмотру отдельные компоненты удаляются и `CCRenderTexture` повторно добавлены.


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>Сводка

В этом руководстве описаны `CCRenderTexture` класса с помощью `Card` сущности, который может использоваться в собираемой карточных играх. Оно было показано, как использовать `CCRenderTexture` класс, чтобы увеличить частоту кадров и правильно реализовать прозрачности всей сущности.

Несмотря на то, что в этом руководстве используется `CCRenderTexture` содержатся в сущности, этот класс может быть используется для отрисовки несколько сущностей, или даже целые `CCLayer` экземпляров для уровня экрана эффекты и улучшения производительности.

## <a name="related-links"></a>Связанные ссылки

- [Справочник по API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Полного проекта (пример)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
