---
title: Введение в OpenTK в Xamarin.Mac
description: Статья содержит общие сведения о работе с OpenTK в приложении Xamarin.Mac. Он охватывает Создание и поддержание окна игры, Подготовка к просмотру простого объекта и отображения объекта для пользователя.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107720"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Введение в OpenTK в Xamarin.Mac

OpenTK (The Open Toolkit) — сложных, низкоуровневых C# библиотека, которая упрощает работу с OpenGL и OpenCL OpenAL. OpenTK может использоваться для игр, научных приложений или других проектов, требующих трехмерной графикой, аудио- или вычислительные функции. В этой статье дается краткое введение в OpenTK приложения Xamarin.Mac.

[![](opentk-images/intro01.png "Запустите пример приложения")](opentk-images/intro01.png#lightbox)

В этой статье мы обсудим основные принципы OpenTK в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>О OpenTK

Как уже говорилось, OpenTK (The Open Toolkit) — сложных, низкоуровневых C# библиотека, которая упрощает работу с OpenGL и OpenCL OpenAL. С помощью OpenTK в приложении Xamarin.Mac предоставляет следующие возможности:

- **Быстрая разработка** -OpenTK предоставляет надежный типы данных и встроенная документация улучшить рабочий процесс кодирования и перехватывать ошибки, проще и быстрее.
- **Простая интеграция** -OpenTK была разработана для простой интеграции с приложениями .NET.
- **Нестрогую лицензию** -OpenTK распространяется по лицензии MIT/X11 и совершенно бесплатно.
- **Широкие возможности, нарушающим типы привязки** -OpenTK поддерживает последние версии OpenGL, OpenGL | ES, OpenAL и OpenCL загрузка автоматическое расширение, ошибки проверки и встроенной документации.
- **Гибкие возможности графического пользовательского интерфейса** -OpenTK предоставляет собственный, окна игры высокой производительности предназначен специально для игр и Xamarin.Mac.
- **Полностью управляемая, CLS-совместимого кода** -OpenTK поддерживает 32-разрядных и 64-разрядные версии macOS без неуправляемых библиотек.
- **3D Math Toolkit** предоставляет OpenTK `Vector`, `Matrix`, `Quaternion` и `Bezier` структуры через его 3D Math Toolkit.

OpenTK может использоваться для игр, научных приложений или других проектов, требующих трехмерной графикой, аудио- или вычислительные функции.

Дополнительные сведения см. в разделе [The Open Toolkit](http://www.opentk.com) веб-сайта.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Краткое руководство OpenTK

Как краткое введение в OpenTK приложения Xamarin.Mac мы собираемся создать простое приложение, которое открывает представление игры, отображает простой треугольник в представлении и attachs представление игры для главного окна приложения Mac для отображения треугольник для пользователя.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Создании нового проекта

Запустите Visual Studio для Mac и создать новое решение Xamarin.Mac. Выберите **Mac** > **приложения** > **Общие** > **приложение Cocoa**:

[![](opentk-images/sample01.png "Добавление нового приложения Cocoa")](opentk-images/sample01.png#lightbox)

Введите `MacOpenTK` для **имя проекта**:

[![](opentk-images/sample02.png "Задание имени проекта")](opentk-images/sample02.png#lightbox)

Нажмите кнопку **создать** кнопка для создания нового проекта.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Включая OpenTK

Прежде чем открыть TK можно использовать в приложении Xamarin.Mac, необходимо включить ссылку на сборку OpenTK. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** папку и выберите **изменить ссылки...** .

Установите флажок, `OpenTK` и нажмите кнопку **ОК** кнопки:

[![](opentk-images/sample03.png "Изменение ссылок проекта")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>С помощью OpenTK

Создания нового проекта, дважды щелкните `MainWindow.cs` файл **обозревателе решений** чтобы открыть его для редактирования. Сделать `MainWindow` внешний класс следующим образом:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

Давайте подробнее рассмотрим этот код ниже подробно.

<a name="Required_APIs" />

### <a name="required-apis"></a>Требуемых API

Для использования в классе Xamarin.Mac OpenTK требуются несколько ссылок. В начале определения мы включили следующие `using` инструкции:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
Это минимальный набор потребуется для любого класса, с помощью OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Добавление представления игр

Далее нам нужно создать представление игры, чтобы содержать все наши взаимодействия с OpenTK и отображает результаты. Мы использовали следующий код:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Здесь мы внесли в представлении игры совпадает с размером окна Mac Main и заменяется новой представление содержимого окна `MonoMacGameView`. Так как мы заменили существующее содержимое окна, нашего представления дал будет изменяться автоматически при изменении размера Windows Main.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Реагирование на события

Существует несколько событий по умолчанию, которые должны реагировать на каждое представление игры. В этом разделе будут рассмотрены основные необходимые события.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>Событие загрузки

`Load` Событий — место для загрузки ресурсов из диска, например изображения, текстуры или музыки. Для нашей простой, тестовое приложение, мы не используем `Load` событий, но включили его для справки:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>Событие изменения размера

`Resize` Событий должен вызываться каждый раз изменяется представление игры. Для нашего приложения-примера мы внесли GL окно просмотра совпадает с размером нашего представления игры (который является быть автоматического изменения размера главного окна Mac) следующим кодом:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>Событие UpdateFrame

`UpdateFrame` Событие используется для обработки введенных пользователем данных, объект позиций, выполнения физики или вычисления искусственного Интеллекта. Для нашей простой, тестовое приложение, мы не используем `UpdateFrame` событий, но включили его для справки: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> Не поддерживает реализацию Xamarin.Mac OpenTK `Input API`, поэтому необходимо будет использовать Apple при условии поддержки API-интерфейсы для добавления клавиатуры и мыши. При необходимости можно создать настраиваемый экземпляр `MonoMacGameView` и переопределить `KeyDown` и `KeyUp` методы.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>Событие RenderFrame

`RenderFrame` Событие содержит код, который используется для отрисовки (рисование) графики. Для нашего примера приложения мы заполняют представление игры с простой треугольник: 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

Обычно код render будет выполняется с помощью вызова `GL.Clear` для удаления всех существующих элементов перед рисовании новых элементов.

> [!IMPORTANT]
> Для версии Xamarin.Mac OpenTK **не** вызвать `SwapBuffers` метод вашей `MonoMacGameView` экземпляра в конце кода отрисовки. Это приведет к представлении игры, чтобы strobe быстро, вместо отображения вашей отображаемого представления.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Представление игр

Все необходимые события определение и представление игры присоединенный к окну Mac Main нашего приложения, мы доступны для чтения для запуска в представлении игры и отображения наших графики. Используйте следующий код:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Мы передаем нужную частоту кадров, для которых нужно в представление игры, чтобы обновить в, в нашем примере мы выбрали `60` кадров в секунду (же частота как обычный ТВ).

Давайте запустите наше приложение и просмотреть выходные данные:

[![](opentk-images/intro01.png "Пример выходных данных приложения")](opentk-images/intro01.png#lightbox)

Если мы изменяем размер нашего окна, игра представление также будет находятся и треугольник будет при изменении размера и также обновление в режиме реального времени.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Где Далее?

С основами работы с OpenTk в приложении Xamarin.mac сделать Вот несколько советов о том, что на пробное использование следующего:

- Попробуйте изменить цвет треугольник и цвет фона представления игры в `Load` и `RenderFrame` события.
- Сделать изменяет цвет, когда пользователю нажать любую клавишу в треугольник `UpdateFrame` и `RenderFrame` события или создать собственный пользовательский `MonoMacGameView` класса и переопределить `KeyUp` и `KeyDown` методы.
- Сделать переместиться через экран, с использованием ключей, учитывать в треугольник `UpdateFrame` событий. Подсказка: использовать `Matrix4.CreateTranslation` метод для создания матрицы преобразования и вызов `GL.LoadMatrix` метод, чтобы загрузить его в `RenderFrame` событий.
- Используйте `for` цикл для подготовки к просмотру несколько треугольников в `RenderFrame` событий.
- Поворот камеры, чтобы предоставить другое представление треугольник в трехмерном пространстве. Подсказка: использовать `Matrix4.CreateTranslation` метод для создания матрицы преобразования и вызов `GL.LoadMatrix` метод для загрузки в нее. Можно также использовать `Vector2`, `Vector3`, `Vector4` и `Matrix4` классы для манипуляции камеры.

Дополнительные примеры см. в разделе [OpenTK примеров GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) репозитория. Он содержит список официальные примеры использования OpenTK. Необходимо будет адаптировать эти примеры использования с версией OpenTK Xamarin.Mac.

Более сложный пример Xamarin.Mac OpenTK реализации, см. в разделе наших [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) образца.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие краткий обзор работы с OpenTK в приложении Xamarin.Mac. Мы узнали, как создать окно игры, как подключить окна игры в окно Mac и Подготовка к просмотру простых фигур в окне игры.

## <a name="related-links"></a>Связанные ссылки

- [MacOpenTK (пример)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (пример)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Работа с Windows](~/mac/user-interface/window.md)
- [Откройте набор средств](http://www.opentk.com)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
