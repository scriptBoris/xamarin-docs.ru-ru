---
title: Объемные координаты в MonoGame
description: Основные сведения о системе координат трехмерной является важным этапом при разработке трехмерных игр. MonoGame предоставляет ряд классов для позиционирования, ориентация и масштабирование объектов в трехмерном пространстве.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 2f14d21302ed4295d16baa28723df6ef79863686
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921642"
---
# <a name="3d-coordinates-in-monogame"></a>Объемные координаты в MonoGame

_Основные сведения о системе координат трехмерной является важным этапом при разработке трехмерных игр. MonoGame предоставляет ряд классов для позиционирования, ориентация и масштабирование объектов в трехмерном пространстве._

Разработка трехмерных игр требует понимания того, как работать с объектами в трехмерной системе координат. В этом пошаговом руководстве мы рассмотрим способы управления визуальных объектов (в частности, модель). Мы выполним сборку основные понятия управления модели для создания класса 3D камеры.

Представлены основные понятия берутся из линейной алгебры, но мы рассмотрим практическим подходом, что любой пользователь без строгого математические фона можно применить этих концепций в своих собственных игр.

Мы будем рассматривать следующие вопросы:

- Создание проекта
- Создание сущности робота
- Перемещение объект робота
- Умножение матриц
- Создание сущности камеры
- Перемещение камеры с входными данными

После завершения процесса, мы еще проект с робот, перемещение в кружке и камеры, который может управляться сенсорный ввод:

![](part3-images/image1.gif "После завершения приложения будет включать проект с робот, перемещение в кружке и камеры, который может управляться сенсорный ввод")


## <a name="creating-a-project"></a>Создание проекта

В этом пошаговом руководстве уделяется перемещение объектов в трехмерном пространстве. Начнем с проектом во время подготовки к просмотру модели и массивы вершин [которого можно найти здесь](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). После загрузки, распаковка и открытие проекта убедитесь, что он работает, и мы увидим следующее:

![](part3-images/image2.png "После загрузки, распаковка и откройте проект, убедитесь, что он работает и должны отображаться в этом представлении")


## <a name="creating-a-robot-entity"></a>Создание сущности робота

Прежде чем начать перемещение нашей робот вокруг, мы создадим `Robot` класс, содержащий логику рисования и перемещения. Разработчиков игр ссылаться на это инкапсуляция логику и данные в виде *сущности*.

Добавьте новый файл пустым классом для **MonoGame3D** переносимой библиотеки классов (не ModelAndVerts.Android специфический для платформы). Назовите его **робота** и нажмите кнопку **New**:

![](part3-images/image3.png "Назовите его робот и нажмите кнопку Создать")

Изменить `Robot` следующим образом:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

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
    }
}
```

`Robot` Код — по существу тот же код в `Game1` для рисования `Model`. Для просмотра на `Model` загрузки и отображения, в разделе [это руководство по работе с моделями](~/graphics-games/monogame/3d/part1.md). Мы теперь можно удалить все `Model` Загрузка и Подготовка к просмотру кода из `Game1`и замените ее строкой `Robot` экземпляр:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Если мы запустим код теперь у нас будет есть сцены с только один робот, который рисуется главным образом в пол:

![](part3-images/image4.png "Если код выполняется сейчас, приложение будет отображать это может быть только один робот, который рисуется главным образом в пол")

## <a name="moving-the-robot"></a>Перемещение робота

Теперь, когда у нас есть `Robot` класса, мы можем добавить логику перемещения робота. В этом случае мы просто выполним робота перемещения в соответствии с временем игры круг. Это довольно нецелесообразным реализацию для реальных игры, поскольку символ обычно может отвечать на входных данных или искусственного аналитики, но он предоставляет среду, мы будем исследовать трехмерное позиционирование и поворота.

Единственные сведения, нам нужно из вне `Robot` класс является текущим временем игры. Мы добавим `Update` метод, который будет принимать `GameTime` параметра. Это `GameTime` он будет использоваться для увеличения значения переменной угол, мы будем использовать для определения позиции последней робота.

Во-первых, мы добавим поле угол `Robot` класса в группе `model` поля:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Теперь мы можно увеличивать это значение в `Update` функции:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Нам нужно убедиться, что `Update` метод вызывается из `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Конечно на этом этапе угол поле ничего не делает — нам требуется писать код для его использования. Мы изменим `Draw` метод, чтобы мира, можно вычислить `Matrix` в выделенном метод: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Далее мы реализовать `GetWorldMatrix` метод `Robot` класса:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

Результат выполнения этого кода приведет к робот, перемещение в кружке:

![](part3-images/image5.gif "Выполнение этого кода приводит в робот, перемещение в кружке")

## <a name="matrix-multiplication"></a>Умножение матриц

Приведенный выше код поворачивает робота путем создания `Matrix` в `GetWorldMatrix` метод. `Matrix` Структура содержит 16 значений с плавающей запятой, которые можно использовать для преобразования (позиция набора), поворот и масштабирование (размер). Когда мы присваиваете `effect.World` свойства, мы указываем визуализации системы как положение, размер и ориентация независимо от базового мы столкнулись Рисование ( `Model` или geometry из вершин). 

К счастью `Matrix` структура включает в себя ряд методов, которые упрощают создание общих типов матрицы. Первый используемый в приведенном выше коде — `Matrix.CreateTranslation`. Математический термин *перевода* относится к операции, что приводит в точке (или в нашем случае модель) переноса из одного расположения в другое без каких-либо изменений (например, поворот или изменение размера). Функция принимает значение X, Y и Z для преобразования. Если мы просмотреть наши сцены из сверху вниз, наши `CreateTranslation` метод (изолированно) выполняет следующее:

![](part3-images/image6.png "Метод CreateTranslation в изоляции, выполняющий это действие")

Вторая матрица, мы создадим является матрицу поворота с использованием `CreateRotationZ` матрицы. Это один из трех методов, которые можно использовать для создания поворота:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Каждый метод создает матрицу поворота, повернув о данной оси. В нашем случае мы поворота относительно оси Z, которое указывает «вверх». Следующие можно составить наглядное представление на основе ось вращения works:

![](part3-images/image7.png "Это может помочь в визуализации на основе ось вращения работает")

Мы также используем `CreateRotationZ` метод с полем угол увеличивается в течение времени, из-за нашей `Update` вызываемого метода. В результате `CreateRotationZ` метод вызывает нашей робот Солнца вокруг начала координат с течением времени.

Последняя строка кода объединяет две матрицы в одно:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Это называется умножение матриц, который работает несколько отличаются от обычных умножения. *Коммутативности умножения* том, что результат не изменить порядок числа в операции умножения. То есть 3 * 4 соответствует 4 * 3. Умножение матриц отличается тем, что он не является коммутативной. То есть выше строки могут считываться как «Применить translationMatrix для перемещения модели, а затем повернуть все данные, применяя rotationMatrix». Нам удалось визуализировать способом, что выше строки влияет на положения и поворота следующим образом:

![](part3-images/image8.png "Визуализация pf способом, что выше строки влияет на положения и поворота")

Чтобы понять, как порядок умножения матрицы может повлиять на результат, рассмотрим следующую команду, где инвертированный умножение матриц.

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

Приведенный выше код сначала поворот модели на месте, а потом перевода:

![](part3-images/image9.png "Приведенный выше код сначала поворот модели на месте, а потом перевода")

Если мы запустим код при выполнении инвертированном умножения, мы Обратите внимание, поскольку сначала применяет поворот, оно влияет только на ориентацию модели и позицию модели остается прежним. Другими словами модель поворачивается на месте:

![](part3-images/image10.gif "Модель поворачивается на месте")

## <a name="creating-the-camera-entity"></a>Создание сущности камеры

`Camera` Сущность будет содержать всю логику, необходимую для выполнения перемещения на основе входных данных и укажите свойства для назначения свойств на `BasicEffect` класса.

Сначала мы реализации статического камеры (не на основе входных данных перемещение) и интегрировать его в нашем существующего проекта. Добавьте новый класс **MonoGame3D** переносимой библиотеки классов (же проект с `Robot.cs`) и назовите его **камеры**. Замените все содержимое этого файла следующим кодом:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

Приведенный выше код очень похож на код из `Game1` и `Robot` которые присваивают матрицы на `BasicEffect`. 

Теперь мы можно интегрировать новый `Camera` класс в нашем существующих проектов. Во-первых, мы изменим `Robot` класс, чтобы получить `Camera` экземпляра в его `Draw` метод, который позволит избежать много повторяющийся код. Замените `Robot.Draw` метод со следующими:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Затем измените `Game1.cs` файла:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Изменения в `Game1` из предыдущей версии (который идентифицируются с помощью `// New camera code` ) являются:

- `Camera` Поля в `Game1`
- `Camera` При создании экземпляра в `Game1.Initialize`
- `Camera.Update` вызов в `Game1.Update`
- `Robot.Draw` Теперь принимает `Camera` параметр
- `Game1.Draw` Теперь использует `Camera.ViewMatrix` и `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Перемещение камеры с входными данными

Итак, мы добавили `Camera` сущности, но еще не выполнено никаких действий с ее, чтобы изменить поведение среды выполнения. Мы добавим поведение, которое позволяет пользователю для:

- В левой части экрана, чтобы включить камеру налево Touch
- В правой части экрана, чтобы включить камеру справа Touch
- В центре экрана для перемещения камеры вперед сенсорного ввода

### <a name="making-lookat-relative"></a>Делая lookAt относительно

Сначала мы обновим `Camera` класса для включения `angle` поля, которое будет использоваться для задания направления, `Camera` обращен. В настоящее время наши `Camera` определяет направление лицевой через локальный `lookAtVector`, которая назначается `Vector3.Zero`. Другими словами наши `Camera` всегда выглядит в источнике. Если перемещается камеры, угол, испытывает камеры также изменится:

![](part3-images/image11.gif "Если перемещается камеры, затем угол, испытывает камеры повлечет за собой изменение")

Мы хотим `Camera` для столкнуться тому же направлению, независимо от его положение — по крайней мере, пока мы реализовали алгоритм для изменения `Camera` с помощью входных данных. Первое изменение будет Настройка `lookAtVector` переменной, в зависимости от наших текущее расположение, а не абсолютное позиционирование просмотр:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

В результате `Camera` Просмотр world прямой вход в систему. Обратите внимание, что начальный `position` значение было изменено для `(0, 20, 10)` поэтому `Camera` центрируется на оси X. Выполнение отображает игры.

![](part3-images/image12.png "Выполнение игры отображаются в этом представлении")

### <a name="creating-an-angle-variable"></a>Создание переменной угол

`lookAtVector` Переменной определяет угол, просмотр нашей камеры. В настоящее время является фиксированной для просмотра вниз отрицательное оси Y и немного наклона вниз (от `-.5f` значение Z). Мы создадим `angle` переменную, которая будет использоваться для настройки `lookAtVector` свойство. 

В предыдущих подразделах в этом пошаговом руководстве мы показали, что матрицы может использоваться для поворота способ рисования объектов. Мы также позволяет матрицы поворота векторов как `lookAtVector` с помощью `Vector3.Transform` метод. 

Добавить `angle` поля и изменение `ViewMatrix` свойства следующим образом:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Чтение входных данных

Наш `Camera` сущности теперь полностью осуществляется через его положение и угол переменные — нам нужно изменить их в соответствии с входных данных.

Во-первых, мы получаем `TouchPanel` состоянию для поиска, когда пользователь касается экрана. Дополнительные сведения об использовании `TouchPanel` см. в описании [Справочник по TouchPanel API](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Если пользователь касается на третий слева, а затем мы будем настроим `angle` значение того, чтобы `Camera` поворачивает слева, и если пользователь касается на третий вправо, мы будем поворот другим способом. Если пользователь касается в третьем средней части экрана, а затем мы перейдем `Camera` вперед.

Во-первых, добавить с помощью инструкции для определения `TouchPanel` и `TouchCollection` классы в `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Затем измените `Update` метод чтения панели сенсорный ввод и настраивать `angle` и `position` переменные соответствующим образом:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

Теперь `Camera` будет реагировать на сенсорный ввод:

![](part3-images/image1.gif "Теперь камеры будет реагировать на сенсорный ввод")

Метод обновления начинается путем вызова метода `TouchPanel.GetState`, который возвращает коллекцию штрихов. Несмотря на то что `TouchPanel.GetState` может возвращать несколько точки касания, мы будем принимать во внимание первый для простоты.

Если пользователь касается экрана, код проверяет влево, по центру или вправо, третий экрана ли первого касания. Втрое левого и правого поворота камеры путем увеличения или уменьшения `angle` переменных в соответствии с `TotalSeconds` (что игра работает так же, независимо от частоты кадров).

Если пользователь касается центре третий экрана, затем камеры будут перемещаться вперед. Для этого сначала с помощью получения прямого вектор, который изначально определен как указывающим отрицательное оси Y, то поворачиваться матрицу, созданные с помощью `Matrix.CreateRotationZ` и `angle` значение. Наконец `forwardVector` применяется к `position` с помощью `unitsPerSecond` коэффициент.

## <a name="summary"></a>Сводка

В этом пошаговом руководстве описывается, как перемещать и вращать `Models` в трехмерном пространстве с помощью `Matrices` и `BasicEffect.World` свойства. Эта форма перемещения обеспечивает основу для перемещения объектов трехмерных игр. В этом пошаговом руководстве также описано, как реализовать `Camera` сущности для просмотра world из любой позиции и угол.

## <a name="related-links"></a>Связанные ссылки

- [Ссылка MonoGame API](http://www.monogame.net/documentation/?page=api)
- [По завершении проекта (пример)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
