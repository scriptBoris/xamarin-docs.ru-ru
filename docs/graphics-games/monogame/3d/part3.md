---
title: Трехмерные координаты в MonoGame
description: Основные сведения о трехмерных координат является важным шагом в разработке трехмерных игр. MonoGame предоставляет ряд классов для позиционирования, ориентация и масштабирование объектов в трехмерном пространстве.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dc21228f1daa74a90ff8f0ea346bc01b109f0987
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107648"
---
# <a name="3d-coordinates-in-monogame"></a>Трехмерные координаты в MonoGame

_Основные сведения о трехмерных координат является важным шагом в разработке трехмерных игр. MonoGame предоставляет ряд классов для позиционирования, ориентация и масштабирование объектов в трехмерном пространстве._

Разработка трехмерных игр требует понимания того, как работать с объектами в трехмерной системе координат. В этом пошаговом руководстве мы рассмотрим, как управлять визуальных объектов (в частности, модель). Мы выполним сборку на концепциях управления модели для создания трехмерной камеры класса.

Представленные концепции получаются из линейной алгебры, но мы будем исходить из практических соображений, что любой пользователь без строгого math фона применить эти концепции в свои собственные игры.

Мы будем рассматривать следующие разделы:

- Создание проекта
- Создание сущности робота
- Перемещение объект робота
- Умножение матриц
- Создание сущности камеры
- Перемещение камеры с входными данными

По завершении мы еще проекта с помощью робота, перемещение в круг и камеры, который может управляться сенсорный ввод:

![](part3-images/image1.gif "По завершении приложение будет включать проект с робота, перемещение в круг и камеры, который может управляться сенсорный ввод")


## <a name="creating-a-project"></a>Создание проекта

В этом пошаговом руководстве рассматривается переход объектов в трехмерном пространстве. Начнем с проектом для моделей отрисовки и массивов вершин [которого можно найти здесь](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). После скачивания, распаковка и открытие проекта, чтобы убедиться в том, он работает, и мы должны увидеть следующее:

![](part3-images/image2.png "После загрузки Распакуйте и откройте проект, чтобы убедиться в том, он работает и должны отображаться в этом представлении")


## <a name="creating-a-robot-entity"></a>Создание сущности робота

Прежде чем начать перенос наших робота вокруг, мы создадим `Robot` класс, содержащий логику рисования и перемещения. Разработчики игр ссылаться на этот инкапсуляции логики и данных в виде *сущности*.

Добавьте новый файл пустой класс **MonoGame3D** переносимой библиотеки классов (не ModelAndVerts.Android платформы). Назовите его **робота** и нажмите кнопку **New**:

![](part3-images/image3.png "Назовите его робота и щелкните \"Создать\"")

Изменить `Robot` следующим образом:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

`Robot` Код является по сути тот же код в `Game1` для рисования `Model`. Для просмотра на `Model` загрузки и Рисование, см. в разделе [в этом руководстве по работе с моделями](~/graphics-games/monogame/3d/part1.md). Теперь вы можем убрать все `Model` Загрузка и Подготовка к просмотру кода из `Game1`и замените ее строкой `Robot` экземпляр:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Если выполнить код теперь у нас будет сцены с помощью только один робот, который рисуется в основном разделе ближайшее снизу целое:

![](part3-images/image4.png "Если теперь выполняется код, приложение будет отображать сцены с помощью только один робот, который рисуется в основном разделе ближайшее снизу целое")

## <a name="moving-the-robot"></a>Перемещение робота

Теперь, когда у нас есть `Robot` класс, мы можем добавить логику движения робота. В этом случае мы просто сделаю робота переместить круг в соответствии с игрового времени. Это довольно неудобно реализации для реальных игры, поскольку символ обычно может реагировать на ввод или искусственного интеллекта, но он предоставляет среду для нам и изучайте трехмерное позиционирование и поворота.

Единственные сведения, нам потребуется из за пределами `Robot` класс является текущее время игры. Мы добавим `Update` метод, который займет `GameTime` параметра. Это `GameTime` параметр будет использоваться для увеличения значения переменной угол, которая будет использоваться для определения окончательного позиции робота.

Во-первых, мы добавим поле угол `Robot` класса в группе `model` поля:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Теперь мы может увеличить это значение в `Update` функции:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Нам нужно убедиться, что `Update` метод вызывается из `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Само собой после этого поле «Угол» не выполняет никаких действий — нам требуется написать код для его использования. Мы изменим `Draw` метод таким образом, мы можем вычислить мира `Matrix` в выделенный метод: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Затем мы реализуем `GetWorldMatrix` метод в `Robot` класса:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

Результат выполнения этого кода приведет к робота, перемещение в круге:

![](part3-images/image5.gif "Выполнение этого кода приводит в робота, перемещение в кружке")

## <a name="matrix-multiplication"></a>Умножение матриц

В приведенном выше коде поворачивает робота, создав `Matrix` в `GetWorldMatrix` метод. `Matrix` Структура содержит 16 значений с плавающей запятой, которые могут использоваться для преобразования (задать положение), поворот и масштабирование (размер). Когда мы назначим `effect.World` свойство, мы указываем базового визуализации системы как для размещения, размер и ориентация все, что мы смогли выполняться рисование ( `Model` или geometry из вершин). 

К счастью `Matrix` структура включает в себя ряд методов, которые упрощают создание распространенных типов матриц. Первый, используемый в приведенном выше коде — `Matrix.CreateTranslation`. Математический термин *перевода* относится к операции, что приводит в точке (или в нашем случае модель) перемещение из одного расположения в другое без каких-либо других изменений (например, поворот или изменение размера). Функция принимает значение X, Y и Z для перевода. Если просмотреть наши сцены сверху вниз, наши `CreateTranslation` метод (в изоляции) выполняет следующее:

![](part3-images/image6.png "Метод CreateTranslation изолированно, выполняющий это действие")

Вторая матрица, которое мы создаем является матрицу поворота с помощью `CreateRotationZ` матрицы. Это один из трех методов, которые можно использовать для создания поворота:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Каждый метод создает матрицу поворота, повернув о данной оси. В нашем случае мы Поворот относительно оси Z, которое указывает «вверх». Следующие может помочь при визуализации на основе оси поворота works:

![](part3-images/image7.png "Это может помочь при визуализации на основе оси поворота works")

Мы также используем `CreateRotationZ` метод с полем угол, которое увеличивает со временем, из-за нашей `Update` вызываемого метода. В результате `CreateRotationZ` метод вызывает наши робота Солнца вокруг начала координат с течением времени.

В последней строке кода объединяет две матрицы в один:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Это упоминается как умножение матриц, который работает немного отличается от обычных умножения. *Коммутативности умножения* том, что порядок номеров в операции умножения не приводит к изменению результата. То есть 3 * 4 соответствует 4 * 3. Умножение матриц отличается тем, что он не является коммутативной. То есть приведенную выше строку может быть прочитан как «Применить translationMatrix для перемещения модели, а затем смените все путем применения rotationMatrix». Мы может визуализировать так, что выше строки влияет на положения и поворота следующим образом:

![](part3-images/image8.png "Pf визуализации так, что выше строки влияет на положения и поворота")

Чтобы понять, как порядок умножения матриц может повлиять на результат, рассмотрим следующую команду, где инвертируется Перемножение матриц:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

В приведенном выше коде сначала поворот модели на месте, а потом его преобразования:

![](part3-images/image9.png "В приведенном выше коде сначала поворот модели на месте, а потом его преобразования")

Если запустить код с инвертированный умножения, мы заметить, так как вращение применяется во-первых, он влияет только на ориентацию модели и положение модели остается изменений. Другими словами модель поворачивается на месте:

![](part3-images/image10.gif "Модель поворачивается на месте")

## <a name="creating-the-camera-entity"></a>Создание сущности камеры

`Camera` Сущность будет содержать все логику, необходимую для перемещения на основе входных данных и для предоставления свойств для присвоения свойства на `BasicEffect` класса.

Сначала мы реализации статического камеры (не на основе входных данных перемещение) и интегрировать его в нашей существующий проект. Добавьте новый класс к **MonoGame3D** переносимой библиотеки классов (же проект с `Robot.cs`) и назовите его **камеры**. Замените все содержимое этого файла следующим кодом:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

Приведенный выше код очень похож на код из `Game1` и `Robot` которые присваивают матрицы на `BasicEffect`. 

Теперь мы можете интегрировать новый `Camera` класс в нашем существующих проектов. Во-первых, мы изменим `Robot` класс, чтобы получить `Camera` экземпляра в его `Draw` метод, который будет исключить повторяющийся код. Замените `Robot.Draw` следующим кодом:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Затем измените `Game1.cs` файла:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Изменения `Game1` из предыдущей версии (который идентифицируются при помощи `// New camera code` ) являются:

- `Camera` Поля в `Game1`
- `Camera` При создании экземпляра в `Game1.Initialize`
- `Camera.Update` вызов в `Game1.Update`
- `Robot.Draw` Теперь принимает `Camera` параметр
- `Game1.Draw` Теперь использует `Camera.ViewMatrix` и `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Перемещение камеры с входными данными

На данный момент мы добавили `Camera` сущности, но не выполнил никаких действий с его, чтобы изменить поведение во время выполнения. Мы добавим поведение, которое позволяет пользователю для:

- В левой части экрана, чтобы включить камеру налево Touch
- Touch в правой части экрана, чтобы включить камеру справа
- Touch центральной части экрана для перемещения камеры вперед

### <a name="making-lookat-relative"></a>Что делает ежегодные показатели относительно

Сначала мы обновим `Camera` класса для включения `angle` поле, которое будет использоваться для задания направления, `Camera` направлена. В настоящее время наши `Camera` определяет направление лицевой через локальный `lookAtVector`, которое присваивается `Vector3.Zero`. Другими словами наши `Camera` всегда ищет в источнике. Если перемещение камеры, угол, с которыми сталкиваются камеры также изменится:

![](part3-images/image11.gif "Если перемещение камеры, затем угол, с которыми сталкиваются камеры также изменится")

Мы хотим `Camera` для сталкиваться направлении независимо от его положение — по крайней мере до мы реализуем логику для смены `Camera` с помощью входных данных. Первое изменение будет корректировать `lookAtVector` переменной должен быть основан на наших текущее расположение, а не абсолютное позиционирование просмотр:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

В результате `Camera` мир прямой вход. Обратите внимание, что начальное `position` значение было изменено для `(0, 20, 10)` поэтому `Camera` центрируется по оси X. Выполнение игры дисплеев.

![](part3-images/image12.png "Запуск игры отображаются в этом представлении")

### <a name="creating-an-angle-variable"></a>Создание переменной угла

`lookAtVector` Переменной определяет угол, просмотр наших камеры. В настоящее время она исправлена для просмотра вниз отрицательной полуоси Y, а немного фокусному вниз (от `-.5f` значение Z). Мы создадим `angle` переменной, которая будет использоваться для корректировки `lookAtVector` свойство. 

В предыдущих разделах данного пошагового руководства мы показали, что матрицы может использоваться для поворота способ рисования объектов. Можно также использовать матрицы поворота векторов как `lookAtVector` с помощью `Vector3.Transform` метод. 

Добавить `angle` поле и измените `ViewMatrix` свойства следующим образом:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Чтение входных данных

Наши `Camera` сущности теперь полностью осуществляется через его положения и угла переменные — нам просто нужно изменить их в соответствии с входных данных.

Во-первых, мы получаем `TouchPanel` состоянию для поиска, когда пользователь касается экрана. Дополнительные сведения об использовании `TouchPanel` , представлена в разделе [Справочник по TouchPanel API](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Если пользователь касается на третий слева, а затем мы скорректируем `angle` значение поэтому `Camera` поворачивает слева, и если пользователь касается на правом третий, мы будем поворот другим способом. Если пользователь касается в третьем средней части экрана, а затем мы перейдем `Camera` вперед.

Во-первых, добавьте с помощью оператора для уточнения `TouchPanel` и `TouchCollection` классы в `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Затем измените `Update` метод для чтения на сенсорной панели и настроить `angle` и `position` переменные соответствующим образом:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

Теперь `Camera` реагирует на сенсорный ввод:

![](part3-images/image1.gif "Теперь камеры, будут отвечать на сенсорный ввод")

Метод обновления начинается с вызова `TouchPanel.GetState`, который возвращает коллекцию штрихов. Несмотря на то что `TouchPanel.GetState` может возвращать несколько точек касания, мы будем принимать во внимание лишь первый из них для простоты.

Если пользователь касается экрана, затем код проверяет Если первого касания находится в левой части средней или прямо третьей части экрана. Втрое левого и правого поворот камеры, увеличив или уменьшив `angle` переменных в соответствии с `TotalSeconds` значение (таким образом, игра работает так же, независимо от частоты кадров).

Если пользователь касается центре третьих экрана, затем камеры будет переместить вперед. Для этого сначала Получив прямой вектор, который изначально определен как указывает сторону отрицательной полуоси Y, то поворачиваться матрицу, созданных с помощью `Matrix.CreateRotationZ` и `angle` значение. Наконец `forwardVector` применяется к `position` с помощью `unitsPerSecond` коэффициент.

## <a name="summary"></a>Сводка

В этом пошаговом руководстве описывается, как перемещать и поворачивать `Models` в трехмерном пространстве с помощью `Matrices` и `BasicEffect.World` свойство. Такая форма перемещение обеспечивает основу для перемещения объектов в трехмерных игр. В этом пошаговом руководстве также описывается, как реализовать `Camera` сущность для мир из любого положения и угла.

## <a name="related-links"></a>Связанные ссылки

- [Ссылку API MonoGame](http://www.monogame.net/documentation/?page=api)
- [Готовый проект (пример)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
