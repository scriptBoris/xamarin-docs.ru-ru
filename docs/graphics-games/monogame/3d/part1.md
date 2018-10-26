---
title: С помощью класса модели
description: Класс модели значительно упрощает отрисовку сложные трехмерные объекты по сравнению с традиционным способом отрисовки 3D-графики. Объекты модели создаются на основе файлов содержимого, что обеспечивает простую интеграцию содержимого без написания кода.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d9c73dfcee6321ecb314ca229db407c6d0438977
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108592"
---
# <a name="using-the-model-class"></a>С помощью класса модели

_Класс модели значительно упрощает отрисовку сложные трехмерные объекты по сравнению с традиционным способом отрисовки 3D-графики. Объекты модели создаются на основе файлов содержимого, что обеспечивает простую интеграцию содержимого без написания кода._

Включает MonoGame API `Model` класс, который может использоваться для хранения данных загружен из содержимого файла и для выполнения отрисовки. Файлы модели могут быть очень простыми (например, сплошной цветной треугольник), или могут включать сведения для сложных отрисовки, включая текстурирования и освещения.

В этом пошаговом руководстве использует [трехмерной модели робота](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) и охватывает следующие:

- Запуск проекта игр
- Создание модели и текстура XNBs
- Включая XNBs в игровой проект
- Рисования к трехмерной модели
- Рисование нескольких моделей

После завершения наш проект будет выглядеть следующим образом:

![По завершении пример, показывающий шесть программ-роботов](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Создание пустого проекта игр

Нам потребуется настроить игровой проект, сначала вызывается MonoGame3D. Сведения о создании нового проекта MonoGame, см. в разделе [в этом пошаговом руководстве по созданию проекта Monogame платформы Cross](~/graphics-games/monogame/introduction/part1.md).

Прежде чем мы следует проверить проект открывает и развертывает правильно. После развертывания, мы должны увидеть пустой синий экран:

![Пустой синего экрана игры](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Включая XNBs в игровой проект

Формат файла .xnb является стандартным расширением для сборки содержимого (content, который был создан с [средства конвейера MonoGame](http://www.monogame.net/documentation/?page=Pipeline)). Все созданные содержимое имеет исходный файл (который представляет собой файл .fbx, в случае нашей модели) и конечный файл (файл .xnb). Формат .fbx Это распространенный формат трехмерной модели, которые могут быть созданы приложениями такие как [Maya](http://www.autodesk.com/products/maya/overview) и [Blender](http://www.blender.org/). 

`Model` Класс может быть создан, загрузив файл .xnb с диска, который содержит данные трехмерной геометрии.   Этот файл .xnb создается с помощью содержимого проекта. Шаблоны Monogame автоматически включают содержимого проекта (с расширением .mgcp) нашей содержимого папки. Дополнительные сведения о средстве MonoGame конвейер, см. в разделе [руководстве конвейер содержимого](~/graphics-games/cocossharp/content-pipeline/index.md).

В этом руководстве, мы не станем по сравнению с использованием конвейера MonoGame средство и будет использовать. XNB файлы в этой статье. Обратите внимание, что. Файлы XNB отличаются каждой платформы, поэтому не забудьте использовать правильный набор файлов XNB для любой платформы, вы работаете.

Мы будем Распакуйте [Content.zip файл](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) таким образом, мы можем использовать файлы автономной .xnb в нашей игре. Если проект Android, щелкните правой кнопкой мыши **активы** папку в **WalkingGame.Android** проекта. Если проект iOS, щелкните правой кнопкой мыши **WalkingGame.iOS** проекта. Выберите **Добавить -> Добавить файлы...**  и укажите оба файла .xnb в папку для платформы, вы работаете.

Теперь два файла должен быть частью нашего проекта:

![Решение explorer содержимого папки с файлами xnb](part1-images/xnbsinxs.png)

Visual Studio для Mac не может автоматически устанавливать действие построения для только что добавленную XNBs. Для iOS, щелкните правой кнопкой мыши на каждом из файла и выберите **действие сборки "->" BundleResource**. Для Android, щелкните правой кнопкой мыши на каждом из файла и выберите **действие сборки "->" AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Подготовка к просмотру трехмерной модели

Последний шаг, необходимый для на экране см. в модели является добавление загрузку и код отрисовки. В частности мы будете делать следующее:

- Определение `Model` экземпляра в наших `Game1` класса
- Загрузка `Model` в экземпляре `Game1.LoadContent`
- Рисование `Model` в экземпляре `Game1.Draw`

Замените `Game1.cs` файл кода (который находится в **WalkingGame** PCL) со следующими:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Если выполнить этот код мы увидим модели на экране:

![Модель отображается на экране](part1-images/image8.png "при выполнении этого кода, модель отображается на экране")

### <a name="model-class"></a>Класс модели

`Model` Класс является основным классом для выполнения отрисовки 3D из содержимого файлов (например, файлы .fbx). Он содержит все сведения, необходимые для подготовки к просмотру, включая трехмерной геометрии, ссылки текстуры, и `BasicEffect` экземпляров, которые положение, освещение и камеру значения элемента управления.

`Model` Непосредственно сам класс не имеет переменных для позиционирования, так как экземпляр одна модель может быть отображен в нескольких местах, как мы покажем далее в этом руководстве.

Каждый `Model` состоит из одного или нескольких `ModelMesh` экземпляров, которые предоставляются посредством `Meshes` свойство. Несмотря на то, что мы рассмотреть `Model` как одну игру объекта (такого как робот или автомобиля), каждый `ModelMesh` можно нарисовать с разными `BasicEffect` значения. Например, сетке отдельных частей могут представлять конечностей робота или колес на автомобиль, и мы может назначить `BasicEffect` значения, чтобы упростить колес "Счетчик" или конечностей перемещения. 

### <a name="basiceffect-class"></a>Класс BasicEffect

`BasicEffect` Класс предоставляет свойства для управления параметры отрисовки. Мы вносим в первом изменении `BasicEffect` заключается в вызове `EnableDefaultLighting` метод. Как и предполагает название, это позволяет освещения по умолчанию, который очень удобен для проверки того, что `Model` появляется в игре должным образом. Если мы закомментируйте `EnableDefaultLighting` вызова, то увидим модели к просмотру с только что текстура, но нет заливку и отражающий свечения:

```csharp
//effect.EnableDefaultLighting ();
```

![Модели к просмотру с только что текстура, но не заливку и отражающий свечение](part1-images/image9.png "модели к просмотру с только что текстура, но нет заливку и отражающий свечения")

`World` Свойство может использоваться для настройки положения, поворота и масштабирования модели. Приведенном выше примере используется `Matrix.Identity` значение, которое означает, что `Model` будет отображаться в игре точно как указано в файле .fbx. Мы будем рассматривать, матрицы и трехмерные координаты более подробно в [часть 3](~/graphics-games/monogame/3d/part3.md), но в качестве примера мы можем изменить положение `Model` , изменив `World` свойства следующим образом:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Этот код вызывает объект перемещается вверх по 3 в международных единицах измерения:

![Этот код приводит объект перемещается вверх по 3 мировыми](part1-images/image10.png "этот код приводит объект перемещается вверх по 3 в международных единицах измерения")

Окончательный двумя свойствами, назначенными на `BasicEffect` являются `View` и `Projection`. Мы будем рассматривать трехмерной камеры в [часть 3](~/graphics-games/monogame/3d/part3.md), но в качестве примера мы можем изменить положение камеры, изменив локальной `cameraPosition` переменной:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Мы видим, камеры перемещено дальнейших назад, в результате чего `Model` отображение меньшего размера, из-за перспективы:

![Камеры перемещено дальнейших назад, приводит появление меньшего размера, из-за точки зрения модели](part1-images/image11.png "камеры перемещено дальнейших назад, приводит появление меньшего размера, из-за точки зрения модели")

## <a name="rendering-multiple-models"></a>Подготовка к просмотру нескольких моделей

Как упоминалось выше, одной `Model` могут отображаться несколько раз. Чтобы облегчить эту задачу мы будем нести скользящего `Model` код отрисовки в отдельный метод, принимающий нужный `Model` позиции в качестве параметра. После завершения наших `Draw` и `DrawModel` методов будет выглядеть следующим образом:


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

Это приводит к робота модели, в которых выводится в шесть раз:

![Это приводит к модели, в которых выводится в шесть раз робота](part1-images/image1.png "это приводит к модели, в которых выводится в шесть раз робота")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве MonoGame `Model` класса. Здесь рассматривается преобразование файла .fbx .xnb которой могут, в свою очередь загружаться в `Model` класса. Он также показано, как изменения `BasicEffect` может повлиять на экземпляр `Model` рисования.

## <a name="related-links"></a>Связанные ссылки

- [Справочник по модели MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.ZIP](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
