---
title: Производительность и визуальные эффекты с CCRenderTexture
description: CCRenderTexture позволяет разработчикам повышать производительность игр, CocosSharp за счет сокращения вызовов draw, а также можно использовать для создания визуальных эффектов. В этом руководстве сопровождающий пример CCRenderTexture для предоставления практический пример того, как эффективно использовать этот класс.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 2e4d9a8fdefd0c543c29061da37098e443d1c092
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123119"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Производительность и визуальные эффекты с CCRenderTexture

_CCRenderTexture позволяет разработчикам повышать производительность игр, CocosSharp за счет сокращения вызовов draw, а также можно использовать для создания визуальных эффектов. В этом руководстве сопровождающий пример CCRenderTexture для предоставления практический пример того, как эффективно использовать этот класс._

`CCRenderTexture` Класс предоставляет функциональные возможности для подготовки к просмотру нескольких объектов CocosSharp в одну текстуру. После создания `CCRenderTexture` экземпляров может использоваться для эффективной визуализации графики и реализации визуальные эффекты. `CCRenderTexture` позволяет несколько объектов отображается в единый Текстурный один раз. То, что текстуры может быть повторно в каждом кадре, уменьшить общее число вызовов draw.

В этом руководстве рассматривается использование `CCRenderTexture` объекта для повышения производительности отрисовки карт в доступные для сбора карточная игра (CCG). Также он демонстрирует, как `CCRenderTexture` можно использовать для прозрачной всей сущности. В этом руководстве ссылается на `CCRenderTexture` [пример проекта](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "В этом руководстве ссылается на CCRenderTexture примера проекта")


## <a name="card--a-typical-entity"></a>Карта — это типичный сущность

Перед тем как рассмотреть способ использования `CCRenderTexture` объекта, мы сначала будем познакомить сами с `Card` сущности, который мы будем использовать на протяжении всего этого проекта, чтобы изучить `CCRenderTexture` класса. `Card` Класс представляет собой типичный объект, следующий шаблон сущности, описанные в [руководство по сущности](~/graphics-games/cocossharp/entities.md). Класс карта имеет все его компоненты visual (экземпляры `CCSprite` и `CCLabel`) в списке полей:


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

Карта экземпляров может визуализироваться с помощью `CCRenderTexture`, или путем рисования каждого компонента visual по отдельности. Несмотря на то, что каждый компонент имеет независимый объект, `CCNode` родительские связи систему, используемую в сущностях делает `Card` ведут себя как один объект — по крайней мере в большинстве случаев. Например если `Card` сущности является перемещать, поворачивать, увеличивать и, а затем будут затронуты всех содержащихся визуальных объектов, чтобы высота карточки могут быть один объект. Для просмотра карты, которые ведут себя как один объект, мы можем изменить `GameLayer.AddedToScene` метод, чтобы задать `useRenderTextures` переменной `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer` Кода не перемещает каждый визуальный элемент независимо друг от друга, пока каждый визуальный элемент в пределах `Card` сущности расположено верно:

![](ccrendertexture-images/image1.png "Код GameLayer не перемещает каждый визуальный элемент независимо друг от друга, но каждый визуальный элемент в пределах сущности карты расположено верно")

Образец написан для предоставления две проблемы, которые могут возникнуть, когда каждый визуальный компонент отрисовывается сам:

- Может наблюдаться снижение производительности из-за нескольких вызовов draw
- Некоторые визуальные эффекты, такие как прозрачность, не может быть реализован точно, как мы изучим более поздней версии


### <a name="card-draw-calls"></a>Вызовы draw карты

Наш код — это упрощение что могут быть найдены в полной *сбор карточная игра* (CCG) например, «Magic: сбор» или «Hearthstone». Нашей игре только отображает три карточки за один раз и имеет небольшое количество возможных единиц (синего, зеленого и оранжевым цветом). Напротив полной версии игры могут иметь больше двадцати карты на экране в определенный момент времени, а игроков, возможно, сотни карточек, чтобы выбрать при создании их колоды. Несмотря на то, что нашей игре не страдала от проблем с производительностью, может полной версии игры с похожая реализация.

CocosSharp предоставляет кадров выполнить некоторое представление о производительности отрисовки, предоставляя вызовов draw. Наши `GameLayer.AddedToScene` метода задает `GameView.Stats.Enabled` для `true`, полученный в приведенном в левом нижнем углу экрана сведений о производительности:

![](ccrendertexture-images/image2.png "Метод GameLayer.AddedToScene задает GameView.Stats.Enabled значение true, приводит к отображенным в левом нижнем углу экрана сведений о производительности")

Обратите внимание на то, что несмотря на наличие трех карт на экране, у нас есть девятнадцать вызовов draw (результаты каждой карты в шести вызовы draw, текста, отображение учетных записей сведения о производительности для еще одно). Вызовы Draw имеют значительное влияние на производительность игр, поэтому CocosSharp предоставляет ряд способов для сокращения их числа. Один из способов описан в [руководстве с помощью CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md). Другой способ состоит в использовании `CCRenderTexture` для уменьшения каждой сущности до одного вызова, как будет рассмотрен в данном руководстве.


### <a name="card-transparency"></a>Прозрачность карты

Наши `Card` сущность включает `Opacity` свойство прозрачности элемента управления, как показано в следующем фрагменте кода:


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

Обратите внимание на то, что метод установки поддерживает использование отрисовки текстуры или по отдельности отрисовки каждого компонента. Чтобы просмотреть его влияние, изменить `opacity` значение `127` (примерно половина непрозрачность) в `GameLayer.AddedToScene` приведет к необходимости каждый компонент `Opacity` значение `127`:


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

Игра теперь будет отображаться карты с некоторых прозрачности, вызывая темнее, так как черный фон:

![](ccrendertexture-images/image3.png "Игра теперь будет отображаться карты с некоторых прозрачности, вызывая темнее, так как черный фон")

На первый взгляд он может выглядеть так, как если бы наши карточки правильно внесены прозрачным. Тем не менее снимок экрана отображается ряд проблем визуализации.

Так как наш фона является черной, ожидается, что каждая часть нашей карты станет темнее из-за прозрачность. То есть становится более прозрачным карточки, чем темнее становится. На прозрачность 0 `Card` будет полностью прозрачным (полностью черный). Тем не менее, некоторые части нашей карты не становятся более темный оттенок, при изменении непрозрачности для `127`. Что еще хуже некоторые части нашей карты фактически стала более яркие при они стали более явными. Давайте взглянем на части нашей карты, которые были черный *перед* они были прозрачным специально текст сведений и черные границы вокруг его следующей громоздкой структуре. Если мы разместить их рядом друг с другом, мы видим, что последствия применения прозрачности:

![](ccrendertexture-images/image4.png "Если поместить рядом друг с другом, можно увидеть последствия применения прозрачности")

Как упоминалось выше, все части карты должны стать более темный оттенок, когда станут более прозрачными, но в ряде областей это не так:

- Структуры робота становится светлее (переходит от черного к серому)
- Текст описания становится светлее (переходит от черного к серому)
- Зеленого компонента робота менее переполняется, но не становится темнее

Чтобы помочь визуализировать, почему это происходит, нам нужно Имейте в виду, что каждый визуальный компонент формируемого независимо друг от друга, чтобы каждый частично прозрачный. Первый визуальный компонент рисовании является фона карты. Последующие прозрачные элементы рисуется поверх карточки и будет оказывать воздействие фона карты. Если удалить какой-либо текст из нашей карты и вниз на рисунке робота, мы видим как фон карты влияет на робота. Обратите внимание, оранжевую линию из верхнего списка можно увидеть на робота, а область робота, объединяющие добавляется синяя полоса в центре карты рисуется темнее:

![](ccrendertexture-images/image5.png "Обратите внимание, что оранжевую линию из верхнего списка можно увидеть на робота, а область робота, объединяющие добавляется синяя полоса в центре карты рисуется темнее")

С помощью `CCRenderTexture` позволяет нам сделать всей карты прозрачным без влияния на отрисовку отдельные компоненты в карточке, как мы увидим далее в этом руководстве.


## <a name="using-ccrendertexture"></a>С помощью CCRenderTexture

Теперь, когда мы определили, проблемы с отрисовки каждого компонента по отдельности, мы перейдем на подготовки к просмотру `CCRenderTexture` и сравните поведение.

Для включения подготовки к просмотру `CCRenderTexture`, изменить `userRenderTextures` переменной `true` в `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Вызовы draw карты

Если мы теперь запустить игру, мы увидим вызовов draw, в отличие от 19 до четырех (каждая карта reduced 6 к одному):

![](ccrendertexture-images/image6.png "Если теперь выполняется игры, вызовов draw сокращение от 19 до четырех каждая карта ограниченной 6 к одному")

Как упоминалось ранее этот тип сокращения может иметь значительное влияние на игр с помощью нескольких visual сущностей на экране.


### <a name="card-transparency"></a>Прозрачность карты

Один раз `useRenderTextures` присваивается `true`, прозрачного карты будет отображаться по-разному:

![](ccrendertexture-images/image7.png "После useRenderTextures присваивается значение true, прозрачного карты будет отображаться по-разному")

Давайте сравним картой прозрачного робота, используя отрисовки текстуры (слева) и без (стрелка вправо):

![](ccrendertexture-images/image8.png "Сравнение картой прозрачного робота, используя отрисовки текстуры (слева) vs без (стрелка вправо)")

Наиболее очевидная отличиями являются сведения текст (черный вместо светло-серый цвет) и спрайт робота (темно-вместо свет и насыщенным).


## <a name="ccrendertexture-details"></a>Сведения о CCRenderTexture

Теперь, когда мы увидели преимущества использования `CCRenderTexture`, давайте рассмотрим использование в `Card` сущности.

`CCRenderTexture` — Это холст, который может быть целевым объектом отрисовки. Он имеет два основных различия по сравнению с экрана игры:

1. `CCRenderTexture` Сохраняется между кадрами. Это означает, что `CCRenderTexture` необходимо допускается отрисовка только в случае, когда происходят изменения. В нашем случае `Card` никогда не изменяет сущность, так, что он отображается только один раз. При наличии `Card` изменить компоненты, а затем потребуется перерисовывает себя к карточке его `CCRenderTexture`. Например если оно HP (точки работоспособности) изменяется при атаке, карточки потребуется для подготовки к просмотру в соответствии с новым значением HP.
1. `CCRenderTexture` Пикселей не привязаны к экрану. Объект `CCRenderTexture` может быть больше или меньше, чем разрешение устройства. `Card` Код создает `CCRenderTexture` исходя из размера его спрайт фона. Карта содержит ссылку на `CCRenderTexture` вызывается `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture` Экземпляра остается `null` пока `UseRenderTexture` присваивается значение true, который вызывает `SwitchToRenderTexture`:


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

`SwitchToRenderTexture` Метод может вызываться всякий раз, когда текстура должна быть обновлена. Может быть вызвана ли карты уже использует его `CCRenderTexture` или переключаюсь на `CCRenderTexture` в первый раз.

Следующие разделы посвящены `SwitchToRenderTexture` метод. 


### <a name="ccrendertexture-size"></a>Размер CCRenderTexture

Конструктор CCRenderTexture требует два набора измерений. Первая определяет размер `CCRenderTexture` при его рисовании и второй указывает пикселей ширины и высоты, его содержимое. `Card` Создает экземпляр сущности его `CCRenderTexture` с использованием фонового [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Наши игры `DesignResolution` 512, 384, как показано в `ViewController.LoadGame` в iOS и `MainActivity.LoadGame` в Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture` Конструктор вызывается с `background.ContentSize` как первый параметр, указывающий, что `CCRenderTexture` должно быть только в качестве фона `CCSprite`. С момента фон карты `CCSprite` высотой 200 пикселей, карточка займет примерно половину высоты экрана.

Второй параметр, передаваемый `CCRenderTexture` конструктор указывает на разрешение `CCRenderTexture`. Как уже говорилось в [CocosSharp разрешение руководство](~/graphics-games/cocossharp/resolutions.md), ширину и высоту области можно просмотреть в единицах игры часто не является таким же, как на разрешение экрана. Аналогичным образом CCRenderTexture может использовать разрешение больше чем размер, поэтому визуальные элементы отображаются Соевые на устройствах с высоким разрешением.

На разрешение является вдвое CCRenderTexture во избежание текст из выглядящие размытыми:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Для сравнения, можно изменить значение pixelResolution для сопоставления `background.ContentSize` (без удвоением) и сравнить результат: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Для сравнения, можно изменить значение pixelResolution соответствовать фону. ContentSize без удвоением и сравнения результатов")


### <a name="rendering-to-a-ccrendertexture"></a>Подготовка к просмотру в CCRenderTexture

Как правило визуальных объектов в CocosSharp не подготавливаются к просмотру явным образом. Вместо этого визуальные объекты, добавляемые `CCLayer` — это является важным `CCScene`. Автоматически отображает CocosSharp `CCScene` и его визуальную иерархию, в каждом кадре без вызова кода отрисовки. 

Напротив `CCRenderTexture` необходимо явным образом производить. Такая отрисовка можно разбить на три этапа:

1. `CCRenderTexture.BeginWithClear` вызывается, указывающее, что все последующие операции обработки, будут обрабатываться вызывающего `CCRenderTexture`.
1. Объекты, наследование `CCNode` (например `Card` сущности) отображаются `CCRenderTexture` путем вызова `Visit`.
1. `CCRenderTexture.End` вызывается, указывающее, что подготовки к просмотру `CCRenderTexture` завершена.

Любое количество объектов могут быть визуализированы `CCRenderTexture` между его `Begin` и `End` вызовов. Перед отрисовкой, все необходимые видимые объекты добавляются как дочерние элементы:


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

`renderTexture` Не следует частью карточки при отрисовке, поэтому оно будет удалено:


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

Теперь `Card` экземпляр может визуализироваться на `CCRenderTexture` экземпляр:


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

После завершения подготовки к просмотру отдельные компоненты удаляются и `CCRenderTexture` добавляется повторно.


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

В этом руководстве рассматривались `CCRenderTexture` , используя `Card` сущности, которую можно использовать в собираемой карточная игра. Он показал, как использовать `CCRenderTexture` класса для повышения частоты кадров и правильно реализовать прозрачности всей сущности.

Несмотря на то, что в этом руководстве используется `CCRenderTexture` содержатся в сущности, этот класс можно использовать для подготовки к просмотру нескольких сущностей, или даже целые `CCLayer` экземпляры для экрана последствий и повышение производительности.

## <a name="related-links"></a>Связанные ссылки

- [Справочник по API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Полный проект (пример)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
