---
title: С помощью ARKit с UrhoSharp в Xamarin.iOS
description: В этом документе описывается настройка приложения ARKit в Xamarin.iOS, после чего просматривает как отрисовываются кадры, как настроить камеры, как обнаружить плоскостей, как работать с освещения и многое другое. Здесь также рассматриваются UrhoSharp и написание кода для HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: 435ca560eee4f8f44443816c2a4ccec195ba5395
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103898"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>С помощью ARKit с UrhoSharp в Xamarin.iOS

С появлением [ARKit](https://developer.apple.com/arkit/), Apple просто стало разработчикам создавать приложения в режиме дополненной реальности. ARKit можно отслеживать точную позицию устройства и обнаружения различных областей по всему миру, а он затем разработчик должен blend данные, исходящие из ARKit в код.

[UrhoSharp](~/graphics-games/urhosharp/index.md) предоставляет комплексное и простой в использовании API 3D, который можно использовать для создания трехмерных приложений.   Они могут быть наложения, ARKit физических информацию о пространстве, а также Urho для показа результатов.

Этой странице объясняется, как подключить этих двух областей для создания приложений отлично режиме дополненной реальности.


## <a name="the-basics"></a>Основы

Мы хотим сделать — представления трехмерного содержимого на основе всему миру, как явствует iPhone и iPad.   Идея состоит в том, для объединения содержимого, поступающих от камеры устройства с трехмерного содержимого и пользователь устройства перемещает вокруг комнате, чтобы убедиться, что трехмерный объект ведут себя, как именно эти части этой комнате, — это можно сделать, привязка объектов в этом мире.

![Анимированный рисунок в ARKit](urhosharp-images/image1.gif)


Мы будем использовать библиотеку Urho для загрузки наших трехмерных ресурсов и помещать их на мир, и мы будем использовать ARKit для получения потока видео, поступающих от камеры, а также расположение телефона в мире.   При перемещении с свой телефон, мы будем использовать изменения в расположении для обновления системы координат, которая отображает Urho ядра.

Таким образом, после помещения объекта в трехмерном пространстве и перемещении, расположение трехмерного объекта отражает место и расположение, где он был помещен.

## <a name="setting-up-your-application"></a>Настройка приложения

### <a name="ios-application-launch"></a>Запуск приложения iOS

Операций ввода-вывода, необходимых приложению для того создать и запустить 3D-содержимого, это сделать, создав, реализующим подкласс [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) и предоставить код установки путем переопределения `Start` метод.  Это где сцена заполняется данными событий, обработчики установки и т. д.

Мы представили `Urho.ArkitApp` класса, который наследуется от класса `Urho.Application` и на его `Start` метод выполняет всю тяжелую работу.   Все что нужно сделать ваши существующие Urho приложения измените базовый класс типа `Urho.ArkitApp` и у вас есть приложение, которое будет выполняться ваш urho сцены в мире.

### <a name="the-arkitapp-class"></a>Класс ArkitApp

Этот класс предоставляет набор полезные значения по умолчанию: оба сцены с помощью некоторых ключевых объектов, а также обработка событий ARKit, так как они предоставляются операционной системой.

Программа установки выполняется в `Start` виртуального метода.   При переопределении этого метода в подклассе, необходимо убедитесь, что цепочка на родительский с помощью `base.Start()` на собственную реализацию.

`Start` Метод настраивает сцены, окна просмотра, камеры и направленный свет и предоставляет их как открытые свойства:

- [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) для хранения ваших объектов
- Направленная [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) с теней, а место которого доступна через `LightNode` свойство
- [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) компоненты которого обновляется, когда ARKit доставляет обновления приложения и
- [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) отображения результатов.


### <a name="your-code"></a>Ваш код

Затем необходимо создать подкласс `ArkitApp` класса и переопределить `Start` метод.   Первое, что следует сделать ваш метод является цепочка до `ArkitApp.Start` путем вызова `base.Start()`.  После этого можно использовать никаких настроек свойств по ArkitApp для добавления объектов в сцену, настроить освещения, теней или события, которые необходимо обработать.

В примере ARKit/UrhoSharp загружает анимированные символом текстур и воспроизводит анимацию, следующей реализацией:

    ```csharp
    public class MutantDemo : ArkitApp
    {
        [Preserve]
        public MutantDemo(ApplicationOptions opts) : base(opts) { }

        Node mutantNode;

        protected override void Start()
        {
            base.Start ();

            // Mutant
            mutantNode = Scene.CreateChild();
            mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
            mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
            mutantNode.SetScale(0.5f);

            var mutant = mutantNode.CreateComponent<AnimatedModel>();
            mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
            mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

            var animation = mutantNode.CreateComponent<AnimationController>();
            animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
        }
    }
    ```

И именно так все, что нужно сделать на этом этапе, чтобы 3D-содержимого отображается в режиме дополненной реальности.

Urho использует пользовательские форматы для трехмерных моделей и анимации, поэтому вам нужно экспортировать свои активы в этот формат.   Можно использовать такие средства, как [Urho3D Blender надстройки](https://github.com/reattiva/Urho3D-Blender) и [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) , можно преобразовывать эти активы из таких популярных форматах, как DBX, DAE, OBJ, Blend, 3D-Max в формат, необходимый Urho.

Дополнительные сведения о создании трехмерных приложений с помощью Urho, [введение UrhoSharp](~/graphics-games/urhosharp/introduction.md) руководства.

## <a name="arkitapp-in-depth"></a>ArkitApp подробно

> [!NOTE]
> Этот раздел предназначен для разработчиков, которые необходимо настроить по умолчанию среда UrhoSharp и ARKit или вы хотите получить более глубокое представление о работе интеграции.   Этот раздел необязателен.

ARKit API очень просто, создание и настройка [ARSession](https://developer.apple.com/documentation/arkit/arsession) объекта, который затем доставлять [ARFrame](https://developer.apple.com/documentation/arkit/arframe) объектов.   Они содержат изображение, создаваемое, камеры, а также предполагаемое положение реальных устройства.

Мы будет создание образов, которые доставляются с помощью камеры к нам с нашей трехмерного содержимого и измените в UrhoSharp в соответствии с вероятности в местоположение устройства и положение камеры.

На следующей схеме показано, что выполняется `ArkitApp` класса:

[![Схема классов и экраны в ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Подготовка к просмотру кадров

Идея проста, объединять видео, поступающих из камеры с нашей трехмерной графикой для создания объединенного изображения.     Мы будут поступать ряд этих записанных образов в последовательности, и мы будет сочетать эти входные данные с Urho сцены.

Самый простой способ сделать это — для вставки [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) в метод main [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Это набор команд, которые выполняются для рисования один кадр.  Эта команда укажет окна просмотра текстурой, которые передать его.    Мы создаем это на первом кадре, — процесс, и фактическое определение выполняется в th **ARRenderPath.xml** файл, который загружается на этом этапе.

Тем не менее мы столкнулись с необходимостью совмещать этих двух областей две проблемы:


1. Текстур GPU iOS, должен иметь разрешение, которое не степень двойки, но кадры, которые мы получаем от камеры, не имеют разрешения, которые степень двойки, например: 1280 x 720.
2. Кадры кодируются в [YUV](https://en.wikipedia.org/wiki/YUV) формат, представленный два изображения — яркость и цветности.

Кадры YUV бывают двумя разными разрешениями.  изображение 1280 x 720, представляющий освещенности (по сути, изображение оттенки серого) и гораздо меньше 640 x 360 цветности компонента:

![Изображение, демонстрация объединение Y и компоненты UV](urhosharp-images/image3.png)


Для рисования полный цветные изображения с помощью OpenGL ES, нам нужно написать небольшой шейдер, который принимает освещенности (компонент Y) и цветности (UV плоскости) из слотов текстуры.  В UrhoSharp они имеют имена — «sDiffMap» и «sNormalMap» и преобразовывать их в формате RGB:

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Для подготовки к просмотру текстуру, не является степенью числа два разрешения, нам нужно определить Texture2D со следующими параметрами:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Таким образом мы имеют возможность отображения записанных образов в качестве фона и отрисовка подобное scary объект-мутант любой сцены, над ним.

### <a name="adjusting-the-camera"></a>Настройка камеры

`ARFrame` Объекты также содержат позиции предполагаемое устройства.  Теперь нам нужно переместить игр камеры ARFrame — перед ARKit, он не был голограммы особенного ориентации устройства track (наката, прецессии и нутации) и визуализации, закрепленные голограмма поверх видео -, но если вы немного переместить устройства - мы будет смещения.

Что происходит, потому что встроенных датчиков, таких как гироскоп не смогут отслеживать движения, они могут только ускорение.  ARKit анализа каждого кадра и извлекает компонента точки для отслеживания и таким образом, способна нам важно знать точный преобразования матрица, содержащая данные, перемещения и поворота.

Например Вот как можно получить текущую позицию:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Мы используем `-row.Z` так, как ARKit использует правостороннюю систему координат.


### <a name="plane-detection"></a>Обнаружение плоскости

ARKit может определить горизонтальной плоскости и эта возможность позволяет взаимодействовать с реальным миром, например, можно поместить объект-мутант на реальной или этаж. Для этого проще всего использовать hitTest-метод (точные точки для отслеживания). Она преобразует экранные координаты (0,5, 0,5 представляет собой центр) в реальном мире координаты (0; 0; 0 — первый кадр расположение).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

Теперь можно поместить объект-мутант на поверхности горизонтальной зависят от места на экране устройства, которые мы коснитесь:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Анимированный рисунок изменение плоскостей как перемещение представления](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Реалистичные освещения

В зависимости от условий освещения реального мира виртуальный сцены должно быть светлее или темнее в соответствии с окружающим его элементам. ARFrame содержит LightEstimate свойство, которое можно использовать для настройки внешнего освещения Urho, это делается следующим образом:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Помимо iOS - HoloLens

UrhoSharp [работает на всех основных операционных системах](~/graphics-games/urhosharp/platform/index.md), поэтому можно повторно использовать существующий код в другом месте.

HoloLens — одна из самых замечательных платформ, на которых оно выполняется.   Это означает, что можно легко переключаться между iOS и HoloLens для создания впечатляющих приложений дополненная реальность, использование UrhoSharp.

Вы найдете источника MutantDemo [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Связанные ссылки

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (с UrhoSharp) (пример)](https://github.com/EgorBo/ARKitXamarinDemo)
