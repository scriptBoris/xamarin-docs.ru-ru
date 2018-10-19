---
title: Общие сведения о UrhoSharp
description: В этом документе описывается базовая структура UrhoSharp приложения и ссылки на различные руководства и примеры приложений, демонстрирующие способы использования UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a3e14ebca961e828fc578035adaca5ba2a809438
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "34783559"
---
# <a name="an-introduction-to-urhosharp"></a>Общие сведения о UrhoSharp

![Логотип UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp — это мощный механизм игры 3D, для разработчиков Xamarin и .NET.  Он аналогичен дух SceneKit Apple и SpriteKit и включают физики, навигации, сети и многое более хотя, по-прежнему различных платформах.

Привязка .NET для [Urho3D](http://urho3d.github.io/) ядра и позволяет разработчикам писать код кроссплатформенный, которое может ориентироваться на Android, iOS, Windows и Mac с тем же базы кода и может привести к системам OpenGL и Direct3D.

UrhoSharp является игровое ядро с большим количеством готовые функциональные возможности:

- Мощные средства отображения трехмерной графики
- [Физических моделирования](https://developer.xamarin.com/api/namespace/Urho.Physics/) (с помощью библиотеки маркер)
- [Обработка сцены](https://developer.xamarin.com/api/type/Urho.Scene/)
- Поддержка await/Async
- [Понятное действия API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Интеграция 2D в 3D-формата](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [В отрисовке шрифта с FreeType](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Клиент и сервер сетевых возможностях](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Импорт широкий спектр средств](https://developer.xamarin.com/api/namespace/Urho.Resources/) (с открыть библиотеку ресурсов)
- [Сетка навигации и pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (с помощью свести обходы)
- [Создание выпуклой оболочки для обнаружения столкновений](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (с помощью StanHull)
- [Воспроизведение аудиозаписей](https://developer.xamarin.com/api/namespace/Urho.Audio/) (с **libvorbis**)

## <a name="getting-started"></a>Начало работы

UrhoSharp удобно распространяется в виде [пакет NuGet](https://www.nuget.org/) и его можно добавить в проекты C# и F #, предназначенных Windows, Mac, Android или iOS.  NuGet в состав как библиотеки, необходимые для запуска программы, а также основные ресурсы (CoreData), используемые обработчиком.

### <a name="urho-as-a-portable-class-library"></a>Urho как переносимой библиотеке классов

Пакет Urho могут использоваться из проекта под конкретную платформу, или из проекта переносимой библиотеки классов, что позволяет повторно использовать весь код на всех платформах.  Это означает, что все, что нужно сделать на каждой платформе для записи вашей платформы конкретной точкой входа, а затем передать управление к общему коду игр.

### <a name="samples"></a>Примеры

Чтобы получить представление о возможностях Urho можно получить, открыв в Visual Studio для Mac или Visual Studio пример решения на:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

По умолчанию это решение содержит проекты для Android, iOS, Windows и Mac.  Мы предусмотрели этого решения, чтобы у нас есть средство запуска определенной tiny платформы, а все примеры кода и кода игры живет в переносимой библиотеке классов, иллюстрирующий, как максимально увеличить повторное использование кода на всех платформах.

Обратитесь к [Urho и Your платформы](~/graphics-games/urhosharp/platform/index.md) дополнительную информацию о том, как создавать собственные решения.

Поскольку все образцы совместно используют общий набор элементов пользовательского интерфейса, образцы абстрагировании базовую настройку в этом файле:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Это предоставляет пример базового класса, который обрабатывает некоторые основные нажатия клавиш и сенсорным событием, настройки веб-камеры, предоставляет основные элементы интерфейса и это позволяет сосредоточиться на определенных функций, который является релизах каждый пример.

В следующем примере показано, что ядро возможностями:

- [Samply игр](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) простой клон ShootySkies.

Хотя в других образцах показано отдельные свойства каждого образца.

## <a name="basic-structure"></a>Основная структура

Подкласс следует игры [`Application`](https://developer.xamarin.com/api/type/Urho.Application/)
класс, это где помогут вам настроить игры (на [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) метод) и начните создавать свою игру (в [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) метод).  Затем можно создать основной пользовательский интерфейс.  Мы собираемся рассмотрим небольшой пример, показывающий, API-интерфейсы для настройки трехмерной сцены, некоторые элементы пользовательского интерфейса и присоединение к нему простое поведение.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Если вы запустите это приложение, вы быстро поймете, что среда выполнения жалуется ресурсов не существует.  Вам нужно будет создать иерархию в проекте, который начинается с имени специальные папки «Данные», а внутри этого, следует поместить ресурсы, на которые есть ссылки в программе.  Затем необходимо задать в свойствах элемента для каждого ресурса «Копировать в выходной каталог» до «Копирования Если новой версии», который будет убедитесь, что данных существует.

Давайте рассмотрим происходящего здесь.

Для запуска приложения вызовите функцию инициализации ядра, а затем, создавая новый экземпляр класса приложения, следующим образом:

```csharp
new MySample().Run();
```

Среда выполнения вызовет `Setup` и `Start` методы для вас.  При переопределении `Setup` можно настроить параметры ядра (не показано в этом образце).

Необходимо переопределить `Start` как это приведет к запуску игры.  В этом методе будет загрузить ресурсы, подключать обработчики событий, Настройка в сцену и запуск действий, которые необходимы.  В нашем примере нам нужно создать небольшой фрагмент пользовательский Интерфейс для отображения для пользователя, а также настройка трехмерной сцены.

Следующий фрагмент кода использует инфраструктуры пользовательского интерфейса для создания текстового элемента и его добавления в приложение:

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

Инфраструктура пользовательского интерфейса существует для обеспечения интерфейса пользователя очень простой игры, и он работает путем добавления новых узлов к [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/) узла.

Вторая часть настроек наш пример основного сцены.  Это включает в себя несколько этапов, Создание трехмерной сцены, Создание трехмерной поля на экране добавления источника света, камеры и окно просмотра.  Они рассматриваются более подробно в разделе [сцены, узлов, компоненты и камер](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

В третьей части нашего примера запускает ряд действий.  Действия являются рецепты, которые описывают тот или иной эффект и после создания они могут быть выполнены в виде узла по требованию, вызвав [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) метод `Node`.

Первое действие масштабирует эффект отскока в поле, и второй поворачивает поле навсегда:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Выше показано, как первое действие, которое мы создаем [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) действие, это просто рецепт, указывающее, что необходимо масштабировать на секунду на единицу масштабирования свойства узла.  Это действие в свою очередь служит оболочкой для реалистичной анимации действие, [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) действие.  Плавности действия искажение линейной выполнения действия и применить эффект, таким образом обеспечивается прыгающий горизонтального эффект.
Поэтому рецепте можно записать так:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

После создания рецепт, мы выполним рецепт:

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await указывает, что может потребоваться возобновить выполнение после этой строки, когда операция завершается.  После выполнения действия из вызова второй анимации.

[UrhoSharp с помощью](~/graphics-games/urhosharp/using.md) документе рассматриваются более подробно принципы Urho и как структурировать код для создания игр.

## <a name="copyrights"></a>Авторские права

Эта документация содержит исходное содержимое из Xamarin Inc, но рисует широко из документации по открытым исходным кодом для проекта Urho3D и содержатся снимки экранов из проекта Cocos2D.

