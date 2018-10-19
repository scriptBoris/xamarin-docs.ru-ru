---
title: Использование UrhoSharp для сборки трехмерной игры
description: Этот документ предоставляет обзор UrhoSharp, описывающий сцены, компоненты, фигур, камеры, действия, ввод данных пользователем, звук и многое другое.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 7d07733ebf62e6e12ccee05f9b72eaf1a74afad2
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "34784043"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Использование UrhoSharp для сборки трехмерной игры

Прежде чем писать свою первую игру, вы хотите ознакомьтесь с основными функциями: как настроить в сцену, загрузка ресурсов (этот файл содержит иллюстрации) и как создать простое взаимодействие для игры.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Сцены, узлов, компоненты и камеры

Модели сцены можно описать как граф сцены на основе компонентов. Сцены состоит из иерархии узлов сцены, начиная с корневого узла, который также представляет всей сцены. Каждый [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) имеет 3D-преобразование (положение, поворота и масштабирования), имя, идентификатор, а также произвольное число компонентов.  Компоненты, переведите этот узел в жизнь, они могли добавить визуальное представление ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), они могут генерируют звук ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), они могут предоставить границе конфликтов и т. д.

Можно создать сцен и настройка узлов с помощью [Urho редактор](#UrhoEditor), или вы можете выполнять действия в коде C#.  В этом документе мы рассмотрим параметр процесса с помощью кода, как они показывают элементы, необходимые для верного отображения на экране

В дополнение к настройке в сцену, необходимо установить [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), это, что определяет, что будет получить отображаться пользователю.

### <a name="setting-up-your-scene"></a>Настройка в сцену

Эта форма обычно создают способа запуска:

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>Компоненты

Отрисовка трехмерных объектов, воспроизведение звука, физики и записанную логику обновления они все включены путем создания различных компонентов в узлы, вызвав [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Например установки, узел и света компонент следующим образом:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

Мы создали выше узел с именем "`DirectionalLight`" и задайте направление для его, но ничего более.  Теперь мы можно включить выше узел в узел создающие свет, присоединив [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) компонент, с помощью `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

Компоненты, созданные в `Scene` сама по себе имеет особую роль: для реализации функций сцены. Они должны создаваться перед всеми остальными компонентами и включают следующее:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): реализует пространственного секционирования и ускоренных запросов видимости. Без этого трехмерных объектов не могут быть представлены.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): реализует физическое моделирование. Физических компонентов, таких как [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) или [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) может работать неправильно без этого.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): реализует отладка отрисовки геометрического объекта.

Обычные компоненты, такие как [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) или [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
не следует создавать непосредственно в [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), но вместо этого в дочерних узлов.

Библиотека поставляется с разнообразными компоненты, которые можно подключить к узлам, чтобы вернуть их в жизнь: видимыми элементами (модели), звуки, жесткой тел, конфликт фигур, камеры, источники света, отправители частиц и многое другое.

### <a name="shapes"></a>Фигур

Для удобства как простой узлы в пространстве имен Urho.Shapes доступны различные фигуры.  К ним относятся поля, сферы, конусы, цилиндров и плоскости.

### <a name="camera-and-viewport"></a>Продажи фотоаппаратов и окна просмотра

Так же, как источник света, камеры являются компонентами, поэтому требуется подключение к узлу компонента, это делается следующим образом:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Таким образом, вы создали камеры и вы разместили камеры в трехмерном мире, следующим шагом является информирование `Application` что это камеры, который вы хотите использовать, это делается следующим кодом:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

И теперь можно увидеть результаты создания.

### <a name="identification-and-scene-hierarchy"></a>Идентификация и сцены иерархии

В отличие от узлов компоненты не имеют имен; компоненты, в том же узле только идентифицируются по их типу и индекс в списке компонентов на узле, который заполняется порядок создания, например, вы можете получить [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) компонент из `lightNode` объекта выше следующим образом:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

Можно также получить список всех компонентов, получая [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) свойство, которое возвращает `IList<Component>` , можно использовать.

При создании, узлы и компоненты получают идентификаторы сцены Глобальное целое число. Они могут быть запрошены из сцены с помощью функций [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) и [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Это намного быстрее, чем например рекурсивные запросы сцены на основе имени узла.

Отсутствует понятие встроенные сущности или объекта игры; Это на программиста выбрать узел иерархии, а также в узлы, которые следует поместить логику в скрипте. Как правило бесплатные перемещение объектов в трехмерный мир будут создаваться как дочерние элементы корневого узла. Узлы могут создаваться с или без имени с использованием [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). Не обеспечивается уникальность имен узла.

При наличии некоторых иерархических композиции, это рекомендуемое (и на самом деле необходимо, так как компоненты не имеют свои собственные трехмерными преобразованиями) для создания дочернего узла.

Например, если символ удерживает объекта в руку, объекта должен быть собственный узел, который будет содержаться родительские объекты кости наличии символа (также [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  Исключением является Физика [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), который может быть offsetted и повернутый по отдельности по отношению к узла.

Обратите внимание, что [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)в собственные преобразования намеренно учитывается для оптимизации, при вычислении преобразования world производным дочерних узлов, поэтому изменение не влияет и его следует оставить, так как это (позиция, с сервера-источника без поворота без масштабирования.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) узлы могут быть свободно изменен порядок наследования. В отличие от компонентов всегда принадлежит узел, подключенный к они не могут перемещаться между узлами. Узлы и компоненты предоставляют [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) функции для выполнения этой задачи без необходимости проходить через родительский. После удаления узла, после вызова этой функции могут использоваться операции, не на один или несколько компонентов в вопросе.

Можно также создать `Node` , не принадлежит сцены. Это полезно, например с помощью камеры, перемещение в сцене, который может загружать и сохранять, поскольку затем камеры не будут сохранены, а также фактические сцены и не будет уничтожен, когда загружается сцены. Тем не менее Обратите внимание, что создание компонентов geometry, физики или сценарий на неподключенные узел, а затем перемещать его сцены позже приведет эти компоненты не будут работать правильно.

### <a name="scene-updates"></a>Обновления сцены

Сцены, в которых разрешено (по умолчанию) автоматически обновляются при каждой итерации основного цикла.  Приложения [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) на нем вызывается обработчик событий.

Узлы и компоненты можно исключить из обновления сцены путем их отключения, см. в разделе [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  Поведение зависит от конкретного компонента, но например отключение drawable компонента также делает его невидимым, во время отключения звука исходный компонент выключает его. Если узел отключен, все его компоненты, обрабатываются согласно отключены вне зависимости от состояния включения/отключения.

## <a name="adding-behavior-to-your-components"></a>Добавление поведения компонентов

Структурировать свою игру рекомендуется создать собственный компонент, инкапсулировать субъекта или элемент на свою игру.  Это делает функцию автономные из ресурсов, используемый для отображения, чтобы его поведение.

Самый простой способ добавления поведения в компонент — действия, которые приведены инструкции по очереди и объединять, с помощью асинхронного программирования на C# можно использовать.  Это позволяет определить поведение компонент может быть очень высокий уровень и делает его проще понять, что произошло.

Кроме того можно управлять точно что происходит с компонента путем обновления свойств компонента в каждом кадре (см. в разделе поведения на основе кадров).

### <a name="actions"></a>Действия

Можно добавить поведение к узлам, очень легко с помощью действий.  Действия можно изменять различные свойства узла и выполнять их за период времени или повторять их несколько раз с кривую данной анимации.

Например, рассмотрим узел «облака» в сцену, вы можете скрывать следующим образом:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Действия являются неизменяемыми объектами, которые позволяет повторно использовать действия для управления разными объектами.

Обычно вызывается для создания действий, выполняющий операцию, обратную операции:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

Следующий пример будет скрывать объекта для вас в течение трех секунд.  Можно также запустить одно действие вслед за другим, например, можно сначала переместить облака и скрыть его:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Если требуется, чтобы оба действия должно выполняться в то же время, можно использовать параллельные действия и предоставляют все действия, которые вы хотите выполняется в параллельном режиме:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

В приведенном выше примере облака будет переместить и исчезать в то же время.

Обратите внимание, что они используется C# await, позволяет линейно думать о поведении, вы хотите получить.

### <a name="basic-actions"></a>Базовые действия

Ниже приведены действия, поддерживаемые в UrhoSharp.

* Перемещение узлов: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Поворот узлов: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* Масштабирование узлов: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* Плавный переход узлов: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Изменяется исследуемое: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Моменты: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Циклы: [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Другим дополнительным функциям относятся сочетание [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) и [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) действия.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Замедление - управления скоростью ваши действия

Для реалистичной анимации является способом, направляющее способ анимация будет разворачивать, что можно сделать гораздо более приятной анимаций.  По умолчанию действия будут иметь линейное поведение, например [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) действие будет иметь очень автоматической перемещения.  Можно включить действия в действие стирание для изменения поведения, например, будет медленно начать перемещение, ускорить и медленно достижения конца ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Это можно сделать путем заключения существующее действие в действие плавности, например:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Существует множество плавности режима, на следующей диаграмме показаны различные типы реалистичной анимации и их поведение на значение объекта, который они управления за период времени, от начала до конца.

![Режимы для реалистичной анимации](using-images/easing.png "эта диаграмма показывает различные типы реалистичной анимации и их поведение на значение объекта, они управления за период времени")

### <a name="using-actions-and-async-code"></a>Использование действий и асинхронного кода

В вашей [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) подкласс, должен вводить асинхронный метод, который подготавливает ваше поведение компонента и дисков для его функциональные возможности.
Затем можно будет вызвать этот метод, с помощью C# `await` ключевое слово из другой части программы, либо к `Application.Start` метода или в ответ на точку пользователя или истории в своем приложении.

Пример:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

В `Launch` запускаются метод выше трех действий: робота поступает от сцены, это действие будет изменять расположение узла за период длительностью 0,6 секунды.  Так как это параметр async, это будет происходить одновременно как следующую инструкцию, которая представляет собой вызов к `MoveRandomly`.  Этот метод будет изменить положение робота в параллельном режиме в произвольное расположение.  Это достигается путем выполнения двух действий комплексная, перемещения в новое расположение и вернемся к исходного размещения и повторите эти действия до тех пор, пока робота остается активным.  И чтобы усложнить задачу, робота будет хранить неполадок одновременно.  Устранении неполадок будет загружаться только каждые 0,1 секунды.

### <a name="frame-based-behavior-programming"></a>Программирование поведения на основе кадров

Если вы хотите управлять поведением компонента на основе фрейм за фреймом, а не с помощью действий, что следует делать является переопределение [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) метод вашей [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) подкласс.  Этот метод вызывается один раз за кадр и вызывается только в том случае, если свойство ReceiveSceneUpdates присвоено значение true.

Далее показано, как создать `Rotator` компонент, который затем подключается к узлу, что приводит к узел для поворота:

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

И это, как этот компонент прикрепляются к узлу:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Объединение стили

Async/действие на основе модели можно использовать для программирования, значительная часть этого поведения, что очень удобно для выстрелил и забыл стиль программирования, но можно также использовать тонкую настройку поведения компонента также выполнять определенный код обновления на каждом фрейме.

Например, в демонстрации SamplyGame используется в `Enemy` класс кодирует действий используется базовое поведение, но это также гарантирует, что компоненты указывают к пользователю, задав направление узла с `Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>Загрузки и сохранения сцены

Сцены можно загружать и сохранять в формате XML; см. в разделе функции [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) и [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). При загрузке сцены, сначала удаляется все существующее содержимое (дочерние узлы и компоненты). Узлы и компонентов, которые помечены как временные файлы с `Temporary` свойства не будут сохранены. Сериализатор обрабатывает все встроенные компоненты и свойства, но он не имеет достаточно возможностей для обработки пользовательских свойств и полей, определенных в подклассах вашего компонента. Однако он предоставляет два виртуальных метода для этого:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) где можно зарегистрировать вас пользовательские состояния для сериализации

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) где вы можете получить ваши сохраненные пользовательские состояния.

Как правило пользовательский компонент будет выглядеть следующим образом:

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>Объект Prefabs

Просто загрузки или сохранения всей сцены не является достаточной гибкостью для игр которых должны быть динамически созданы новые объекты. С другой стороны создания сложных объектов и задав их свойства в коде также будет утомительным. По этой причине можно также сохранить узел сцены, в том числе его дочерних узлов, компоненты и атрибуты. Удобно позже их можно загрузить как группу.  Сохраненный объект часто называется prefab. Это можно сделать тремя способами:

- В коде путем вызова [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) на узле
- В редакторе, выбрав узел в окне иерархии и выбрав команду «Сохранить узел как» в меню «Файл».
- С помощью команды «узел» в `AssetImporter`, которой будет сохранен узел иерархии сцены и всех моделей, содержащихся в входного ресурса-контейнера (например) файл Collada)

Чтобы создать сохраненный узел сцены, вызовите [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). Узел будет создан в качестве дочернего элемента сцены, но может быть свободно изменен порядок наследования после этого. Положения и поворота для размещения узла необходимо указать. Следующий код демонстрирует, как создать экземпляр готового блока `Ninja.xm` сцену с требуемой положения и поворота:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>События

UrhoObjects к возникновению ряда событий, они отображаются в виде событий C# в различных классах, которые создают их.  В дополнение к C#-модель на основе событий, можно также использовать `SubscribeToXXX` методы, которые можно подписаться и сохранить маркер подписки, можно позже использовать для отмены подписки.  Разница в первый позволит много вызывающих объектов, чтобы подписаться, второй только позволяет одному, но также позволяет лучше лямбда-выражение стиль подход для использования и еще, позволяют легко удаления подписки.  Они являются взаимоисключающими.

При оформлении подписки на событие, необходимо указать метод, который принимает в качестве аргумента с аргументами соответствующего события.

Например это как подписаться на событие нажатия кнопки мыши:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Со стилем лямбда-выражения:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

Иногда требуется прекратить получение уведомлений для событий, в таком случае сохраните значение из вызова `SubscribeTo` метод и вызвать метод отмены подписки на него:

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

Параметра, полученного обработчиком события является классом аргументов строго типизированного события, который будет относиться только к каждому событию, а также содержит полезные данные события.

## <a name="responding-to-user-input"></a>Реагировать на действия пользователя

Можно подписаться на различные события, например нажатий клавиш вниз подписки на событие, и реагирование на входные данные доставляются:

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Но во многих сценариях необходимо обновление сцены обработчиков для проверки текущее состояние клавиш, когда они обновляются и соответствующим образом изменить код.  Например ниже можно использовать для обновления на месте камеры, исходя из ввод с клавиатуры:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Ресурсы (активы)

Ресурсы включают большинство возможностей UrhoSharp, которые загружаются из запоминающего устройства во время инициализации или среды выполнения:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -для базовой анимации
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) — Представляет образы, хранящиеся в различных графические форматы
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Трехмерных моделей
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -материалы, предназначенные для отрисовки моделей.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [Описывает](http://urho3d.github.io/documentation/1.4/_particles.html) передатчика частиц работает, см. в разделе "[частицы](#particles)" ниже.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -пользовательскими шейдерами
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -звуковые фалы для воспроизведения, см. в разделе "[звук](#sound)" ниже.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -Методы отрисовки материала
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Двумерной текстуры
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -3D-текстуры
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -Cube текстуры
- `XmlFile`

Они управляются и загружен [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) подсистемы (доступен как [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Сами ресурсы идентифицируются по их пути к файлам относительно зарегистрированных ресурсов каталогов или файлов пакета. По умолчанию ядро регистрирует каталогов ресурсов `Data` и `CoreData`, или пакеты `Data.pak` и `CoreData.pak` если они существуют.

При сбое загрузке ресурса, в журнал, и возвращается пустая ссылка.

Следующий пример показывает типичный способ выборки ресурс из кэша ресурса.  В этом случае текстуры для элемента пользовательского интерфейса, при этом используется `ResourceCache` свойства из `Application` класса.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Ресурсы можно также создать вручную и сохранена в кэше ресурсов так, как если бы они были загружать с диска.

Можно задать бюджеты памяти каждого типа ресурсов: Если ресурсы потребляют больше памяти, чем разрешено, самая старая ресурсы будут удалены из кэша, если не используется больше. По умолчанию бюджеты памяти присваивается неограниченное количество.

### <a name="bringing-3d-models-and-images"></a>3D-моделей и образов

Urho3D пытается использовать существующие форматы файлов, когда это возможно, а также определить пользовательские форматы файлов только в случае крайней необходимости, например, для моделей (*.mdl) и для анимации (*.ani). Для этих типов ресурсов, Urho предоставляет преобразователь - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) которых требует много популярных трехмерных форматов, например fbx, dae, 3ds и obj и т. д.

Имеется также удобно надстройка для Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) , можно экспортировать в формате, который подходит для Urho3D Blender ресурсов.

### <a name="background-loading-of-resources"></a>Фоновая загрузка ресурсов

Как правило при запросе ресурсов, с помощью одного из `ResourceCache`в `Get` метод, они загружаются непосредственно в основном потоке, который может занять несколько миллисекунд для все необходимые этапы (загрузить файл с диска, синтаксический анализ данных, передача GPU, при необходимости ) и может привести падения частоты кадров.

Если вы знаете, заранее какие ресурсы необходимо, вы можете запросить их необходимо загрузить в фоновом потоке, вызвав `BackgroundLoadResource()`. Можно подписаться на событие ресурс фон загружен с помощью `SubscribeToResourceBackgroundLoaded` метод. он будет сообщать, если загрузка фактически было успешным или неудачным. В зависимости от ресурсов, только часть процесса загрузки, перемещаются в фоновом потоке, например завершения шаг отправки GPU всегда должно происходить в основном потоке. Обратите внимание, что если при вызове одного из методов для ресурса, который помещается в очередь для фоновой загрузки загрузки ресурса, основной поток будет простаивать до завершения загрузки.

Асинхронные сцены, функциональность загрузки `LoadAsync()` и `LoadAsyncXML()` способна фоновой нагрузки ресурсы сначала прежде чем приступить к загрузить содержимое сцены. Он также позволяют загружать ресурсы без изменения в сцену, указав только `LoadMode.ResourcesOnly`. Это позволяет готовить сцены или объекта prefab файлы для быстрого создания экземпляра.

Наконец максимальное время (в миллисекундах), затраченное на каждый кадр на завершение фоновой загрузки ресурсов можно настроить, задав `FinishBackgroundResourcesMs` свойство `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Звук

Звук является важной частью игры и UrhoSharp framework предоставляет способ воспроизведение звуков в игре.  Воспроизведение звуков путем присоединения [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
компонент [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) и затем воспроизводить файл с именем из ресурсов.

Вот как это делается:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Примитивы

Примитивы обеспечит простой способ добавления некоторые эффекты, простое и дешевое приложение.  Можно использовать примитивы, которые хранятся в формате PEX, с помощью таких средств, как [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Примитивы являются компоненты, которые могут быть добавлены к узлу.  Необходимо вызвать на узле `CreateComponent<ParticleEmitter2D>` способ создать фрагмент, а затем настройте примитив, задав свойство Effect эффекта 2D загружается из кэша ресурса.

Например можно вызвать этот метод в компоненте для отображения некоторых примитивы, которые подготавливаются к просмотру как развертывание, когда он достигает:

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

Приведенный выше код создаст узел развертывания, к которой подключен текущий компонент, внутри этого узла развертывания мы создайте передатчика двумерный примитив и настройте его, задав свойство Effect.  Мы выполните два действия, один узел, который меньше по размеру масштабирует, а, оставляет ее в этот размер для 0,5 секунды.  Затем мы удаляем развертывания, которая также устраняет эффект частиц на экране.

Выше примитив визуализирует следующим образом, при использовании текстуры sphere:

![Примитивы с текстурным sphere](using-images/image-1.png "выше примитив отображает следующим образом, при использовании sphere текстуры")

И это выглядит при использовании виде текстуры.

![Примитивы с текстурным поле](using-images/image-2.png "и это выглядит при использовании виде текстуры")

## <a name="multithreading-support"></a>Поддержка многопоточности

UrhoSharp — отдельная библиотека потоками.  Это означает, что не следует пытаться вызывать методы в UrhoSharp из фонового потока, или существует риск, что приведет к повреждению состояния приложения и скорее всего сбоев приложения.

Если вы хотите выполнять определенный код в фоновом режиме, а затем обновите компоненты Urho на основной пользовательский Интерфейс, можно использовать [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
метод.  Кроме того, вы можете использовать await C# и .NET задач API-интерфейсов, чтобы убедиться, что код выполняется в нужном потоке.

## <a name="urhoeditor"></a>UrhoEditor

Вы можете скачать редакторе Urho для вашей платформы из [Urho веб-сайт](http://urho3d.github.io/), перейдите к загрузкам и выбрать последнюю версию.

## <a name="copyrights"></a>Авторские права

Эта документация содержит исходное содержимое из Xamarin Inc, но рисует широко из документации по открытым исходным кодом для проекта Urho3D и содержатся снимки экранов из проекта Cocos2D.
