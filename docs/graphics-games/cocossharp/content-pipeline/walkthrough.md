---
title: С помощью средства MonoGame конвейера
description: Средство MonoGame конвейер используется для создания и управления проектами содержимого MonoGame. Файлы проектов обрабатываются с помощью средства конвейера MonoGame и выводятся в виде файлов .xnb для использования в приложениях CocosSharp и MonoGame.
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 84f825c19aa1ac08fbcf3e732077304218768bcc
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983605"
---
# <a name="using-the-monogame-pipeline-tool"></a>С помощью средства MonoGame конвейера

_Средство MonoGame конвейер используется для создания и управления проектами содержимого MonoGame. Файлы проектов обрабатываются с помощью средства конвейера MonoGame и выводятся в виде файлов .xnb для использования в приложениях CocosSharp и MonoGame._

Средства конвейера MonoGame предоставляет простой в использовании среду для преобразования содержимого файлов в **.xnb** файлов для использования в приложениях CocosSharp и MonoGame. Сведения о конвейерах содержимого и почему они полезны при разработке игр, см. в разделе [это общие сведения о конвейерах содержимого](~/graphics-games/cocossharp/content-pipeline/introduction.md)

В этом пошаговом руководстве рассматривается следующее:

 - Установка средства MonoGame конвейера
 - Создание проекта CocosSharp
 - Создание проекта
 - Обработка файлов в средство конвейера MonoGame
 - С помощью файлов во время выполнения

В этом пошаговом руководстве используется проект CocosSharp для демонстрации как **.xnb** файлы можно загружать и использовать в приложении. Из MonoGame также можно будет сослаться в этом пошаговом руководстве, как CocosSharp, так и с помощью MonoGame используйте тот же **.xnb** файлы содержимого.

Готовое приложение будет отображаться один спрайт отображение текстуры из **.xnb** файл и одну метку отображения шрифта спрайт из **.xnb** файла:

![](walkthrough-images/image1.png "Готовое приложение будет отображаться один спрайт отображение текстуры из файла .xnb")


## <a name="monogame-pipeline-tool-discussion"></a>Обсуждение средства конвейера MonoGame

В Windows, OS X и Linux доступно средство конвейера MonoGame. В этом пошаговом руководстве будет запустить средство на Windows, но он может быть выполнили все описанные действия на Mac и Linux также. Сведения о том, как средство на Max или Linux, см. в разделе [эту страницу](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

Возможность создания содержимого для приложений iOS даже при запуске в Windows, поэтому разработчики, использующие средства конвейера MonoGame [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) будут иметь возможность продолжить разработку в Windows.


## <a name="installing-the-monogame-pipeline-tool"></a>Установка средства MonoGame конвейера

Мы начнем, установив MonoGame, включая конвейер содержимого MonoGame. Обратите внимание, что конвейер содержимого MonoGame отдельный загружаемый компонент для Mac. Все установщики MonoGame можно найти на [страницы загрузок для MonoGame](http://www.monogame.net/downloads/). Мы будем скачать MonoGame для Visual Studio, но после установки разработчик может использовать MonoGame в Visual Studio для Mac слишком:

![](walkthrough-images/image2.png "Скачайте MonoGame для Visual Studio, но после установки использования разработчиками MonoGame в Visual Studio для Mac слишком")

После загрузки, мы через установщик и примите значения по умолчанию. По завершении работы установщика средства MonoGame конвейера устанавливается и можно найти в меню Start:

![](walkthrough-images/image3.png "По завершении работы установщика средства MonoGame конвейера устанавливается и можно найти в меню Start")

Запустите средство MonoGame конвейера:

![](walkthrough-images/image4.png "Запустить средство MonoGame конвейера")

После запуска средства MonoGame конвейера, мы сможем сделать наши проекты игр и содержимого.


## <a name="creating-an-empty-cocossharp-project"></a>Создание пустого проекта CocosSharp

Следующим шагом является создание проекта CocosSharp. Очень важно, что мы CocosSharp сначала создать проект, чтобы наши содержимого проекта можно сохранить в структуре папок, созданную проектом CocosSharp. Сведения о структуре проекта CocosSharp, взгляните на [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), который будет использоваться в этом руководстве. Тем не менее если у вас есть существующий проект CocosSharp, который вы хотите добавить содержимое, вы можете использовать этот проект вместо BouncingGame.

После создания проекта, запустим его, чтобы проверить, что при этом создается, и что у нас есть все, что настроен правильно:

![](walkthrough-images/image5.png "После создания проекта, запустите его, чтобы проверить, что сборка выполняется и что все, что настроен правильно")


## <a name="creating-a-content-project"></a>Создание проекта

Теперь, когда у нас есть игровой проект, мы создадим проект MonoGame конвейера. Для этого выберите средства конвейера MonoGame **файл > создать...**  и перейдите в папку содержимого проекта. Для Android, папка находится в **[проекта root]\BouncingGame.Android\Assets\Content\\**. Для iOS, папка находится в **[проекта root]\BouncingGame.iOS\Content\\**.

Изменение **имя файла** для **ContentProject** и нажмите кнопку **Сохранить** кнопки:

![](walkthrough-images/image8.png "Измените имя файла на ContentProject и нажмите кнопку \"Сохранить\"")

После создания проекта, средства MonoGame конвейера отобразятся сведения о проекте при корневой **ContentProject** выбран элемент:

![](walkthrough-images/image9.png "После создания проекта MonoGame конвейера будет выводиться сведения о проекте при выборе ContentProject корневой элемент")

Давайте рассмотрим некоторые из наиболее важных параметров для содержимого проекта.


### <a name="output-folder"></a>Выходная папка

Это папка (относительно самого содержимого проекта), где выходные данные **.xnb** файлы будут сохранены. Для простоты мы будем использовать ту же папку для хранения наших входные и выходные файлы. Другими словами мы изменим **выходную папку** быть **.\\**  :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Platform

Определяет целевую платформу для содержимого. Обратите внимание, что это **Windows** по умолчанию, поэтому нам понадобятся для нашей целевой платформы, который является **Android** (или iOS, если вы создали вместе с проектом iOS).

![](walkthrough-images/image11.png "Обратите внимание на то, что это Windows по умолчанию, поэтому измените это на целевую платформу, равное Android или iOS, если вместе в проект iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>Обработка файлов в средство конвейера MonoGame

Далее мы добавим содержимое к нашей **ContentProject**. Для этого проекта мы будем добавлять файлы в корень проекта, но крупных проектов обычно будет организовать их содержимое в папках.

Мы добавим два файла в свой проект:

 - Объект **.png** файл, который будет использоваться для рисования спрайт. Этот файл можно [загрузить здесь](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - Объект **.spritefont** файл, который будет использоваться для рисования текста на экране. Средство конвейера содержимого поддерживает создание новых файлов .spritefont, поэтому нет файла для скачивания.


### <a name="adding-a-png-file"></a>Добавление PNG-файл

Чтобы добавить **.png** файл к проекту, мы будем сначала скопируйте его в тот же каталог, как проект конвейера, который **.mgcb** расширения.

![](walkthrough-images/image12.png "Добавьте PNG-файл в проект")

Далее мы добавим файл в проект конвейера. Для этого в средстве MonoGame конвейера, выберите **изменить > Добавить элемент...** выберите **ball.png** файл и нажмите кнопку **откройте**. Файл теперь будет частью содержимого проекта и, при выборе отобразит его свойства:

![](walkthrough-images/image13.png "Файл теперь будет частью содержимого проекта и, при выборе отобразит его свойства")

Мы увидим, как изменения не требуются для загрузки файла .xnb в CocosSharp оставляя все значения в их значения по умолчанию. Можно создать файл, выбрав **сборки > построения** параметр меню, который будет начать сборку и отображает выходные данные о сборке. Мы можем убедиться, что сборка прошла без ошибок, проверив **содержимого** папку для нового **ball.xnb** файла:

![](walkthrough-images/image14.png "Убедитесь, что сборка прошла без ошибок, проверив папку содержимого для нового файла ball.xnb")


### <a name="adding-a-spritefont-file"></a>Добавление файла .spritefont

Можно создать файл .spritefont через средство конвейера MonoGame. CocosSharp требует шрифты в **шрифты** папки и шаблоны CocosSharp автоматически создавать папку шрифты автоматически. Эту папку можно добавить к средству MonoGame конвейера, выбрав **изменить > Добавить > существующую папку...** . Перейдите к **содержимого** папку и выберите **шрифты** папку и нажмите кнопку **ОК**:

![](walkthrough-images/browsetofonts.png "Перейдите к папке Content и выберите папку, шрифты и нажмите кнопку ОК")

Чтобы добавить новый файл .sprintefont, шрифты папку правой кнопкой мыши и выберите **Добавить > новый элемент...** выберите **описание SpriteFont** параметр, введите имя **arial 36**и нажмите кнопку **ОК**. CocosSharp требуется специальная именования файлов шрифтов — они должны быть в формате [FontType]-[FontSize]. Если шрифт не соответствует этот формат именования, которые он не будет загружен с CocosSharp во время выполнения.

![](walkthrough-images/image15.png "Если шрифт соответствует ли этот формат именования, которые он не будет загружен с CocosSharp во время выполнения")

.Spritefont он фактически файл XML, который можно изменять в любом текстовом редакторе, включая Visual Studio для Mac. Наиболее распространенные переменные, изменить в файле .spritefont `FontName` и `Size` свойство:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

Мы будем откройте файл в любом текстовом редакторе. Как наш **arial 36.spritefont** названия, мы оставим `FontName` как `Arial` , но изменить `Size` значение `36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>С помощью файлов во время выполнения

Файлы .xnb теперь являются встроенными и готовые к использованию в нашем проекте. Мы добавим файлы для Visual Studio для Mac, а затем мы добавим код к нашей `GameScene.cs` файл для загрузки этих файлов и их отображения.


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Добавление файлов .xnb Visual Studio для Mac

Сначала мы добавим файлы в свой проект. В Visual Studio для Mac, мы расширим **BouncingGame.Android** проекта, разверните **активы** папку, щелкните правой кнопкой мыши **содержимого** и выберите **Добавить > Добавить файлы...** Во-первых, мы выберем **ball.xnb** мы создали ранее и нажмите кнопку **откройте**. Затем повторите описанные выше действия, но добавить **arial 36.xnb** файл. Мы выберем **сохранить файл в его текущем подкаталоге** параметр, если Visual Studio для Mac предлагает способы добавления файла. После завершения работы, оба файла должны быть частью нашего проекта:

![](walkthrough-images/image20.png "После завершения работы, оба файла должны быть частью проекта")


### <a name="adding-gamescenecs"></a>Добавление **GameScene.cs**

Мы создадим класс под названием `GameScene,` оно будет содержать объекты наших спрайт и текст. Чтобы сделать это, щелкните правой кнопкой мыши **BouncingGame** (не BouncingGame.Android) проект и выберите **Добавить > новый файл...** . Выберите **Общие** категорию **пустой класс** параметр, а затем введите имя **GameScene**.

После создания, мы изменим `GameScene.cs` файл, содержащий следующий код:


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

Мы не буду рассказывать о приведенный выше код так, как работа с CocosSharp визуальных объектов, таких как CCSprite и CCLabelTtf рассматривается в [руководство по игре BouncingGame](~/graphics-games/cocossharp/bouncing-game.md).

Мы также необходимо добавить код для загрузки наших только что созданный `GameScene`. Для этого мы откроем `GameAppDelegate.cs` файл (который находится в **BouncingGame** переносимой библиотеки Классов) и измените `ApplicationDidFinishLaunching` метод, чтобы он выглядел:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

При запуске, нашей игре выглядят следующим образом:

![](walkthrough-images/image1.png "При запуске, игра будет выглядеть так")


## <a name="summary"></a>Сводка

В этом пошаговом руководстве показано, как использовать средства MonoGame конвейера для создания файлов .xnb из входного PNG-файл, а также как создать новый файл .xnb из только что созданный .sprintefont файла. Он также описано, как следует структурировать проекты CocosSharp использовать файлы .xnb и загрузки этих файлов во время выполнения.

## <a name="related-links"></a>Связанные ссылки

- [Файлы для загрузки MonoGame](http://www.monogame.net/downloads/)
- [Документация по MonoGame конвейера](http://www.monogame.net/documentation/?page=Pipeline)
- [Запуск проекта BouncingGame для Android (пример)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.PNG график (пример)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
