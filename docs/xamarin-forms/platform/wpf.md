---
title: Программа установки платформы WPF
description: Xamarin.Forms теперь имеет поддержку предварительной версии платформа WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 04/05/2018
ms.openlocfilehash: 416e33f131c6e1ef144608f98964fd8372f454f8
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33799351"
---
# <a name="wpf-platform-setup"></a>Программа установки платформы WPF

![Предварительный просмотр](~/media/shared/preview.png)

Xamarin.Forms теперь имеет поддержку предварительной версии для Windows Presentation Foundation (WPF). В этой статье показано, как добавить проект WPF в решение Xamarin.Forms.

Прежде чем начать, создайте новое решение Xamarin.Forms в Visual Studio 2017 г. или использовать существующее решение Xamarin.Forms, например, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/). Приложения WPF можно добавлять только для решения xamarin.Forms создается в Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Добавить проект WPF в приложении Xamarin.Forms с помощью Xamarin.University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF поддерживает, путем [университета Xamarin](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>Добавление приложения WPF

Выполните эти инструкции, чтобы добавить приложение WPF, который будет выполняться на компьютерах Windows 7, 8 и 10.

1. В Visual Studio 2017 г., щелкните правой кнопкой мыши имя решения в **обозревателе решений** и выберите **Добавить > Новый проект...** .

2. В **новый проект** окна слева выберите **Visual C#** и **классического Windows**. В списке типов проектов выберите **приложения WPF (.NET Framework)**. 

3. Введите имя для проекта с **WPF** расширения, например, **BoxViewClock.WPF**. Нажмите кнопку **Обзор** кнопку, выберите **BoxViewClock** папку и нажмите клавишу **Выбор папки**. Это позволит проект WPF в одном каталоге с остальными проектами в решении.

    ![Добавьте новый проект WPF](wpf-images/add-new-project.png "добавьте новый проект WPF")

    Нажмите кнопку ОК, чтобы создать проект.

4. В **обозревателе решений**, щелкните правой кнопкой мыши новый **BoxViewClock.WPF** проект и выберите **управление пакетами NuGet**. Выберите **Обзор** щелкните **включить предварительный выпуск** флажок и выполните поиск **Xamarin.Forms**.

    ![Выберите пакет NuGet](wpf-images/select-nuget-package.png "установите пакет NuGet")

    Выберите нужный пакет и нажмите кнопку **установить** кнопки.

5. Теперь поиск **Xamarin.Forms.Platform.WPF** пакет и установите один также. Убедитесь, что пакет находится в Майкрософт!

6. Щелкните правой кнопкой мыши имя решения в **обозревателе решений** и выберите **управление пакетами NuGet для решения**. Выберите **обновление** вкладку и **Xamarin.Forms** пакета. Выбрать все проекты и обновите их до одной и той же версии Xamarin.Forms:

    ![Обновление пакета NuGet](wpf-images/update-nuget-package.png "обновления пакета NuGet") 

7. В проекте WPF, щелкните правой кнопкой мыши **ссылки**. В **диспетчер ссылок** диалогового окна выберите **проекты** в слева, а затем установите флажок рядом с **BoxViewClock** проекта:

    ![Ссылки на общий проект](wpf-images/reference-shared-project.png "ссылается на общий проект")

8. Изменить **MainWindow.xaml** файла проекта WPF. В `Window` , добавьте объявление пространства имен XML для **Xamarin.Forms.Platform.WPF** сборку и пространство имен:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Теперь измените `Window` тег `wpf:FormsApplicationPage`. Изменение `Title` на имя приложения, например, **BoxViewClock**. Полный файл XAML должен выглядеть следующим образом:

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

9. Изменить **MainWindow.xaml.cs** файла проекта WPF. Добавление двух новых `using` директивы:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Измените базовый класс для `MainWindow` из `Window` для `FormsApplicationPage`. После `InitializeComponent` call, добавьте две следующие инструкции:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    За исключением комментариев и неиспользуемые `using` директивы, полный **MainWindows.xaml.cs** файл должен выглядеть следующим образом:

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

10. Щелкните правой кнопкой мыши проект WPF в **обозревателе решений** и выберите **Назначить запускаемым проектом**. Нажмите клавишу F5, чтобы запустить программу с помощью отладчика Visual Studio на рабочем столе Windows:

    ![Часы WPF BoxView](wpf-images/wpf-boxviewclock.png "BoxView часов WPF" )

## <a name="next-steps"></a>Следующие шаги

### <a name="platform-specifics"></a>Особенности платформы

Можно определить, какие платформы ОС Xamarin.Forms приложения с помощью XAML или кода. Это позволяет изменить характеристики программы, выполняющейся на WPF. В коде, сравните значение `Device.RuntimePlatform` с `Device.WPF` константа (что соответствует строка «WPF»). Если соответствие, приложение выполняется на основе WPF.

В XAML, можно использовать `OnPlatform` тег установите значение свойства, относящееся к платформе:

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

Начальный размер окна в WPF можно настроить **MainWindow.xaml** файла:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Проблемы

Это предварительная версия, поэтому следует ожидать, что не все из которых работает готовности. Не все пакеты NuGet Xamarin.Forms готовы для WPF, и некоторые возможности могут работать не полностью.

