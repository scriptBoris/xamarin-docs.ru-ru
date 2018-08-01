---
title: С помощью класса модели
description: Класс модели значительно упрощает отрисовку сложных трехмерных объектов по сравнению с традиционным способом отображения трехмерной графики. Объекты модели создаются из файлов содержимого, что обеспечивает простую интеграцию содержимого без пользовательского кода.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e108bc6098d2c754428bf35e7312ebdc89459501
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783471"
---
# <a name="using-the-model-class"></a>С помощью класса модели

_Класс модели значительно упрощает отрисовку сложных трехмерных объектов по сравнению с традиционным способом отображения трехмерной графики. Объекты модели создаются из файлов содержимого, что обеспечивает простую интеграцию содержимого без пользовательского кода._

Включает MonoGame API `Model` загрузить класс, который может использоваться для хранения данных из файла с содержимым и для выполнения отрисовки. Файлы модели может быть очень простым (например сплошной цветной треугольник), или могут быть включены сведения для сложных подготовки к просмотру, включая текстурирования и освещение.

В этом пошаговом руководстве используется [трехмерной модели робот](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) и охватывает следующие:

- Запуск нового проекта игры
- Создание модели и текстура XNBs
- Включая XNBs в проекте игры
- Рисование трехмерной модели
- Рисование несколько моделей

После завершения проекта будет выглядеть следующим образом:

![По завершении образец, показывающий шесть роботов](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Создавая пустой проект игры

Нам нужно будет настроить проект игры, сначала вызывается MonoGame3D. Сведения о создании нового проекта MonoGame см. в разделе [в данном пошаговом руководстве по созданию кросс-платформенным проектом Monogame](~/graphics-games/monogame/introduction/part1.md).

Перед переходом мы убедитесь, что проект открывается и развертывает правильно. Один раз развернутой мы должны увидеть пустой синий экран:

![Пустой синий экран игры](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Включая XNBs в проекте игры

Формат файла .xnb является стандартным расширением для построения содержимого (содержимого, которое было создано, [MonoGame конвейера средство](http://www.monogame.net/documentation/?page=Pipeline)). Все построенные содержимое имеет исходный файл (который представляет собой файл .fbx в случае нашей модели) и конечный файл (файл .xnb). Формат .fbx — общий формат 3D-модели, которые могут быть созданы приложениями, такими как [Maya](http://www.autodesk.com/products/maya/overview) и [Blender](http://www.blender.org/). 

`Model` Класса могут быть созданы путем загрузки файла .xnb с диска, содержащего 3D геометрических данных.   Этот файл .xnb создается посредством содержимого проекта. Шаблоны Monogame автоматически включают содержимого проекта (с расширением .mgcp) в нашем содержимого папки. Подробный рассказ о средстве MonoGame конвейера, в разделе [конвейера содержимого руководства](~/graphics-games/cocossharp/content-pipeline/index.md).

В этом руководстве мы будем пропустить с помощью конвейера MonoGame инструментов и будет использовать. XNB-файлы, содержащиеся здесь. Обратите внимание, что. XNB файлы отличаются каждой платформы, поэтому убедитесь, что используется правильный набор файлов XNB для любой платформы, вы работаете.

Мы будем Распакуйте [Content.zip файл](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) , чтобы мы могли использовать файлы автономной .xnb в нашем игры. Если проект Android, щелкните правой кнопкой мыши **активы** папки в **WalkingGame.Android** проекта. Если проект iOS, щелкните правой кнопкой мыши **WalkingGame.iOS** проекта. Выберите **Добавить -> Добавить файлы...**  и выберите оба файла .xnb в папке для платформы, вы работаете.

Оба файла должны быть частью проекта теперь:

![Папки содержимого обозреватель решений с файлами xnb](part1-images/xnbsinxs.png)

Visual Studio для Mac не может автоматически устанавливать действие построения для новых XNBs. Для операций ввода-вывода, щелкните правой кнопкой мыши на каждом из файлов и выберите **действие построения "->" BundleResource**. Для Android, щелкните правой кнопкой мыши на каждом из файлов и выберите **действие построения "->" AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Подготовка к просмотру трехмерной модели

Последний шаг позволяет просматривать модели на экране является добавление загрузки и код отрисовки. В частности мы будете делать следующее:

- Определение `Model` экземпляра в нашем `Game1` класса
- Загрузка `Model` экземпляра `Game1.LoadContent`
- Рисование `Model` экземпляра `Game1.Draw`

Замените `Game1.cs` файл кода (который находится в **WalkingGame** PCL) со следующими:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Если мы запустим этот код мы модели будет отображен на экране:

![Модель отображается на экране](part1-images/image8.png "при выполнении этого кода, модель отображается на экране")

### <a name="model-class"></a>Класс модели

`Model` Класс является основным классом для выполнения трехмерной отрисовки из файлов содержимого (например, файлы .fbx). Он содержит все сведения, необходимые для подготовки к просмотру, включая трехмерной геометрии, ссылки текстур и `BasicEffect` экземпляры, которые определяют положение, освещения и камеры значения.

`Model` Непосредственно самого класса нет переменных для позиционирования, так как экземпляр одна модель может отображаться в нескольких местах, как мы покажем далее в этом руководстве.

Каждый `Model` состоит из одного или нескольких `ModelMesh` экземпляров, которые предоставляются посредством `Meshes` свойство. Несмотря на то, что можно рассмотреть возможность `Model` как один игры объекта (робот или машины), каждый `ModelMesh` могут отображаться с различными `BasicEffect` значения. Например, сетки отдельных частей могут представлять лучей робот или колеса в автомобиле, и мы может назначить `BasicEffect` значения счетчика колеса или конечностей перемещения. 

### <a name="basiceffect-class"></a>Класс BasicEffect

`BasicEffect` Класс предоставляет свойства для управления параметры отрисовки. Первое изменение, мы предоставляем `BasicEffect` заключается в вызове `EnableDefaultLighting` метода. Как следует из имен, это позволяет освещения по умолчанию, который очень удобен для проверки того, что `Model` появляется в игре должным образом. Если мы закомментируйте `EnableDefaultLighting` вызвать, мы увидим модели к просмотру с только что текстура, но нет свечения заливку или зеркального отражения:

```csharp
//effect.EnableDefaultLighting ();
```

![Модели к просмотру с только что текстура, но нет свечения заливку или отраженный](part1-images/image9.png "модели к просмотру с только что текстура, но нет заливку или отраженный свечения")

`World` Свойство можно использовать для настройки положения, поворот и масштабирование модели. Приведенный выше использует `Matrix.Identity` значение, которое означает, что `Model` будет отображаться в игре точно, как указано в файле .fbx. Мы будем рассматривать матрицы и трехмерных координат более подробно в [часть 3](~/graphics-games/monogame/3d/part3.md), но в качестве примера можно изменить положение `Model` , изменив `World` свойства следующим образом:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Этот код вызывает объект перемещается вверх по 3 международных единицах измерения.

![Этот код вызывает объект перемещается вверх по три единицы world](part1-images/image10.png "этот код вызывает объект перемещается вверх по три единицы world")

Окончательный двумя свойствами, назначенными на `BasicEffect` , `View` и `Projection`. Мы будем рассматривать 3D камеры в [часть 3](~/graphics-games/monogame/3d/part3.md), но в качестве примера мы можно изменить положение камеры, изменив локальной `cameraPosition` переменной:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Мы видим, камеры перемещено дальнейших назад, в результате чего `Model` появление меньшего размера, из-за перспективы:

![Камера перемещено дальнейших назад, возникающие в модели, появляющихся меньшего размера, из-за перспективы](part1-images/image11.png "камеры перемещено дальнейших назад, возникающие в модели, появляющихся меньшего размера, из-за перспективы")

## <a name="rendering-multiple-models"></a>Подготовка к просмотру нескольких моделей

Как упоминалось выше, один `Model` могут отображаться несколько раз. Чтобы облегчить эту задачу можно скользящего `Model` код отрисовки в свой собственный метод, который принимает нужный `Model` позиции в качестве параметра. После окончания нашей `Draw` и `DrawModel` методов будет выглядеть так:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

Это приводит к робота которых выводится в шесть раз модели:

![Это приведет к робота которых выводится в шесть раз модели](part1-images/image1.png "это приведет к робота которых выводится в шесть раз модели")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве MonoGame `Model` класса. Она охватывает преобразование файла .fbx в .xnb, который в свою очередь в может быть загружена `Model` класса. Здесь также показано, как изменения в `BasicEffect` может повлиять на экземпляр `Model` рисования.

## <a name="related-links"></a>Связанные ссылки

- [Справочник по MonoGame модели](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.ZIP](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
