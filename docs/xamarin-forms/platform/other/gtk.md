---
title: 'Программа установки платформы GTK #'
description: 'Xamarin.Forms теперь имеет поддержку предварительной версии платформы GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: dcfcf6c29b0a055c59501d6d95d4f97c16475bb3
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209229"
---
# <a name="gtk-platform-setup"></a>Программа установки платформы GTK #

![Предварительный просмотр](~/media/shared/preview.png)

Xamarin.Forms теперь имеет поддержку GTK # приложений в предварительной версии. GTK # — это графический пользовательский интерфейс набор средств, связывающий toolkit GTK + и различных библиотеках GNOME, позволяя разработки полностью собственные GNOME графических приложений, с помощью Mono и .NET. В этой статье показано, как добавить в проект GTK # в решение Xamarin.Forms.

Прежде чем начать, создайте новое решение Xamarin.Forms или использовать существующее решение Xamarin.Forms, например, [ **GameOfLife**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/).

> [!NOTE]
> Хотя эта статья посвящена Добавление приложения GTK # в решение Xamarin.Forms в Visual Studio 2017 и Visual Studio для Mac, он может также выполняться в [MonoDevelop](http://www.monodevelop.com/) для Linux.

## <a name="adding-a-gtk-app"></a>Добавление приложения GTK #

GTK # для macOS и Linux устанавливается как часть [Mono](http://www.mono-project.com/download/stable/). GTK # для .NET можно установить в Windows с помощью [GTK # установщика](http://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Выполните эти инструкции, чтобы добавить приложение GTK #, которое будет запускаться на рабочем столе Windows.

1. В Visual Studio 2017, щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **Добавить > Новый проект...** .

2. В **новый проект** окне слева выберите **Visual C#** и **классический рабочий стол Windows**. В списке типов проектов выберите **библиотека классов (.NET Framework)** и убедитесь, что **Framework** раскрывающегося списка имеет значение менее .NET Framework 4.7.

3. Введите имя для проекта с **GTK** расширение, например **GameOfLife.GTK**. Нажмите кнопку **Обзор** "Кнопка", выберите папку, содержащую другие платформы проектов и нажмите клавишу **Выбор папки**. Это переключит проект GTK в тот же каталог, что другие проекты в решении.

    ![Добавьте новый проект GTK](gtk-images/win/add-new-project.png "добавьте новый проект GTK")

    Нажмите клавишу **ОК** кнопку, чтобы создать проект.

4. В **обозревателе решений**, щелкните правой кнопкой мыши новый проект GTK и выберите **управление пакетами NuGet**. Выберите **Обзор** вкладке и выполните поиск **Xamarin.Forms** 3.0 или более поздней версии.

    ![Выберите пакет Xamarin.Forms NuGet](gtk-images/win/select-forms-nuget-package.png "выберите пакет Xamarin.Forms NuGet")

    Выберите пакет и нажмите кнопку **установить** кнопки.

5. Теперь поиск **Xamarin.Forms.Platform.GTK** пакет 3.0 или более поздней версии.

    ![Выберите пакет Xamarin.Forms.Platform.GTK NuGet](gtk-images/win/select-forms-platform-nuget-package.png "выберите пакет Xamarin.Forms.Platform.GTK NuGet")

    Выберите пакет и нажмите кнопку **установить** кнопки.

6. В **обозревателе решений**, щелкните правой кнопкой мыши имя решения и выберите **управление пакетами NuGet для решения**. Выберите **обновление** вкладку и **Xamarin.Forms** пакета. Выберите все проекты и обновить их в ту же версию Xamarin.Forms, что используемый проект GTK.

7. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** в проекте GTK. В **диспетчер ссылок** диалоговом окне выберите **проекты** в слева и установите флажок возле проекта .NET Standard "и" Общий:

    ![Ссылки на общий проект](gtk-images/win/reference-shared-project.png "ссылку на общий проект")

8. В **диспетчер ссылок** диалоговое окно, нажмите клавишу **Обзор** кнопку и перейдите к **C:\Program Files (x86)\GtkSharp\2.12\lib** папку и выберите  **ATK sharp.dll**, **gdk sharp.dll**, **glade sharp.dll**, **glib sharp.dll**, **gtk-dotnet.dll**, **gtk sharp.dll** файлов.

    ![Ссылаться на библиотеки GTK #](gtk-images/win/reference-gtk-libraries.png "ссылаться на библиотеки GTK #")

    Нажмите клавишу **ОК** кнопку, чтобы добавить ссылки.

9. В проект GTK, переименуйте **Class1.cs** для **Program.cs**.

10. В проекте GTK изменить **Program.cs** файл, как показано ниже:

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

    Этот код инициализирует GTK # и Xamarin.Forms, создает окно приложения и запускает приложение.

11. В **обозревателе решений**, правой кнопкой мыши проект GTK и выберите **свойства**.

12. В **свойства** выберите **приложения** вкладку и измените **тип выходных данных** раскрывающийся список, чтобы **приложения Windows**.

    ![Изменить тип выходных данных проекта](gtk-images/win/change-project-output-type.png "изменить тип выходных данных проекта")

13. В **обозревателе решений**, щелкните правой кнопкой мыши проект GTK и выберите **Назначить запускаемым проектом**. Нажмите клавишу F5 для запуска программы с помощью отладчика Visual Studio на рабочем столе Windows:

    ![Игра GTK # жизни](gtk-images/win/gtk-gameoflife.png "GTK # игры жизненного цикла")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Выполните следующие действия, чтобы добавить приложение GTK #, которое будет запускаться на рабочем столе Mac:

1. В Visual Studio для Mac, щелкните правой кнопкой мыши решение Xamarin.Forms и выберите **Добавить > Добавить новый проект...** .

2. В **новый проект** выберите окно **других > .NET > Gtk # 2.0 проекта** и нажмите клавишу **Далее**.

3. Введите имя для проекта с **GTK** расширение, например **GameOfLife.GTK**и нажмите клавишу **создать**.

4. В **панели решения**, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...**  для GTK проекта и добавьте предварительную версию пакета NuGet Xamarin.Forms 3.0 или более поздней версии.

    ![Выберите пакет Xamarin.Forms NuGet](gtk-images/mac/select-forms-nuget-package.png "выберите пакет Xamarin.Forms NuGet")

5. В **панели решения**, щелкните правой кнопкой мыши **пакеты > Добавить пакеты...**  для GTK проекта и добавьте пакет NuGet Xamarin.Forms.Platform.GTK 3.0 предварительной версии или более поздней версии.

    ![Выберите пакет Xamarin.Forms.Platform.GTK NuGet](gtk-images/mac/select-forms-platform-nuget-package.png "выберите пакет Xamarin.Forms.Platform.GTK NuGet")

6. Обновление других проектов платформы для использования одной и той же версии Xamarin.Forms, используемого в проект GTK.

7. В **панели решения**, щелкните правой кнопкой мыши **ссылки > Изменить ссылки...**  для GTK проекта и добавьте ссылку на проект Xamarin.Forms (.NET Standard или общий проект).

    ![Ссылки на общий проект](gtk-images/mac/reference-shared-project.png "ссылку на общий проект")

8. Изменить **Program.cs** файл проекта GTK, так что он похож на следующий код:

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

    Этот код инициализирует GTK # и Xamarin.Forms, создает окно приложения и запускает приложение.

9. В **панели решения**, щелкните правой кнопкой мыши проект GTK и выберите **Назначить запускаемым проектом**.

10. В Visual Studio для Mac инструментов, нажмите клавишу **запустить** (треугольная кнопка, "Воспроизвести") для запуска приложения.

    ![Игра GTK # жизни](gtk-images/mac/gtk-gameoflife.png "GTK # игры жизненного цикла")

-----

## <a name="next-steps"></a>Следующие шаги

### <a name="platform-specifics"></a>Особенности платформы

Можно определить, какие платформы, выполняемое приложение Xamarin.Forms на с XAML или кода. Это позволяет изменить характеристики программы, когда он работает под управлением GTK #. В коде, сравните значение `Device.RuntimePlatform` с `Device.GTK` константа (это строка «GTK»). Если есть совпадение, приложение выполняется на GTK #.

В XAML, можно использовать `OnPlatform` тег, чтобы выбрать значение свойства, относящееся к платформе:

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

### <a name="native-forms"></a>Исходные формы

Исходные формы позволяет Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы мог использоваться собственные проекты, включая проекты GTK #. Это можно сделать, создав экземпляр [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страницы и преобразование ее в собственном GTK # типа с помощью `CreateContainer` метод расширения:

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Дополнительные сведения о собственном Forms, см. в разделе [исходные формы](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Проблемы

Это предварительная версия, поэтому следует ожидать, что не все, что является готовы для рабочей среды. Текущее состояние реализации, см. в разделе [состояние](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)и текущие известные проблемы, см. в разделе [ожидающие & известные проблемы](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
