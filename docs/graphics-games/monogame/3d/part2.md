---
title: Рисование трехмерной графики с вершинами в MonoGame
description: MonoGame поддерживает использование массивов вершин для определения, просмотру трехмерного объекта на основе точки. Пользователи могут воспользоваться преимуществами массивы вершин для создания динамических geometry, реализовать специальные эффекты и повысить эффективность их подготовки к просмотру по поверхности.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121442"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Рисование трехмерной графики с вершинами в MonoGame

_MonoGame поддерживает использование массивов вершин для определения, просмотру трехмерного объекта на основе точки. Пользователи могут воспользоваться преимуществами массивы вершин для создания динамических geometry, реализовать специальные эффекты и повысить эффективность их подготовки к просмотру по поверхности._

Пользователи, обязательно прочитайте [руководство по визуализации моделей](~/graphics-games/monogame/3d/part1.md) будут знакомы с отрисовки 3D-модели в MonoGame. `Model` Класс является эффективным способом отрисовки 3D-графики, при работе с данными, определенные в файле (например, .fbx) и при работе со статическими данными. Некоторые игры требуют трехмерной геометрии, для которых будет обрабатываться динамически во время выполнения. В таких случаях можно использовать массивы *вершины* для определения и отрисовывать геометрию. Вершина – это общий термин для точки в трехмерном пространстве, который является частью упорядоченный список, используемый для определения geometry. Вершины, обычно упорядочиваются в таким образом, образуя серию треугольников.

Чтобы помочь визуализировать как вершин используются для создания трехмерных объектов, рассмотрим следующие сферы.

![](part2-images/image1.png "Чтобы помочь визуализировать как вершин используются для создания трехмерных объектов, рассмотрите возможность этой сферы")

Как показано выше, сферы четко состоит из нескольких треугольников. Мы могли просматривать каркас сферы как вершины, подключиться к треугольники формы:

![](part2-images/image2.png "Просмотреть каркас сферы как вершины, подключиться к треугольники формы")

В этом пошаговом руководстве рассматриваются следующие темы:

- Создание проекта
- Создание вершин
- Добавив код рисования
- Отрисовка с помощью текстуры
- Изменение координаты текстуры
- Подготовка к просмотру вершин с моделями

Готовый проект будет содержать клетчатого floor, в которой будет отображен с массивом вершин:

![](part2-images/image3.png "Готовый проект будет содержать клетчатого floor, в которой будет отображен с массивом вершины")

## <a name="creating-a-project"></a>Создание проекта

Во-первых мы будем загрузить проект, который будет использоваться в качестве нашей отправной точкой. Мы будем использовать проект модели [которого можно найти здесь](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

После загружены и распаковать, откройте и запустите проект. Мы ожидаем увидеть шесть моделей робота, отрисовывается на экране:

![](part2-images/image4.png "Шесть моделей робота, отрисовывается на экране")

К концу этого проекта мы будем сочетание собственную пользовательскую отрисовки с робота `Model`, поэтому мы не собираемся удалить код отрисовки робота. Вместо этого мы просто очистите `Game1.Draw` запрос на удаление рисунка 6 роботов сейчас. Чтобы сделать это, откройте **Game1.cs** файл и найдите `Draw` метод. Измените его, чтобы он содержал следующий код:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

В результате нашей игре, отображение пустой синий экран:

![](part2-images/image5.png "Это приведет к игре, отображение пустой синий экран")

## <a name="creating-the-vertices"></a>Создание вершин

Мы создадим массив вершин для определения наших geometry. В этом пошаговом руководстве мы создадим трехмерной проекции (квадрат в трехмерном пространстве, не самолете). Несмотря на то, что наши плоскости состоит из четырех сторон и четыре угла, он будет состоять из двух треугольниках, каждый из которых требует тремя вершинами. Таким образом мы будем определять всего шесть точек.

Пока мы разговаривали о вершин в общем смысле, но MonoGame предоставляет некоторые стандартные структуры, который может использоваться для вершин:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Имя каждого типа указывает компоненты, которые он содержит. Например `VertexPositionColor` содержит значения для положение и цвет. Давайте рассмотрим каждый из компонентов:

- Позиция — все типы вершин включают `Position` компонента. `Position` Значения определяют, где находится вершины в трехмерном пространстве (X, Y и Z).
- Цвет — вершин можно дополнительно указать `Color` значение для выполнения пользовательских изменяется исследуемое.
- Normal — нормали определение способа направлена поверхности объекта. Нормали необходимы, если Подготовка к просмотру объекта с освещением, так как направление, поверхность направлена влияет на объем света он получает. Нормали обычно указываются как *единичный вектор* – трехмерный вектор, который имеет длину 1.
- Текстуры — текстуры — это координаты текстуры — то есть часть текстуры должны отображаться в данной вершины. Значения текстуры необходимы в том случае, если Подготовка к просмотру трехмерный объект с текстурой. Координаты текстуры являются нормализованных координатах, что означает, что значения будет произведен от 0 до 1. Мы обсудим координаты текстуры более подробно далее в этом руководстве.

Наши плоскости будет служить этаж, и нам понадобятся для применения текстуры, при выполнении наших визуализации, поэтому мы будем использовать `VertexPositionTexture` типа для определения наших вершины.

Во-первых мы добавим элемент в наш класс Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Затем определите наших вершин в `Game1.Initialize`. Обратите внимание, что указанный шаблон, упомянутый в этой статье не содержит `Game1.Initialize` метод, поэтому нам нужно добавить всего способ `Game1`:

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

Чтобы помочь визуализировать, как будет выглядеть наш вершины, рассмотрим следующую диаграмму:

![](part2-images/image6.png "Чтобы помочь визуализировать, как будет выглядеть вершины, рассмотрите возможность на этой схеме")

Нам нужно полагаться на нашей схеме для визуализации вершин, пока не закончится реализация наш код отрисовки.

## <a name="adding-drawing-code"></a>Добавление кода отрисовки

Теперь у нас позиции для наших геометрии определен, можно написать код отрисовки.

Во-первых, нам нужно будет определить `BasicEffect` экземпляра, которая будет содержать параметры для отрисовки, например положение и освещения. Чтобы сделать это, добавьте `BasicEffect` члена `Game1` класс ниже места расположения `floorVerts` поле определено:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Затем измените `Initialize` метод для определения влияния:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Теперь можно добавить код для рисования:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

Нам потребуется вызвать `DrawGround` в наших `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

Приложение будет отображаться при выполнении следующих:

![](part2-images/image7.png "Приложение будет отображать это при выполнении")

Давайте взглянем на некоторые сведения в приведенном выше коде.

### <a name="view-and-projection-properties"></a>Просмотр свойств проекции и

`View` И `Projection` свойства определяют, как мы рассматриваем сцены. Мы будем модифицировать этот код позже, когда мы повторно добавьте код отрисовки модели. В частности `View` определяет местоположение и ориентацию камеры, и `Projection` элементов управления *поле зрения* (который может использоваться для изменения масштаба камеры).

### <a name="techniques-and-passes"></a>Методы и передает исключение

Один раз назначаются свойства на наших эффект, который мы можем выполнить отрисовку. 

Мы не будет изменять `CurrentTechnique` свойства в этом пошаговом руководстве, но более сложных игр могут иметь один эффект, который может выполнять рисование по-разному (например, как применяется значение цвета). Каждый из этих режимов отрисовки может быть представлено как методика, которые назначаются перед отрисовкой. Кроме того каждому из них может потребоваться несколько проходов для подготовки к просмотру правильно. Эффекты может потребоваться несколько проходов, если Подготовка к просмотру сложных визуальных элементов, таких как свечение область или заднему.

Важно помнить, что является то, что `foreach` цикла позволяет же C# код для подготовки к просмотру никакого вне зависимости от сложности базовой `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` — где вершины подготавливаются к просмотру. Первый параметр определяет метод, как мы организованы наших вершины. Мы предусмотрели их таким образом, чтобы каждый треугольник определяется тремя вершинами упорядоченный, поэтому мы используем `PrimitiveType.TriangleList` значение.

Второй параметр — массив вершины, которые мы определили ранее.

Третий параметр указывает первый индекс для рисования. Поскольку мы хотим нашего массива всего вершин для визуализации, передадим значение 0.

Наконец мы указываем числа треугольников для подготовки к просмотру. Нашего массива вершин содержит двух треугольниках, таким образом передать значение 2.

## <a name="rendering-with-a-texture"></a>Отрисовка с помощью текстуры

На этом этапе наше приложение отображает белый плоскости (в перспективе). Далее мы добавим текстуры в свой проект, используемый при отрисовке наших плоскости. 

Для простоты мы добавим .png непосредственно в наш проект, а не с помощью средства MonoGame конвейера. Чтобы сделать это, загрузите [PNG-файл](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) к компьютеру. После скачивания, щелкните правой кнопкой мыши **содержимого** папки в панели решения и выберите **Добавить > Добавить файлы...**  . Если на устройстве Android, затем эта папка будет размещена в **активы** папки в проекте Android под конкретную. Если на устройстве iOS, то эта папка будет в корне проекта iOS. Перейдите к расположению, где **checkerboard.png** сохраняется, а затем выберите этот файл. Выберите, чтобы скопировать файл в каталог.

Далее мы добавим код для создания нашей `Texture2D` экземпляра. Во-первых, добавьте `Texture2D` членом `Game1` под `BasicEffect` экземпляр:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Изменить `Game1.LoadContent` следующим образом:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Затем измените `DrawGround` метод. Единственным необходимым изменением является назначение `effect.TextureEnabled` для `true` и задать `effect.Texture` для `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Наконец, нам нужно изменить `Game1.Initialize` метод также назначения текстуры координирует на наших вершины:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Если выполнить код, мы видим, что наши плоскости теперь отображает шахматной:

![](part2-images/image8.png "Плоскость теперь отображает шахматной")

## <a name="modifying-texture-coordinates"></a>Изменение текстуры координирует

Использует MonoGame нормализовать координаты текстуры, которые являются координаты от 0 до 1, а не между 0 и текстуры ширины или высоты. Следующая схема может помочь при визуализации нормализованных координатах:

![](part2-images/image9.png "На этой схеме могут помочь визуализировать нормализованных координатах")

Координаты текстуры нормализованное допускают текстуру без необходимости переписывать код или повторное создание модели (например, файлы .fbx). Это возможно, так как Нормализованная координаты представляют отношение, а не в определенных точках. Например (1,1) будут всегда представляют нижний правый угол независимо от размера текстуры.

Мы можем изменить назначение координат текстуры для использования одной переменной для число повторений:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
}
```

Это приводит к текстуры, повторив 20 раз:

![](part2-images/image10.png "Это приводит к текстуры, повторив 20 раз")


## <a name="rendering-vertices-with-models"></a>Подготовка к просмотру вершин с моделями

Теперь, когда наши плоскости отрисовки должным образом, мы можем повторно добавить модели, которые будут просматривать все вместе. Во-первых, мы повторно добавим код модели, чтобы наши `Game1.Draw` метод (с измененной позиций):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

Мы также создадим `Vector3` в `Game1` для представления положения наших камеры. Мы добавим поле в разделе наших `checkerboardTexture` объявление:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Затем удалите локальной `cameraPosition` переменной из `DrawModel` метод:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Аналогичным образом удалить локальный `cameraPosition` переменной из `DrawGround` метод:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Теперь запускаем код можно видеть моделей и нуля в то же время:

![](part2-images/image11.png "Модели и во всех отношениях, отображаются в то же время")

Если нужно изменить положение камеры (например, увеличив его значения X, которой в данном случае перемещает камеры влево) видно, что значение влияет на все аспекты и модели:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Этот код приведет к следующему:

![](part2-images/image3.png "Этот код вызывает в этом представлении")

## <a name="summary"></a>Сводка

В этом пошаговом руководстве было показано, как использовать массив вершин для выполнения пользовательской отрисовки. В этом случае мы создали клетчатого floor, объединяя наших подготовки отчетов на основе вершин с текстурой и `BasicEffect`, но код показан здесь служит основой для любой трехмерной прорисовки. Мы также показали, что вершины, в основе визуализации могут сочетаться с моделями в той же сцены.

## <a name="related-links"></a>Связанные ссылки

- [Файл выглядящий как шахматная доска (пример)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Завершенный проект (пример)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
