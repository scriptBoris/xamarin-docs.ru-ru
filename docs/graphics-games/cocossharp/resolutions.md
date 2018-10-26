---
title: Обработка нескольких разрешений в CocosSharp
description: В этом руководстве показано, как работать с CocosSharp для разработки игр, которые правильного отображения на устройствах в различные способы их устранения.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 6803dc2668b89ee2d037da8b34e202191dd5465d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118686"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Обработка нескольких разрешений в CocosSharp

_В этом руководстве показано, как работать с CocosSharp для разработки игр, которые правильного отображения на устройствах в различные способы их устранения._

CocosSharp предоставляет методы для стандартизации объекта измерения в игре, независимо от физического количества пикселей на экране устройства. В большинстве случаев игры, разработанные для компьютеров и игровых приставок может поддерживать единый разрешение. Современные игры — и особенно игры для мобильных устройств — должны быть построены в соответствии с самых разнообразных устройствах. В этом руководстве показано, как для стандартизации CocosSharp игры, независимо от того, разрешение характеристики физических отображения.

Разрешения по умолчанию CocosSharp — в соответствии с координатами в игре физических пикселей. В следующей таблице показаны как разные устройства умеет отрисовывать спрайт среды фона с шириной и высотой 368 x 240. Первая строка является технически не фактическом устройстве, но вместо ожидаемого отрисовку спрайта, независимо от того, разрешение устройства:


| **Устройства** | **Разрешение экрана** | **Снимок экрана с примером** |
|--- | --- |--- |
|Требуется отображать|368 x 240 пикселей (с черные полосы для пропорции)| ![368 x 240 пикселей (с черные полосы для пропорции)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960 x 640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

В этом документе описывается использование CocosSharp, чтобы исправить эту проблему, показано в приведенной выше таблице. То есть мы обсудим, как сделать любое устройство отображения, как показано в первой строке — независимо от разрешения экрана.


## <a name="working-with-setdesignresolutionsize"></a>Работа с SetDesignResolutionSize

`CCScene` Класс обычно используется как корневой контейнер для всех визуальных объектов, но он также предоставляет статический метод с именем `SetDesignResolutionSize` для указания размера по умолчанию для всех сцен. Другими словами `SetDesignResolutionSize` метод позволяет разработчикам для разработки игр, которые будут отображаться правильно на различного оборудования разрешения. Шаблоны проекта CocosSharp этот метод позволяет задать размер проекта по умолчанию 1024 x 768, как показано в следующем коде:


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
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

Вы можете изменить `desiredWidth` и `desiredHeight` переменные выше, чтобы изменить отображение в соответствии с требуемой разрешением своей игры. Например приведенный выше код можно изменить следующим образом для отображения фона 368 x 240 в виде весь экран:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` можно указать, как изменяется окна игры в нужное разрешение. В следующих разделах описано, как образ 500 x 500 отображается с разными `CCSceneResolutonPolicy` значения, передаваемые `SetDesignResolutionSize` метод. Предоставляются следующие значения `CCSceneResolutionPolicy` перечисления:

 - `ShowAll` Отображает весь запрошенное разрешение, сохранение пропорций.
 - `ExactFit` — Отображает весь запрошенное разрешение, но не сохраняет пропорции.
 - `FixedWidth` — Отображает всю ширину запрошенного, сохранение пропорций.
 - `FixedHeight` Отображает весь Запрошенная высота, сохранение пропорций.
 - `NoBorder` — Отображает всю ширину, сохранение пропорций или по всей высоте. Если пропорции устройства больше, чем пропорции нужное разрешение, будет отображаться всю ширину. Если пропорции устройства меньше, чем пропорции нужное разрешение, будет отображаться во всю высоту.
 - `Custom` — Отображение зависит от `Viewport` текущего элемента `CCScene`.

Все снимки экрана создаются с разрешением iPhone 4s (960 x 640) в альбомной ориентации и использовать этот образ:

![](resolutions-images/image4.png "Все снимки экрана создаются iPhone 4s разрешением 960 x 640 в альбомной ориентации и использовать этот образ")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Указывает, что весь игр разрешения будут видны на экране, но может отображать *фоном* (черные полосы), чтобы настроить разные пропорции. Эта политика обычно используется, как это гарантирует, что всего игр представления будут отображаться на экране без искажения.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Допустить появления пустых участков отображается слева и справа от изображения, с учетом физических пропорции, шире, чем разрешение:

![](resolutions-images/image5.png "Допустить появления пустых участков отображается слева и справа от изображения, с учетом физических пропорции, шире, чем разрешение")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Указывает, что весь игр разрешение будет отображаться на экране с не допустить появления пустых участков. Искажается видимой области (соотношение сторон может не поддерживаться) в соответствии с оборудования пропорции.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Отображается не допустить появления пустых участков, но искажается, так как разрешение устройства представляет собой прямоугольник игр представления:

![](resolutions-images/image6.png "Отображается не допустить появления пустых участков, но искажается, так как разрешение устройства представляет собой прямоугольник представлении игр")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Указывает, что ширина представления будет соответствовать ширины значение, передаваемое `SetDesignResolutionSize`, но для просмотра высота зависит от пропорции физического устройства. Значение высоты, передаваемое `SetDesignResolutionSize` игнорируется, так как она будет вычисляться во время выполнения в зависимости от физического устройства пропорции. Это означает, что вычисленную может быть меньше, чем требуемую высоту (что приводит к части игр представления, вне экрана) или вычисленную может быть больше, чем требуемую высоту (что приводит к более игр отображаемого представления). Так как это может привести больше отображаемой игры, затем может оказаться так, как если допустить появления пустых участков произошло; Тем не менее дополнительное пространство будет не обязательно будет черной, если существует любой визуальный объект. 

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s имеет соотношением сторон 3:2, поэтому вычисленную составляет приблизительно 333 единиц:

![](resolutions-images/image7.png "IPhone 4s имеет соотношением сторон 3:2, поэтому вычисленную составляет приблизительно 333 единиц")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

По существу `FixedHeight` ведет себя так же, как `FixedWidth` – игра будут соответствовать значение высоты, передаваемое `SetDesignResolutionSize,` , но вычисляет ширину во время выполнения на основе физического разрешения. Как сказано выше, это означает, что ширина отображаемого быть меньше или больше необходимую ширину, приводит к игры являются off экрана не менее игры, отображения, соответственно.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

С момента создания следующий снимок экрана с приложение, работающее в альбомной ориентации, вычисляемую ширину больше, чем 500 — в частности 750. Эта политика сохраняет значение X 0 по левому краю, поэтому дополнительные разрешения для просмотра в правой части экрана.

![](resolutions-images/image8.png "Эта политика сохраняет значение X 0 по левому краю, поэтому в правой части экрана отображается дополнительное разрешение")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` пытается отобразить приложения с не допустить появления пустых участков, сохраняя исходные пропорции (без искажений). Если запрошенное разрешение пропорции соответствует пропорции физического устройства, то произойдет отсечение. Если пропорции не совпадают, произойдет затем обрезки.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

На следующем снимке экрана отображает верхней и нижней части области отображения, обрезается, хотя отображаются все 500 пикселей в ширину отображения:

![](resolutions-images/image9.png "Этот снимок экрана отображает верхней и нижней части области отображения, обрезается, хотя отображаются все 500 пикселей в ширину отображения")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` позволяет каждой `CCScene` указать свои собственные пользовательские окна просмотра относительно разрешения, указанные в `SetDesignResolutionSize`.

Автоматически определять их `Viewport` свойство, создав `CCViewport`, который, в свою очередь, создается с `CCRect`. Дополнительные сведения о `CCViewport` и `CCRect` см. в разделе [документации по API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). В контексте создания `CCViewport` `CCRect` указать параметры конструктора (в порядке):

 - x — величина по горизонтали смещение окна просмотра, где каждая единица представляет один ширины весь экран. Например используя значение .5f результатов в сцене, сдвигаются вправо, на половину ширины экрана.
 - y — величина по вертикали смещение окна просмотра, где каждая единица представляет одну высоту весь экран. Например используя значение .5f результатов в сцене, сдвигаются на половину высоты экрана.
 - Ширина – по горизонтали, составляющей должен занимать сцены. Например, используя значение 1 / 3.0f приводит к сцене по горизонтали резервирует треть высоты экрана.
 - Высота – это вертикальная область, должен занимать сцены. Например, используя значение 1 / 3.0f приводит к сцене по вертикали резервирует треть высоты экрана.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

В приведенном выше коде приведет к следующему:

![](resolutions-images/image10.png "В приведенном выше коде результаты на этом снимке экрана")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio` Упрощает применение текстур с более высоким разрешением на устройствах с помощью экранов с высоким разрешением. В частности это свойство позволяет игры используют ресурсы с более высоким разрешением без необходимости изменения размера или размещения визуальных элементов. 

Например, игра может включать актива art для символ игры, который является 200 пикселей в высоту, а также игр экрана (как указано `SetDesignResolutionSize`) может быть 400 пикселов в высоту. В этом случае символ будет занимать половину экрана. Тем не менее если ресурс 400 пикселей высоту символа для устройств с более высоким разрешением, символ будет занимать всю высоту дисплея. Если предполагается хранить символ одинаковый размер, чтобы воспользоваться преимуществами выше разрешения устройства, дополнительный код необходимо поместить спрайта символ на половину высоты экрана.

Простой пример, представленный выше представляет часто встречающихся проблем в разработке игр — Добавление большего размера активы разработчику не приходится выполнять изменение размера для каждого визуального элемента в соответствии с разрешения устройства. `DefaultTexelToContentSizeRatio` глобальное свойство используется для изменения размеров визуальных элементов. Это значение изменяет размер все визуальные элементы определенного типа, присваиваемое значение.

Это свойство присутствует в следующие классы: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio для Mac шаблоны набора `CCSprite.DefaultTexelToContentSizeRatio` в соответствии с ширину устройства в качестве примера того, как его использовать. Следующий код, содержащийся в `CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
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
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>Пример DefaultTexelToContentSizeRatio

Чтобы увидеть как `DefaultTexelToContentSizeRatio` влияет на размер визуального элемента элементов, рассмотрим код, представленный выше:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Что приведет к следующим образом с помощью текстуры 500 x 500:

![](resolutions-images/image5.png "Что приведет к следующим образом: использование текстур 500 x 500")

IPad Retina выполняется физических разрешение 2048 x 1536. Это означает, что игра, показанной выше бы растянуть 500 пикселей через физические пиксели 1536. Игры можно воспользоваться преимуществами дополнительному разрешению путем создания, что обычно называются *hd* средств — ресурсы, которые предназначены для запуска на экранов с высоким разрешением. Например эта игра использовать версию этой текстуры, размер 1000 x 1000, hd. Тем не менее, с помощью увеличенного изображения приведет к `CCSprite` с шириной и высотой 1000 единиц. Поскольку нашей игре всегда будет отображаться 500 единиц (из-за `SetDesignResolutioSize` вызова), то нашей спрайт 1000 x 1000 будет слишком большой (отображается только в нижней левой части его):

![](resolutions-images/image11.png "Поскольку игра всегда будет отображаться на 500 единиц из-за вызова SetDesignResolutioSize, спрайт 1000 x 1000 будет слишком велик, отображаются только в нижней левой части его")

Можно установить `CCSprite.DefaultTexelToContentSizeRatio` для учетной записи для текстуры 1000 x 1000 дважды, как ширину, высоту, как исходная текстура 500 x 500:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Теперь мы выполнения игры 1000 x 1000 текстура будет полностью видимой:

![](resolutions-images/image12.png "Теперь запускаем игры 1000 x 1000 текстура будет полностью видимым")


### <a name="defaulttexeltocontentsizeratio-details"></a>Сведения о DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio` Свойство `static,` означающее все спрайтов в приложении будут совместно использовать то же значение. Типичный подход для игр с помощью средств, предназначенных для различных разрешений должно содержать полный набор средств для каждой категории разрешения. По умолчанию CocosSharp Visual Studio для Mac шаблоны предоставляют **ld** и **hd** папок для средств, которые могли бы быть полезным для игр, поддерживающий два набора текстур. Примера содержимого папки с содержимым может выглядеть так:

![](resolutions-images/image13.png "Может иметь вид примера содержимого папки с содержимым")

Обратите внимание на то, что шаблон по умолчанию также включает код для добавления в приложение `ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
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
    ...           
}
```

Это означает, что приложение будет искать файлы в пути в соответствии с, выполняется ли он в высоком разрешении или режим регулярных разрешения. Например если **hd** и **ld** папки содержат файл с именем **background.png** следующий код будет выполнить и выделить правильный файл для разрешения:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Сводка

В этой статье описывается, как создавать игры, в которых отображается неправильно, независимо от того, разрешение устройства. В нем представлены примеры использования разных `CCSceneResolutionPolicy` значений для изменения размеров игры в соответствии с разрешения устройства. Он также содержит пример того, как `DefaultTexelToContentSizeRatio` может использоваться для размещения нескольких наборов содержимого без необходимости визуальные элементы к изменению размера по отдельности.

## <a name="related-links"></a>Связанные ссылки

- [Общие сведения о CocosSharp](~/graphics-games/cocossharp/index.md)
- [Документация по API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
