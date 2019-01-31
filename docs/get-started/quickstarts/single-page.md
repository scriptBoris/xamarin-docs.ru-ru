---
title: Создание одностраничного приложения Xamarin.Forms
description: В этой статье объясняется, как создать одностраничное приложение Xamarin.Forms кросс платформенных, что дает возможность ввести примечание и сохранить их в хранилище устройства.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 7696ece64da28f05bb15866214de4a7f1103d06f
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293315"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Создание приложения Xamarin.Forms одной страницы

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

В этом кратком руководстве вы узнаете, как:

- Создание кросс платформенные приложения Xamarin.Forms.
- Определение пользовательского интерфейса для страницы с использованием расширяемого языка разметки приложений (XAML).
- Взаимодействие с элементами пользовательского интерфейса XAML из кода.

Кратком руководстве показано, как создать кроссплатформенное приложение Xamarin.Forms, что дает возможность ввести примечание и сохранить их в хранилище устройства. Ниже показано итоговое приложение:

[![](single-page-images/screenshots-sml.png "Приложение «Заметки»")](single-page-images/screenshots.png#lightbox "Приложение «Заметки»")

::: zone pivot="windows"

### <a name="prerequisites"></a>Предварительные требования

- Visual Studio 2017 (последний выпуск), с помощью **разработки мобильных приложений на .NET** установленной рабочей нагрузкой.
- Знание C#.
- (необязательно) Связанном компьютере Mac, чтобы создать приложение на iOS.

Дополнительные сведения об этих требованиях см. в разделе [Установка Xamarin](~/cross-platform/get-started/installation/index.md). Сведения о подключении Visual Studio 2017 к узлу сборки Mac, см. в разделе [связывание с компьютером Mac для разработки Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio"></a>Начало работы с Visual Studio

1. Запустите Visual Studio, а также на начальной странице нажмите кнопку **создать проект...**  для создания нового проекта:

    ![](single-page-images/vs/new-solution.png "Создание проекта")

2. В диалоговом окне **Новый проект** щелкните **Кроссплатформенный**, выберите шаблон **Мобильное приложение (Xamarin.Forms)**, присвойте параметру "Имя" значение **Notes**, выберите подходящее расположение для проекта и нажмите кнопку **ОК**:

    ![](single-page-images/vs/new-project.png "Шаблоны кроссплатформенных проектов")

    > [!IMPORTANT]
    > Фрагменты кода на C# и XAML из этого краткого руководства предполагают, что решение называется **Notes**. Выбор другого имени приведет к ошибкам сборки при копировании кода из этого краткого руководства в решение.

3. В диалоговом окне **Новое кроссплатформенное приложение** щелкните **Пустое приложение**, выберите **.NET Standard** в качестве стратегии совместного использования кода, а затем нажмите кнопку **OK**:

    ![](single-page-images/vs/new-app.png "Новое кроссплатформенное приложение")

    Дополнительные сведения о библиотеке .NET Standard, который создается, см. в разделе [структура приложения Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

4. В **обозревателе решений** дважды щелкните файл **MainPage.xaml** в проекте **Notes**, чтобы открыть его:

    ![](single-page-images/vs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

5. Удалите из **MainPage.xaml** весь шаблонный код и замените его приведенным ниже.

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [`Label`](xref:Xamarin.Forms.Label) для отображения текста, [`Editor`](xref:Xamarin.Forms.Editor) для ввода текста, а также двух экземпляров [`Button`](xref:Xamarin.Forms.Button), которые помогают приложению сохранить или удалить файл. Два экземпляра `Button` располагаются по горизонтали в [`Grid`](xref:Xamarin.Forms.Grid), а `Label`, `Editor` и `Grid` — по вертикали в [`StackLayout`](xref:Xamarin.Forms.StackLayout). Дополнительные сведения о создании пользовательского интерфейса см. в разделе [пользовательский интерфейс](deepdive.md#user-interface) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в файле **MainPage.xaml**, нажав клавиши **CTRL+S**, и закройте файл.

6. В **обозревателе решений** в проекте **Notes** разверните узел **MainPage.xaml** и дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](single-page-images/vs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

7. Удалите из **MainPage.xaml.cs** весь шаблонный код и замените его приведенным ниже.

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Этот код определяет поле `_fileName`, которое ссылается на файл с именем `notes.txt`, где будут храниться данные с заметками в локальной папке данных для приложения. При выполнении конструктора страниц файл считывается, если он существует, и отображается в [`Editor`](xref:Xamarin.Forms.Editor). При нажатии кнопки **Сохранить** [`Button`](xref:Xamarin.Forms.Button) выполняется обработчик событий `OnSaveButtonClicked`, который сохраняет содержимое `Editor` в файле. При нажатии кнопки **Удалить** `Button` выполняется обработчик событий `OnDeleteButtonClicked`, который удаляет файл при условии, что он существует, и весь текст из `Editor`. Дополнительные сведения о взаимодействия с пользователем, см. в разделе [взаимодействие с пользователем](deepdive.md#responding-to-user-interaction) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в файле **MainPage.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

### <a name="building-the-quickstart"></a>Сборка примера из краткого руководства

1. В Visual Studio выберите элемент меню **Сборка > Построить решение** (или нажмите клавишу F6). Выполняется сборка решения, а в строке состояния Visual Studio отображается сообщение об успешном выполнении:

      ![](single-page-images/vs/build-succeeded.png "Сборка успешно завершена")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка решения не будет проходить успешно.

2. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в выбранном эмуляторе Android.

    ![](single-page-images/vs/android-start.png "Панель инструментов Visual Studio для Android")

    [![](single-page-images/vs/notes-android.png "Заметки в эмуляторе Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Введите примечание и нажмите кнопку **Сохранить**.

    Дополнительные сведения о том, как запустить приложение на каждой платформе, см. в разделе [запуска приложения на каждой платформе](deepdive.md#launching-the-application-on-each-platform) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Следующие шаги следует выполнить, только если у вас есть [связанный компьютер Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md), отвечающий требованиям к системе для разработки приложений Xamarin.Forms.

3. На панели инструментов Visual Studio щелкните правой кнопкой мыши проект **Notes.iOS**, а затем выберите команду **Назначить запускаемым проектом**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Назначение iOS запускаемым проектом")

4. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в выбранном [удаленном эмуляторе для iOS](~/tools/ios-simulator/index.md).

    ![](single-page-images/vs/ios-start.png "Панель инструментов Visual Studio для iOS")

    [![](single-page-images/vs/notes-ios.png "Заметки в симуляторе iOS")](single-page-images/vs/notes-ios-large.png#lightbox "заметки в симуляторе iOS")

    Введите примечание и нажмите кнопку **Сохранить**.

    Дополнительные сведения о том, как запустить приложение на каждой платформе, см. в разделе [запуска приложения на каждой платформе](deepdive.md#launching-the-application-on-each-platform) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Предварительные требования

- Visual Studio для Mac (последний выпуск), iOS и установленной поддержки платформы Android.
- Xcode (последний выпуск).
- Знание C#.

Дополнительные сведения об этих требованиях см. в разделе [Установка Xamarin](~/cross-platform/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Начало работы с Visual Studio для Mac

1. Запустите Visual Studio для Mac и на начальной странице щелкните **Новый проект...** , чтобы создать новый проект:

    ![](single-page-images/vsmac/new-project.png "Создание решения")

2. В диалоговом окне **Выберите шаблон из нового проекта** щелкните **Многоплатформенность > Приложение** и выберите шаблон **Приложение с пустыми формами**, а затем нажмите кнопку **Далее**:

    ![](single-page-images/vsmac/choose-template.png "Выбор шаблона")

3. В диалоговом окне **Настройка приложения с пустыми формами** присвойте новому приложению имя **Notes**, убедитесь, что переключатель установлен в положение **Использовать .NET Standard** и нажмите кнопку **Далее**:    

    ![](single-page-images/vsmac/configure-app.png "Настройка приложения Forms")

4. В диалоговом окне **Настроить новое приложение с пустыми формами** сохраните для проекта и решения имя **Notes**, выберите подходящее расположение для проекта и нажмите кнопку **Создать** для создания проекта:

    ![](single-page-images/vsmac/configure-project.png "Настройка проекта Forms")

    > [!IMPORTANT]
    > Фрагменты кода на C# и XAML из этого краткого руководства предполагают, что решение и проект называются **Notes**. Выбор другого имени приведет к ошибкам сборки при копировании кода из этого краткого руководства в проект.

    Дополнительные сведения о библиотеке .NET Standard, который создается, см. в разделе [структура приложения Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

5. В **панели решения** дважды щелкните файл **MainPage.xaml** в проекте **Notes**, чтобы открыть его:

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. Удалите из **MainPage.xaml** весь шаблонный код и замените его приведенным ниже.

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [`Label`](xref:Xamarin.Forms.Label) для отображения текста, [`Editor`](xref:Xamarin.Forms.Editor) для ввода текста, а также двух экземпляров [`Button`](xref:Xamarin.Forms.Button), которые помогают приложению сохранить или удалить файл. Два экземпляра `Button` располагаются по горизонтали в [`Grid`](xref:Xamarin.Forms.Grid), а `Label`, `Editor` и `Grid` — по вертикали в [`StackLayout`](xref:Xamarin.Forms.StackLayout). Дополнительные сведения о создании пользовательского интерфейса см. в разделе [пользовательский интерфейс](deepdive.md#user-interface) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **MainPage.xaml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

7. В **панели решений** в проекте **Notes** разверните узел **MainPage.xaml** и дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. Удалите из **MainPage.xaml.cs** весь шаблонный код и замените его приведенным ниже.

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Этот код определяет поле `_fileName`, которое ссылается на файл с именем `notes.txt`, где будут храниться данные с заметками в локальной папке данных для приложения. При выполнении конструктора страниц файл считывается, если он существует, и отображается в [`Editor`](xref:Xamarin.Forms.Editor). При нажатии кнопки **Сохранить** [`Button`](xref:Xamarin.Forms.Button) выполняется обработчик событий `OnSaveButtonClicked`, который сохраняет содержимое `Editor` в файле. При нажатии кнопки **Удалить** `Button` выполняется обработчик событий `OnDeleteButtonClicked`, который удаляет файл при условии, что он существует, и весь текст из `Editor`. Дополнительные сведения о взаимодействия с пользователем, см. в разделе [взаимодействие с пользователем](deepdive.md#responding-to-user-interaction) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **MainPage.xaml.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984; + S**), а затем закройте файл.

### <a name="building-the-quickstart"></a>Сборка примера из краткого руководства

1. В Visual Studio для Mac выберите элемент меню **Сборка > Собрать все** (или нажмите клавиши **&#8984;+B**). Выполняется сборка проектов, а на панели инструментов Visual Studio для Mac отображается сообщение об успешном выполнении:

      ![](single-page-images/vsmac/build-successful.png "Успешное выполнение сборки")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка проектов не будет проходить успешно.

2. В **панели решения**выберите **Notes.iOS** проекта, щелкните правой кнопкой мыши и выберите **Назначить запускаемым проектом**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Назначение iOS запускаемым проектом")

3. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения) для запуска приложения в выбранном симуляторе iOS:

      ![](single-page-images/vsmac/start.png "Панель инструментов Visual Studio для Mac")

      [![](single-page-images/vsmac/notes-ios.png "Заметки в симуляторе iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "заметки в симуляторе iOS")

    Введите примечание и нажмите кнопку **Сохранить**.

    Дополнительные сведения о том, как запустить приложение на каждой платформе, см. в разделе [запуска приложения на каждой платформе](deepdive.md#launching-the-application-on-each-platform) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

4. В **панели решения**выберите **Notes.Droid** проекта, щелкните правой кнопкой мыши и выберите **Назначить запускаемым проектом**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Назначение Android запускаемым проектом")

5. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения) для запуска приложения в выбранном эмуляторе Android:

      [![](single-page-images/vsmac/notes-android.png "Заметки в эмуляторе Android")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Введите примечание и нажмите кнопку **Сохранить**.

    Дополнительные сведения о том, как запустить приложение на каждой платформе, см. в разделе [запуска приложения на каждой платформе](deepdive.md#launching-the-application-on-each-platform) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы узнали, как:

- Создание кросс платформенные приложения Xamarin.Forms.
- Определение пользовательского интерфейса для страницы с использованием расширяемого языка разметки приложений (XAML).
- Взаимодействие с элементами пользовательского интерфейса XAML из кода.

Чтобы включить это одностраничное приложение в одностраничное приложение, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Вперед](multi-page.md)

## <a name="related-links"></a>Связанные ссылки

- [Заметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
- [Краткое руководство по Xamarin.Forms глубокое погружение в обработку](deepdive.md)
