---
title: Настройка платформы Mac
description: В этой статье объясняется, как добавить проект Mac в проект Xamarin.Forms, которое формирует приложения может выполняться на macOS Sierra и macOS El Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: ae0fbfc7862a0d2147b2c3bbdbae7dd53dfce78f
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831693"
---
# <a name="mac-platform-setup"></a>Настройка платформы Mac

![Предварительный просмотр](~/media/shared/preview.png)

Прежде чем начать, создайте (или использовать существующую) проекта Xamarin.Forms.
Можно добавлять только приложения Mac с помощью Visual Studio для Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Путем добавления проекта macOS Xamarin.Forms, [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>Добавление приложения Mac

Выполните эти инструкции, чтобы добавить приложение Mac, который будет выполняться в macOS Sierra и macOS El Capitan.

1. В Visual Studio для Mac, щелкните правой кнопкой мыши в существующее решение Xamarin.Forms и выберите **Добавить > Добавить новый проект...**

2. В **новый проект** выберите окно **Mac > приложение > приложение Cocoa** и нажмите клавишу **Далее**.

3. Тип **имя_приложения** (и при необходимости выберите другое имя для закрепления элемента), затем нажмите клавишу **Далее**.

4. Проверьте конфигурацию и нажмите клавишу **создать**. Эти действия показаны в ниже:

  ![Анимированные инструкции, как добавлять приложения Cocoa](mac-images/add-macos-proj.gif)

5. В проекте Mac, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...**  добавление [Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet. Вы должны также обновить другие проекты до этой версии.

6. В проекте Mac, щелкните правой кнопкой мыши **ссылки** и добавьте ссылку на проект Xamarin.Forms (проект библиотеки общего проекта или .NET Standard).

  ![Добавьте ссылку в проекте с общим кодом Xamarin.Forms](mac-images/references-sml.png)

7. Обновление **Main.cs** для инициализации `AppDelegate`:

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Обновление `AppDelegate` для инициализации Xamarin.Forms, создание окна и загрузить приложение Xamarin.Forms (не забудьте задать соответствующее `Title`). _Если у вас есть другие зависимости, которые должны быть инициализированы, они здесь также доступны._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Дважды щелкните **Main.storyboard** для редактирования в Xcode. Выберите **окно** и _снимите_ **является начальной контроллера** флажок (это потому, что приведенный выше код создает окно):

  [![Снимите этот флажок, является начальной контроллера в Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

  Вы можете изменить систему меню в раскадровке, чтобы удалить ненужные элементы.

10. Наконец добавьте все локальные ресурсы (например) файлы изображений) из существующей платформы проектов, которые необходимы.

11. Проект Mac теперь должен выполнять код Xamarin.Forms в macOS!

## <a name="next-steps"></a>Следующие шаги

### <a name="styling"></a>Задание стиля

С помощью последних изменений, внесенных в `OnPlatform` можно указать любое количество платформ. Включая macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Обратите внимание на то, также может дважды на платформах следующим образом: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Размер и положение окна

Можно настроить исходный размер и расположение окна в `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Известные проблемы

Это предварительная версия, поэтому следует ожидать, что не все, что является готовы для рабочей среды. Ниже приведены несколько вещей, которые могут возникнуть при добавлении macOS в проекты.

### <a name="not-all-nugets-are-ready-for-macos"></a>Не все пакеты NuGet готовы для macOS

Пакеты должны быть предназначены «xamarinmac20» для работы в проекте на macOS. Вы можете обнаружить, что некоторые из библиотек, которые можно использовать еще не поддерживает macOS.

В этом случае необходимо отправить запрос на программы обслуживания проекта, чтобы добавить его. Не имеют поддержку, может потребоваться попробуйте найти альтернативные варианты.

### <a name="missing-xamarinforms-features"></a>Для отсутствующих компонентов Xamarin.Forms

Не все компоненты Xamarin.Forms выполнены в этой предварительной версии; Ниже приведен список некоторых функций, которые еще не реализован:

* Нижние колонтитулы
* Изображение — аспект
* UnevenRows ListView – ScrollTo, поддерживают обновление, SeparatorColor, SeparatorVisibility
* MasterDetailPage – BackgroundColor
* Навигация — InsertPageBefore
* OpenGLRenderer
* Средство выбора — реализация Bindable/Observable
* BarTextColor TabbedPage — BarBackgroundColor,
* TableView – UnevenRows
* ForceUpdateSize ViewCell — IsEnabled,
* Веб-представления — большинство WebNavigationEvents


## <a name="related-links"></a>Связанные ссылки

- [Xamarin.Mac](~/mac/index.yml)
