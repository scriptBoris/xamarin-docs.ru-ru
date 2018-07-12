---
title: Настройка платформы WPF
description: Xamarin.Forms теперь включает поддержку предварительной версии для платформы WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 416e33f131c6e1ef144608f98964fd8372f454f8
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831323"
---
# <a name="wpf-platform-setup"></a>Настройка платформы WPF

![Предварительный просмотр](~/media/shared/preview.png)

Xamarin.Forms теперь есть поддержка предварительной версии для Windows Presentation Foundation (WPF). В этой статье показано, как добавить проект WPF в решение Xamarin.Forms.

Прежде чем начать, создайте новое решение Xamarin.Forms в Visual Studio 2017 или использовать существующее решение Xamarin.Forms, например, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Только приложения WPF можно добавить в решение Xamarin.Forms в Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Добавьте в приложение Xamarin.Forms с Xamarin.University проект WPF

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF поддерживает, путем [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Добавление приложения WPF

Выполните эти инструкции, чтобы добавить это приложение WPF, которое будет выполняться на рабочих столах Windows 7, 8 и 10.

1. В Visual Studio 2017, щелкните правой кнопкой мыши имя решения на **обозревателе решений** и выберите **Добавить > Новый проект...** .

2. В **новый проект** окне слева выберите **Visual C#** и **классический рабочий стол Windows**. В списке типов проектов выберите **приложение WPF (.NET Framework)**. 

3. Введите имя для проекта с **WPF** расширение, например, **BoxViewClock.WPF**. Нажмите кнопку **Обзор** кнопку, выберите **BoxViewClock** папки и клавишу **Выбор папки**. Это позволит проект WPF в том же каталоге, что другие проекты в решении.

    ![Добавьте новый проект WPF](wpf-images/add-new-project.png "добавьте новый проект WPF")

    Нажмите кнопку ОК, чтобы создать проект.

4. В **обозревателе решений**, щелкните правой кнопкой мыши новый **BoxViewClock.WPF** проекта и выберите **управление пакетами NuGet**. Выберите **Обзор** щелкните **включить предварительные выпуски** флажок и выполните поиск **Xamarin.Forms**.

    ![Выберите пакет NuGet](wpf-images/select-nuget-package.png "выберите пакет NuGet")

    Укажите, что пакет и нажмите кнопку **установить** кнопки.

5. Теперь поиск **Xamarin.Forms.Platform.WPF** упаковки и также установить один. Убедитесь, что пакет находится от корпорации Майкрософт!

6. Щелкните правой кнопкой мыши имя решения в **обозревателе решений** и выберите **управление пакетами NuGet для решения**. Выберите **обновление** вкладку и **Xamarin.Forms** пакета. Выберите все проекты и обновите их до одной и той же версии Xamarin.Forms:

    ![Обновите пакет NuGet](wpf-images/update-nuget-package.png "обновите пакет NuGet") 

7. В проекте WPF, щелкните правой кнопкой мыши **ссылки**. В **диспетчер ссылок** диалоговом окне выберите **проекты** в слева и установите флажок рядом с **BoxViewClock** проекта:

    ![Ссылки на общий проект](wpf-images/reference-shared-project.png "ссылку на общий проект")

8. Изменить **MainWindow.xaml** файл проекта WPF. В `Window` , добавьте объявление пространства имен XML для **Xamarin.Forms.Platform.WPF** сборки и пространства имен:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Теперь измените `Window` тег `wpf:FormsApplicationPage`. Изменение `Title` на имя приложения, например, **BoxViewClock**. Законченный файл XAML должен выглядеть следующим образом:

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Изменить **MainWindow.xaml.cs** файл проекта WPF. Добавьте два новых `using` директивы:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Измените базовый класс для `MainWindow` из `Window` для `FormsApplicationPage`. Следуя `InitializeComponent` вызова, добавьте следующие две инструкции:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    За исключением комментариев и неиспользуемые `using` директивы, полный **файл MainWindows.xaml.cs** файл должен выглядеть следующим образом:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

10. Щелкните правой кнопкой мыши проект WPF в **обозревателе решений** и выберите **Назначить запускаемым проектом**. Нажмите клавишу F5 для запуска программы с помощью отладчика Visual Studio на рабочем столе Windows:

    ![Часов WPF BoxView](wpf-images/wpf-boxviewclock.png "часов BoxView WPF" )

## <a name="next-steps"></a>Следующие шаги

### <a name="platform-specifics"></a>Особенности платформы

Можно определить, какие платформы, приложение Xamarin.Forms выполняется из кода или XAML. Это позволяет изменить характеристики программы, если он выполняется на основе WPF. В коде, сравните значение `Device.RuntimePlatform` с `Device.WPF` константа (это строка «WPF»). Если есть совпадение, приложение выполняется на основе WPF.

В XAML, можно использовать `OnPlatform` тег, чтобы выбрать значение свойства, относящееся к платформе:

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="window-size"></a>Размер окна

Можно настроить начальный размер окна в WPF **MainWindow.xaml** файла:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Проблемы

Это предварительная версия, поэтому следует ожидать, что не все, что является готовы для рабочей среды. Не все пакеты NuGet для Xamarin.Forms все готово для WPF, а некоторые функции работают полностью.

