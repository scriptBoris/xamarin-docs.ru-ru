---
title: 'Программа установки платформы GTK #'
description: 'Xamarin.Forms теперь имеет поддержку предварительной версии для платформы GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: 7f68b7c8affc11b50bdb4a2fc9589f8dcbfb45ec
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209194"
---
# <a name="gtk-platform-setup"></a>Программа установки платформы GTK #

![Предварительный просмотр](~/media/shared/preview.png)

Xamarin.Forms теперь имеет поддержку GTK # приложений в предварительной версии. GTK # — это графический пользовательский интерфейс набор средств, набор средств GTK + и различных библиотек GNOME ссылок позволяет разработки полностью машинным кодом GNONE графики приложения моно и .NET. В этой статье показано, как добавить проект GTK # решения Xamarin.Forms.

Прежде чем начать, создайте новое решение Xamarin.Forms или использовать существующее решение Xamarin.Forms, например, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Хотя данная статья посвящена приложения GTK # при добавлении в решение Xamarin.Forms в Visual Studio и VS2017 для Mac, его можно также выполнять в [MonoDevelop](http://www.monodevelop.com/) для Linux.

## <a name="adding-a-gtk-app"></a>Добавление приложения GTK #

GTK # для macOS и Linux устанавливается как часть [моно](http://www.mono-project.com/download/stable/). GTK # для .NET можно установить в Windows с [GTK # установщика](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Выполните эти инструкции, чтобы добавить приложение GTK #, который будет выполняться на рабочем столе Windows.

1. В Visual Studio 2017 г., щелкните правой кнопкой мыши имя решения в **обозревателе решений** и выберите **Добавить > Новый проект...** .

2. В **новый проект** окна слева выберите **Visual C#** и **классического Windows**. В списке типов проектов выберите **библиотека классов (.NET Framework)** и убедитесь, что **Framework** раскрывающийся список имеет значение менее .NET Framework 4.7.

3. Введите имя для проекта с **GTK** расширение, например **GameOfLife.GTK**. Нажмите кнопку **Обзор** кнопку, выберите папку, содержащую другие платформы проектов и нажмите клавишу **Выбор папки**. Это позволит GTK проекта в одном каталоге с остальными проектами в решении.

    ![Добавьте новый проект GTK](gtk-images/win/add-new-project.png "добавьте новый проект GTK")

    Нажмите клавишу **ОК** кнопку, чтобы создать проект.

4. В **обозревателе решений**, щелкните правой кнопкой мыши новый проект GTK и выберите **управление пакетами NuGet**. Выберите **Обзор** и найдите **Xamarin.Forms** 3.0 или более поздней.

    ![Установите пакет Xamarin.Forms NuGet](gtk-images/win/select-forms-nuget-package.png "установите пакет Xamarin.Forms NuGet")

    Выберите пакет и нажмите кнопку **установить** кнопки.

5. Теперь поиск **Xamarin.Forms.Platform.GTK** пакета 3.0 или выше.

    ![Выберите пакет Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "установите пакет Xamarin.Forms.Platform.GTK NuGet")

    Выберите пакет и нажмите кнопку **установить** кнопки.

6. В **обозревателе решений**, щелкните правой кнопкой мыши имя решения и выберите **управление пакетами NuGet для решения**. Выберите **обновление** вкладку и **Xamarin.Forms** пакета. Выбрать все проекты и их обновления до версии Xamarin.Forms, используемый в проекте GTK.

7. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** в проекте GTK. В **диспетчер ссылок** диалогового окна выберите **проекты** в слева, а затем установите флажок рядом с проекта .NET Standard или общем:

    ![Ссылки на общий проект](gtk-images/win/reference-shared-project.png "ссылается на общий проект")

8. В **диспетчер ссылок** диалоговое окно, нажмите клавишу **Обзор** кнопку и перейдите к папке **C:\Program Files (x86)\GtkSharp\2.12\lib** папку и выберите  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **gtk sharp.dll** файлов.

    ![Ссылаться на библиотеки GTK #](gtk-images/win/reference-gtk-libraries.png "ссылаться на библиотеки GTK #")

    Нажмите клавишу **ОК** кнопку, чтобы добавить ссылки.

9. В проекте GTK переименование **Class1.cs** для **Program.cs**.

10. В проекте GTK изменить **Program.cs** файла, как показано в следующем коде:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Этот код инициализирует GTK # и Xamarin.Forms, окно приложения создает и запускает приложение.

11. В **обозревателе решений**, щелкните правой кнопкой мыши проект GTK и выберите **свойства**.

12. В **свойства** выберите **приложения** вкладку и измените **тип вывода** раскрывающийся список для **приложение Windows**.

    ![Измените тип выходного файла проекта](gtk-images/win/change-project-output-type.png "измените тип выходного файла проекта")

13. В **обозревателе решений**, щелкните правой кнопкой мыши проект WPF и выберите **Назначить запускаемым проектом**. Нажмите клавишу F5, чтобы запустить программу с помощью отладчика Visual Studio на рабочем столе Windows:

    ![Игра GTK # жизни](gtk-images/win/gtk-gameoflife.png "GTK # игры жизненного цикла")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Выполните следующие действия, чтобы добавить приложение GTK #, которое будет запускаться на рабочем столе Mac:

1. В Visual Studio для Mac, щелкните правой кнопкой мыши решение Xamarin.Forms и выберите **Добавить > Добавить новый проект...** .

2. В **новый проект** окна выберите **других > .NET > проект 2.0 Gtk #** и нажмите клавишу **Далее**.

3. Введите имя для проекта с **GTK** расширение, например **GameOfLife.GTK**и нажмите клавишу **создать**.

4. В **Pad решения**, щелкните правой кнопкой мыши **пакеты > Добавление пакетов...**  для GTK проекта и добавьте пакет NuGet предварительной версии Xamarin.Forms 3.0 или выше.

    ![Установите пакет Xamarin.Forms NuGet](gtk-images/mac/select-forms-nuget-package.png "установите пакет Xamarin.Forms NuGet")

5. В **Pad решения**, щелкните правой кнопкой мыши **пакеты > Добавление пакетов...**  для GTK проекта и добавьте пакет NuGet предварительного выпуска Xamarin.Forms.Platform.GTK 3.0 или выше.

    ![Выберите пакет Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "установите пакет Xamarin.Forms.Platform.GTK NuGet")

6. Обновление других проектов платформы для использования одной и той же версии Xamarin.Forms, используемый в проекте GTK.

7. В **Pad решения**, щелкните правой кнопкой мыши **References > Изменить ссылки...**  для GTK проекта и добавьте ссылку на проект Xamarin.Forms (.NET Standard или общий проект).

    ![Ссылки на общий проект](gtk-images/mac/reference-shared-project.png "ссылается на общий проект")

8. Изменить **Program.cs** файла проекта GTK, так что он похожа на следующий код:

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Этот код инициализирует GTK # и Xamarin.Forms, окно приложения создает и запускает приложение.

9. В **Pad решения**, щелкните правой кнопкой мыши проект GTK и выберите **Назначить запускаемым проектом**.

10. В Visual Studio для Mac панели инструментов, нажмите клавишу **запустить** (кнопка треугольное похожа на кнопку «Воспроизведение») для запуска приложения.

    ![Игра GTK # жизни](gtk-images/mac/gtk-gameoflife.png "GTK # игры жизненного цикла")

-----

## <a name="next-steps"></a>Следующие шаги

### <a name="platform-specifics"></a>Особенности платформы

Можно определить, какие платформе, Xamarin.Forms приложения XAML или кода. Это позволяет изменить характеристики программы, выполняющейся на GTK #. В коде, сравните значение `Device.RuntimePlatform` с `Device.GTK` константа (что соответствует строка «GTK»). Если соответствие, приложение выполняется на GTK #.

В XAML, можно использовать `OnPlatform` тег установите значение свойства, относящееся к платформе:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Значок приложения

Значок приложения можно задать во время запуска:

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Темы

Существуют разнообразные темы для GTK #, и они могут использоваться из приложения Xamarin.Forms:

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Собственный форм

Xamarin.Forms позволяет собственного Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-производный страниц используемого проектов машинного кода, включая проекты GTK #. Это можно сделать, создав экземпляр [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-производный страницы и преобразование ее в машинном коде GTK # типа с помощью `CreateContainer` метода расширения:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Дополнительные сведения о собственном форм см. в разделе [собственного Forms](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Проблемы

Это предварительная версия, поэтому следует ожидать, что не все из которых работает готовности. Текущее состояние реализации, см. [состояние](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)и текущие известные проблемы, см. [ожидающие & известные проблемы](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
