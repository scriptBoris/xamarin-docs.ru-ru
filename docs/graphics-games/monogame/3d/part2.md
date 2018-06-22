---
title: Рисование трехмерной графики с вершины в MonoGame
description: MonoGame поддерживает использование массивов вершин для определения просмотру трехмерный объект отдельно для каждой точки. Пользователи могут воспользоваться преимуществами массивы вершин для создания динамических geometry, реализовать специальные эффекты и повысить эффективность их отрисовки через отбора.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 4fa6929a8de19608c06690e4cf24514f6749480a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921486"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Рисование трехмерной графики с вершины в MonoGame

_MonoGame поддерживает использование массивов вершин для определения просмотру трехмерный объект отдельно для каждой точки. Пользователи могут воспользоваться преимуществами массивы вершин для создания динамических geometry, реализовать специальные эффекты и повысить эффективность их отрисовки через отбора._

Пользователей, которые прочтите [руководство по подготовке к просмотру модели](~/graphics-games/monogame/3d/part1.md) будут знакомы с отрисовки 3D-модели в MonoGame. `Model` Класс — это эффективный способ для отображения трехмерной графики при работе с данными, определенные в файле (например, .fbx) и при работе со статическими данными. Некоторым играм требуется 3D geometry, для которых будет обрабатываться динамически во время выполнения. В этих случаях можно использовать массивы *вершин* для определения и отображения геометрии. Узел — это общий термин для точки в трехмерном пространстве, который является частью упорядоченный список используется для определения geometry. Обычно вершины упорядочиваются в таким образом, чтобы определить серию треугольников.

Наглядность использовании вершин для создания трехмерных объектов, рассмотрим следующие сфере.

![](part2-images/image1.png "Визуализация использовании вершин для создания трехмерных объектов необходимо учитывайте это сфере")

Как показано выше, сфере четко состоит из нескольких треугольников. Можно просматривать каркаса сферы как вершинах подключиться к треугольники формы:

![](part2-images/image2.png "Просмотр каркаса сферы как вершинах подключиться к форме треугольника")

В этом пошаговом руководстве рассматриваются следующие темы:

- Создание проекта
- Создание вершин
- Добавление кода для рисования
- Подготовка к просмотру с текстурным заполнением
- Изменение координаты текстуры
- Подготовка к просмотру вершины с моделями

По завершении проект будет содержать клетчатого floor, в которой будет перерисовываться с помощью массива вершин:

![](part2-images/image3.png "По завершении проект будет содержать клетчатого floor, в которой будет перерисовываться с помощью массива вершин")

## <a name="creating-a-project"></a>Создание проекта

Сначала мы будем загрузите проект, который будет служить нашей отправной точки. Мы будем использовать проект модели [которого можно найти здесь](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

После загруженный распакованную, откройте и запустите проект. Ожидается, что шесть робота с моделями отрисовывается на экране:

![](part2-images/image4.png "Шесть моделей робот отрисовывается на экране")

В конце этого проекта мы будем объединения собственную пользовательскую отрисовку с робота `Model`, поэтому мы не будем удалить код отрисовки робот. Вместо этого мы просто очистить `Game1.Draw` запрос на удаление рисунка 6 роботов сейчас. Чтобы сделать это, откройте **Game1.cs** и найдите `Draw` метод. Измените его, чтобы он содержал следующий код:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

В результате наш игры, отображение пустой синий экран:

![](part2-images/image5.png "Это приведет к игре, отображение пустой синий экран")

## <a name="creating-the-vertices"></a>Создание вершин

Мы создадим массив вершин для определения нашей geometry. В этом пошаговом руководстве будут созданы трехмерной плоскости (квадрат в трехмерном пространстве, не самолете). Несмотря на то, что наши плоскости имеет четыре стороны и четыре угла, он будет состоять из двух треугольников, каждый из которых требуется три вершины. Таким образом мы будем определять всего шесть точек.

Пока мы идет речь о вершин в общем смысле, но MonoGame предоставляет некоторые стандартные структуры, который может использоваться для вершин:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Имя каждого типа указывает компоненты, которые он содержит. Например `VertexPositionColor` содержит значения для положение и цвет. Давайте рассмотрим каждый из компонентов:

- Включает все типы вершин позиции — `Position` компонента. `Position` Значения определяют, где вершины находится в трехмерном пространстве (X, Y и Z).
- Цвет — вершин можно дополнительно указать `Color` для выполнения пользовательских оттенков.
- Обычный — нормали определение способа обращен к поверхности объекта. Нормали необходимы, если отрисовки объект с освещения с момента направление, рабочую область с выходом влияет на объем light получит. Нормали обычно указываются как *единичным вектором* – трехмерный вектор, который имеет длину 1.
- Текстуры — текстуры ссылается координаты текстуры — то есть, какая часть текстуры должны отображаться в данной вершин. Значения текстуры необходимы при подготовке к просмотру 3D-объекта с текстурным заполнением. Координаты текстуры, нормализованное координаты означает, что значения будет произведен от 0 до 1. Мы обсудим координаты текстуры более подробно далее в этом руководстве.

Наш плоскости будет служить этаж и будем использовать текстуры, при выполнении нашей модуль подготовки отчетов, поэтому мы будем использовать `VertexPositionTexture` тип, чтобы определить нашей вершин.

Во-первых мы добавим элемент нами класса Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Затем определите нашей вершин в `Game1.Initialize`. Обратите внимание, что указанный шаблон, указанным ранее в этой статье не содержит `Game1.Initialize` метод, поэтому необходимо добавить весь метод `Game1`:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Чтобы представить, как будет выглядеть наш вершин, рассмотрим следующую диаграмму:

![](part2-images/image6.png "Визуализация, как будет выглядеть вершин, необходимо учитывайте эту диаграмму")

Мы должны полагаться на наша диаграмма для визуализации вершин, пока мы завершаем работу реализации наш код отрисовки.

## <a name="adding-drawing-code"></a>Добавление кода для рисования

Теперь, когда есть позиции для наших геометрии определен, можно написать код отрисовки.

Во-первых, необходимо определить `BasicEffect` экземпляр, который будет содержать параметры для подготовки к просмотру, такие как положение и освещение. Чтобы сделать это, добавьте `BasicEffect` члена `Game1` класс ниже where `floorVerts` поле определяется:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Затем измените `Initialize` метод для определения влияния:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Теперь можно добавить код для изображения:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start 
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

Нам нужно будет вызывать `DrawGround` в нашем `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

Приложение будет отображать при выполнении следующих:

![](part2-images/image7.png "Приложение будет отображать это при выполнении")

Рассмотрим некоторые сведения в приведенном выше коде.

### <a name="view-and-projection-properties"></a>Просмотр и свойств проекции

`View` И `Projection` свойства определяют, как мы просмотреть сцены. Мы будем изменения этот код позже при мы повторно добавьте код отрисовки модели. В частности `View` определяет расположение и ориентацию камеры, и `Projection` элементов управления *поля зрения* (который можно использовать для изменения масштаба камеры).

### <a name="techniques-and-passes"></a>Методы и передает исключение

Один раз мы назначенные свойства на нашем эффект, который можно установить фактической визуализации. 

Мы не будут изменены `CurrentTechnique` свойства в пошаговом руководстве, но более сложных игр могут иметь один эффект, который может выполнять рисование по-разному (например, как применяется значение цвета). Каждый из этих режимов визуализации можно представить как метод, то можно назначить перед отрисовкой. Кроме того каждый метод может понадобиться несколько передач для подготовки к просмотру правильно. Эффекты может понадобиться несколько проходов, если визуализации сложных визуальный элемент, например свечение поверхности и внезапно.

Важно необходимо иметь в виду, что `foreach` цикла позволяет же код C# для подготовки к просмотру вступать в силу независимо от сложности базового `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` — где вершины подготавливаются к просмотру. Первый параметр определяет метод, как мы организованы нашей вершин. Мы структурированные их, чтобы каждый треугольник определяется три упорядоченный вершин, поэтому мы используем `PrimitiveType.TriangleList` значение.

Второй параметр — массив вершин, которым было определено ранее.

Третий параметр указывает первый индекс для рисования. Поскольку мы хотим нашего всего вершин массива должен быть подготовлен к просмотру, мы будем передать значение 0.

Наконец мы указываем сколько треугольники для подготовки к просмотру. Наш вершин массив содержит два треугольники, таким образом передать значение 2.

## <a name="rendering-with-a-texture"></a>Подготовка к просмотру с текстурным заполнением

На этом этапе нашего приложения подготавливается белый плоскости (в перспективе). Далее мы добавим текстуры в свой проект для использования при подготовке к просмотру нашей плоскости. 

Для простоты мы добавим .png напрямую в данном проекте, а не с помощью средства MonoGame конвейера. Чтобы сделать это, загрузите [PNG-файл](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) к компьютеру. После загрузки, щелкните правой кнопкой мыши **содержимого** папки в панель решения и выберите **Добавить > Добавить файлы...**  . При работе на Android, затем эта папка будет размещена в **активы** Android конкретного проекта. Если на iOS, то эта папка будет в корне проекта iOS. Перейдите к расположению где **checkerboard.png** сохраняется и выберите этот файл. Выберите, чтобы скопировать файл в каталог.

Далее мы добавим код, чтобы создать нашей `Texture2D` экземпляра. Сначала добавьте `Texture2D` членом `Game1` под `BasicEffect` экземпляр:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Изменить `Game1.LoadContent` следующим образом:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Затем измените `DrawGround` метод. Только необходимые изменения, — присвоить `effect.TextureEnabled` для `true` и задать `effect.Texture` для `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Наконец, нам нужно изменить `Game1.Initialize` метод присвоить текстуры координаты на нашем вершин:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Если мы запустим код, мы видим, что наши плоскости теперь отображаются шахматной:

![](part2-images/image8.png "Плоскость теперь отображает шахматной")

## <a name="modifying-texture-coordinates"></a>Изменение текстуры координаты

Использует MonoGame нормализовать координаты текстуры, что координаты от 0 до 1, а не от 0 до текстуры ширины или высоты. Следующая схема может помочь визуализировать нормализованный координаты:

![](part2-images/image9.png "На этой диаграмме можно помогают визуализировать нормализованный координаты")

Координаты текстуры нормализованный разрешение на изменение размеров текстуры без необходимости переписать код или повторное создание модели (например, файлы .fbx). Это можно сделать, так как Нормализованная координаты представляют отношение, а не в определенных точках. Например (1,1) будет всегда представляют нижний правый угол независимо от размера текстуры.

Мы можем изменить назначение координат текстуры используйте одну переменную для числа повторов:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
}
```

Это приводит к текстуры, повторение 20 раз:

![](part2-images/image10.png "Это приведет к повторение 20 раз текстуры")


## <a name="rendering-vertices-with-models"></a>Подготовка к просмотру вершины с моделями

Теперь, когда наши плоскости готовится к просмотру правильно, можно повторно добавить модели, чтобы просмотреть все объекты вместе. Во-первых, мы повторно добавить код модели для наших `Game1.Draw` метод (с измененный позиций):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

Также мы создадим `Vector3` в `Game1` для представления положение нашей камеры. Мы добавим поле в нашем `checkerboardTexture` объявление:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Затем удалите локальной `cameraPosition` переменную `DrawModel` метод:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Аналогичным образом удалить локальный `cameraPosition` переменную `DrawGround` метод:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Теперь если мы запустим код в то же время мы могут видеть моделей и нуля:

![](part2-images/image11.png "В то же время отображаются моделей и нуля")

В случае изменения позиция камеры (например, путем увеличения значения X которого в этом случае перемещает камеру слева) видно, что оказывает влияние на значение нуля и модели:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Этот код вызывает следующие:

![](part2-images/image3.png "Этот код вызывает в этом представлении")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве показано, как использовать массив вершин для выполнения пользовательской отрисовки. В этом случае мы создали клетчатого floor, объединяя нашей подготовки отчетов на основе вершин с текстурным заполнением и `BasicEffect`, но код представленных здесь служит основой для любой трехмерной отрисовки. Также мы показали, что визуализации на основе вершины можно комбинировать с моделей в одной сцене.

## <a name="related-links"></a>Связанные ссылки

- [Файл шахматной доски (пример)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
