---
title: Обработка нескольких разрешений в CocosSharp
description: В этом руководстве показано, как работать с CocosSharp для разработки игр, правильно отображаться на устройствах для различных разрешений экрана.
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 577a3edbd106b6fba298b3ee5999265ef955f9dd
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920823"
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Обработка нескольких разрешений в CocosSharp

_В этом руководстве показано, как работать с CocosSharp для разработки игр, правильно отображаться на устройствах для различных разрешений экрана._

CocosSharp предоставляет методы для стандартизации объекта измерения в игру независимо от количества физических пикселей на экране устройства. В большинстве случаев игры, разработанных для ПК и игровые консоли может поддерживать один разрешение. Современные игры — и особенно игры для мобильных устройств — должны быть построены в соответствии с широкого спектра устройств. В этом руководстве показано, как для стандартизации CocosSharp игры, вне зависимости от характеристик разрешение физического экрана.

По умолчанию разрешения из CocosSharp выполняется в соответствии с координатами в игре физических пикселях. В следующей таблице показаны как различные устройства будет отображен спрайт фон среды с шириной и высотой 368 x 240. Первая строка является технически не само устройство, но вместо ожидаемого отрисовку спрайта, независимо от того, разрешение устройства:


| **Устройство** | **Разрешение экрана** | **Снимок экрана примера** |
|--- | --- |--- |
|Нужную форму|368 x 240 (с черной полосы для пропорции)| ![368 x 240 (с черной полосы для пропорции)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960 x 640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

В этом документе рассматриваются способы использования CocosSharp для устранения проблемы, перечисленные в таблице выше. То есть мы обсудим, как сделать устройство отображения, как показано в первой строке — вне зависимости от разрешения экрана.


## <a name="working-with-setdesignresolutionsize"></a>Работа с SetDesignResolutionSize

`CCScene` Класс обычно используется как корневой контейнер для всех визуальных объектов, но он также предоставляет статический метод с именем `SetDesignResolutionSize` для указания размера по умолчанию для всех сцены. Другими словами `SetDesignResolutionSize` метод разработчики могут разрабатывать игры, которые будет правильно отображаться на различного оборудования разрешения. Шаблоны проектов CocosSharp используйте этот метод, чтобы задать размеру проекта по умолчанию 1024 x 768, как показано в следующем коде:


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

Вы можете изменить `desiredWidth` и `desiredHeight` переменные, перечисленные выше, чтобы изменить отображение в соответствии с требуемой разрешением игру. Например приведенный выше код может быть изменено следующим образом для отображения фонового рисунка 368 x 240 в полноэкранном режиме:


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

`SetDesignResolutionSize` позволяет указать, как изменяется окна игры в нужное разрешение. В следующих разделах приводится способ отображения изображения 500 x 500 с различными `CCSceneResolutonPolicy` значения, передаваемые в `SetDesignResolutionSize` метод. Предоставляет следующие значения по `CCSceneResolutionPolicy` перечисления:

 - `ShowAll` Отображает весь запрошенное разрешение, сохранение пропорций.
 - `ExactFit` — Отображает весь запрошенное разрешение, но не сохраняет пропорции.
 - `FixedWidth` — Отображает всю ширину запрошенного, сохранение пропорций.
 - `FixedHeight` — Отображает по всей высоте запрошенного, сохранение пропорций.
 - `NoBorder` — Отображает всю высоту или всю ширину, сохранение пропорций. Если пропорции устройства больше, чем пропорции нужное разрешение, будет показано всю ширину. Если пропорции устройства меньше, чем пропорции нужное разрешение, по всей высоте отображается.
 - `Custom` — Отображение зависит от `Viewport` текущего элемента `CCScene`.

Все снимки экрана создаются при разрешении iPhone 4s (960 x 640) в альбомной ориентации и использовать это изображение:

![](resolutions-images/image4.png "Все снимки экрана создаются при разрешении iPhone 4s 960 x 640 в альбомной ориентации и использовать это изображение")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Указывает, будут видны на экране всей игры разрешения, но может отображать *появлению* (черные полосы), чтобы компенсировать различным соотношением сторон. Эта политика обычно используется, как он гарантирует, что все игры представление будет отображаться на экране без искажений, любой.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Этого отображается слева и справа от изображения с учетом физических пропорции, шире, чем нужное разрешение:

![](resolutions-images/image5.png "Этого отображается слева и справа от изображения с учетом физических пропорции, шире, чем нужное разрешение")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Указывает, что весь игры разрешения будут видны на экране с не допустить появления пустых участков. Искажается видимой области (соотношение сторон может не поддерживаться) согласно пропорции оборудования.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Не допустить появления пустых участков видима, но искажается, так как разрешение устройства составляет прямоугольный игры представления:

![](resolutions-images/image6.png "Не допустить появления пустых участков видима, но искажается, так как разрешение устройства составляет прямоугольный представлении игры")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Указывает, что ширина представления будет соответствовать ширина значение, передаваемое `SetDesignResolutionSize`, но для просмотра высоту регулируется пропорции физического устройства. Значение высоты, передаваемое `SetDesignResolutionSize` игнорируется, так как он будет вычисляться во время выполнения в зависимости от физического устройства пропорции. Это означает, что вычисленную ширину может быть меньше, чем требуемую высоту (что приводит в частях игры представления выполняется вне экрана) или вычисляемую высоту может быть больше, чем требуемую высоту (что приводит к дополнительному игры отображаемого представления). Так как это может привести к более отображаемых игры, затем может показаться, как если бы этого произошло; Тем не менее дополнительное пространство не обязательно черный, если любой визуальный объект появится там. 

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s имеет пропорции 3:2, поэтому вычисленную ширину составляет приблизительно 333 единиц:

![](resolutions-images/image7.png "IPhone 4s имеет пропорции 3:2, поэтому вычисленную ширину составляет приблизительно 333 единиц")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

По существу `FixedHeight` ведет себя так же, как `FixedWidth` — игры будут соответствовать значение высоты, передаваемое `SetDesignResolutionSize,` , но вычислит ширину во время выполнения на основе этих физического разрешения. Как сказано выше, это означает, что ширина отображаемой быть требуемую ширину, возникающие в процессе игры, больше или меньше off экрана или несколько игры, при отображении соответственно.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

С момента создания следующий снимок экрана с выполнением приложения в альбомном режиме, не должен превышать 500 — в частности 750 рассчитанную ширину. Этот параметр сохраняет значение X 0 по левому краю, поэтому дополнительное разрешение для просмотра в правой части экрана.

![](resolutions-images/image8.png "Этот параметр сохраняет значение X 0 по левому краю, поэтому дополнительное разрешение для просмотра в правой части экрана")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` пытается отобразить приложения с не допустить появления пустых участков сохранением первоначальных пропорций (без искажений). Если запрошенное разрешение пропорции соответствует пропорции физического устройства, затем отсечение выполнено не будет. Если пропорции не совпадают, затем отсекает возникнет.

Пример кода:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

На следующем снимке экрана отображает верхней и нижней части экрана обрезается, пока все 500 пикселей от ширины экрана отображаются:

![](resolutions-images/image9.png "Этот снимок экрана отображает верхней и нижней части экрана обрезается, пока все 500 пикселей от ширины экрана отображаются")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` позволяет каждой `CCScene` чтобы указать свой собственный пользовательский просмотра относительно разрешения, указанные в `SetDesignResolutionSize`.

Автоматически определять их `Viewport` свойства, создав `CCViewport`, который, в свою очередь, создается с `CCRect`. Дополнительные сведения о `CCViewport` и `CCRect` разделе [документации по API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). В контексте создания `CCViewport` `CCRect` указать параметры конструктора (по порядку):

 - x — величина горизонтальное смещение окна просмотра, где каждая единица представляет один ширину весь экран. Например использование значения .5f результатов в сцене сдвинуто вправо половины ширины экрана.
 - y — величина вертикальное смещение окна просмотра, где каждая единица представляет один высоту весь экран. Например с помощью значения .5f результатов в сцене сдвигаются на половину высоту экрана.
 - Ширина-горизонтальной часть, которая должна занимать сцены. Например, с помощью значение 1 / 3.0f приводит сцены, занимающих одну треть экрана по горизонтали.
 - Высота — это вертикальная область, должен занимать сцены. Например, с помощью значение 1 / 3.0f приводит сцены, занимающих одну треть экрана по вертикали.

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

Приведенный выше код произведут следующий результат:

![](resolutions-images/image10.png "Приведенный выше результаты на этом снимке экрана")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio` Упрощает использование текстур с более высоким разрешением на устройствах с помощью экранов с высоким разрешением. В частности это свойство позволяет игры для использования более высоким разрешением средств без необходимости изменять размер или расположение визуальных элементов. 

Например, игры могут включать актива рисунка для игры символ, — 200 пикселей в высоту и игровые экрана (в соответствии с `SetDesignResolutionSize`) может быть 400 пикселей в высоту. В этом случае символ будут занимать половину экрана. Однако если 400 активов высоту в пикселях были использованы для символа для устройств с более высоким разрешением, символ будет занимать всю высоту дисплея. Если предполагается хранить символ тот же размер, чтобы воспользоваться преимуществами выше разрешение устройства дополнительный код необходимо поместить знак sprite на половину высоты экрана.

Простой пример, представленный выше представляет распространенной проблемой для разработки игр — Добавление большего размера средств без необходимости составления разработчиком для выполнения изменения размера для каждого элемента visual в соответствии с разрешения устройства. `DefaultTexelToContentSizeRatio` глобальное свойство используется для изменения размеров визуальные элементы. Это значение изменяет все визуальные элементы определенного типа, присваиваемое значение.

Это свойство присутствует в следующие классы: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

CocosSharp Visual Studio для Mac набор шаблонов `CCSprite.DefaultTexelToContentSizeRatio` согласно ширину устройства в качестве примера того, как его использовать. Следующий код содержится в `CCApplicationDelegate.ApplicationDidFinishLaunching`:


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

Чтобы увидеть как `DefaultTexelToContentSizeRatio` влияет на размер визуальных элементов, рассмотрим код, представленные выше:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Что приведет к на следующем рисунке, использование текстур 500 x 500:

![](resolutions-images/image5.png "Что приведет к этот образ с помощью текстуры 500 x 500")

IPad Сетчатка работает с физического разрешения 2048 x 1536. Это означает, что игра, показанной выше сделать вывод 500 пикселей по 1536 физических пикселях. Игры могут воспользоваться преимуществами дополнительное разрешение путем создания, что обычно называются *hd* активы — средства, которые предназначены для запуска на экранов с высоким разрешением. Например эта игра может использовать версию hd этот текстуры в 1000 x 1000 размере. Тем не менее, использование более крупного изображения приведет `CCSprite` с шириной и высотой 1000 единиц. Поскольку наш игры всегда будет отображаться 500 единицы (из-за `SetDesignResolutioSize` вызова), то нашей sprite 1000 x 1000 будет слишком большой (только в нижней левой части она отображает):

![](resolutions-images/image11.png "Поскольку игры всегда будет отображаться 500 единиц из-за вызова SetDesignResolutioSize, sprite 1000 x 1000 бы отображается только в нижней левой части он слишком велик")

Можно выбрать вариант `CCSprite.DefaultTexelToContentSizeRatio` с учетом 1000 x 1000 текстуры, дважды в ширину, высоту как исходная текстура 500 x 500:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Теперь если мы запустим игры текстуры 1000 x 1000 будет полностью отображены:

![](resolutions-images/image12.png "Теперь если мы запустим игры текстуры 1000 x 1000 будет полностью отображены")


### <a name="defaulttexeltocontentsizeratio-details"></a>Сведения о DefaultTexelToContentSizeRatio

`DefaultTexelToContentSizeRatio` Свойство `static,` означающее все спрайтов в приложении будет иметь одинаковое значение. Типичный подход для игр с помощью средств, предназначенных для различных разрешений экрана должно содержать полный набор средств для каждой категории разрешения. По умолчанию CocosSharp Visual Studio для Mac шаблонов предоставляют **ld** и **hd** папок для средств, которые будут использоваться для поддержки двух наборов текстур игр. Пример содержимого папки с содержимым может иметь вид:

![](resolutions-images/image13.png "Пример содержимого папки с содержимым может выглядеть как")

Обратите внимание, что шаблон по умолчанию также включает код для добавления в приложение `ContentSearchPaths`:


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

Это означает, что приложение будет искать файлы в путях согласно ли она выполняется в высокого разрешения или режим регулярного разрешения. Например если **hd** и **ld** папки содержат файл с именем **background.png** затем может выполнить и выберите правильный файл для разрешения следующий код:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Сводка

В этой статье описывается, как для создания игр, в которых отображается неправильно, вне зависимости от разрешения устройства. Приводятся примеры использования различных `CCSceneResolutionPolicy` значений для изменения размеров игры согласно разрешения устройства. Он также предоставляет пример `DefaultTexelToContentSizeRatio` может использоваться для размещения несколько наборов содержимого, не требуя визуальные элементы к изменению размера по отдельности.

## <a name="related-links"></a>Связанные ссылки

- [Введение CocosSharp](~/graphics-games/cocossharp/index.md)
- [Документация по CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
