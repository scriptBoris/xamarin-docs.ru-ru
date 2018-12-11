---
title: Краткое руководство по Xamarin.Forms
description: В этой статье объясняется, как создать с помощью Xamarin.Forms простое кроссплатформенное приложение для заметок, которое позволяет ввести примечание и сохранить его в хранилище устройства.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/21/2018
ms.openlocfilehash: 880fa527d91098cf8c5f8c46cd9c5cce9ec9a489
ms.sourcegitcommit: 744c0a50420bb091fca8b92a84c20e61c741cf9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52743137"
---
# <a name="xamarinforms-quickstart"></a>Краткое руководство по Xamarin.Forms

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

В этом кратком руководстве объясняется, как создать с помощью Xamarin.Forms простое кроссплатформенное приложение для заметок, которое позволяет ввести примечание и сохранить его в хранилище устройства. Ниже показано итоговое приложение:

[![](quickstart-experimental-images/screenshots-sml.png "Приложение «Заметки»")](quickstart-experimental-images/screenshots.png#lightbox "Приложение «Заметки»")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Начало работы с Visual Studio

1. На **начальном экране** запустите Visual Studio. Открывается начальная страница:

    ![](quickstart-experimental-images/vs/start-page.png "Visual Studio")

2. В Visual Studio щелкните **Создать проект...**, чтобы создать проект:

    ![](quickstart-experimental-images/vs/new-solution.png "Создание проекта")

3. В диалоговом окне **Новый проект** щелкните **Кроссплатформенный**, выберите шаблон **Мобильное приложение (Xamarin.Forms)**, присвойте параметру "Имя" значение **Notes**, выберите подходящее расположение для проекта и нажмите кнопку **ОК**:

    ![](quickstart-experimental-images/vs/new-project.png "Шаблоны кроссплатформенных проектов")

    > [!IMPORTANT]
    > Фрагменты кода на C# и XAML из этого краткого руководства предполагают, что решение называется **Notes**. Выбор другого имени приведет к ошибкам сборки при копировании кода из этого краткого руководства в решение.

4. В диалоговом окне **Новое кроссплатформенное приложение** щелкните **Пустое приложение**, выберите **.NET Standard** в качестве стратегии совместного использования кода, а затем нажмите кнопку **OK**:

    ![](quickstart-experimental-images/vs/new-app.png "Новое кроссплатформенное приложение")

5. В **обозревателе решений** дважды щелкните файл **MainPage.xaml** в проекте **Notes**, чтобы открыть его:

    ![](quickstart-experimental-images/vs/open-mainpage-xaml.png "Открытие файла MainPage.xaml")

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
            <Editor x:Name="_editor"
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

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [`Label`](xref:Xamarin.Forms.Label) для отображения текста, [`Editor`](xref:Xamarin.Forms.Editor) для ввода текста, а также двух экземпляров [`Button`](xref:Xamarin.Forms.Button), которые помогают приложению сохранить или удалить файл. Два экземпляра `Button` располагаются по горизонтали в [`Grid`](xref:Xamarin.Forms.Grid), а `Label`, `Editor` и `Grid` — по вертикали в [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Сохраните изменения в файле **MainPage.xaml**, нажав клавиши **CTRL+S**, и закройте файл.

7. В **обозревателе решений** в проекте **Notes** разверните узел **MainPage.xaml** и дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](quickstart-experimental-images/vs/open-mainpage-codebehind.png "Открытие файла MainPage.xaml.cs")

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
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Этот код определяет поле `_fileName`, которое ссылается на файл с именем `notes.txt`, где будут храниться данные с заметками в локальной папке данных для приложения. При выполнении конструктора страниц файл считывается, если он существует, и отображается в [`Editor`](xref:Xamarin.Forms.Editor). При нажатии кнопки **Сохранить** [`Button`](xref:Xamarin.Forms.Button) выполняется обработчик событий `OnSaveButtonClicked`, который сохраняет содержимое `Editor` в файле. При нажатии кнопки **Удалить** `Button` выполняется обработчик событий `OnDeleteButtonClicked`, который удаляет файл при условии, что он существует, и весь текст из `Editor`.

    Сохраните изменения в файле **MainPage.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

### <a name="building-the-quickstart"></a>Сборка примера из краткого руководства

1. В Visual Studio выберите элемент меню **Сборка > Построить решение** (или нажмите клавишу F6). Выполняется сборка решения, а в строке состояния Visual Studio отображается сообщение об успешном выполнении:

      ![](quickstart-experimental-images/vs/build-succeeded.png "Сборка успешно завершена")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка решения не будет проходить успешно.

2. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в выбранном эмуляторе Android.

    ![](quickstart-experimental-images/vs/android-start.png "Панель инструментов Visual Studio для Android")

    ![](quickstart-experimental-images/vs/notes-android.png "Приложение Notes в эмуляторе Android")

    Введите примечание и нажмите кнопку **Сохранить**.

    > [!NOTE]
    > Следующие шаги следует выполнить, только если у вас есть [связанный компьютер Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md), отвечающий требованиям к системе для разработки приложений Xamarin.Forms.

3. На панели инструментов Visual Studio щелкните правой кнопкой мыши проект **Notes.iOS**, а затем выберите команду **Назначить запускаемым проектом**.

      ![](quickstart-experimental-images/vs/set-as-startup-project-ios.png "Назначение iOS запускаемым проектом")

4. На панели инструментов Visual Studio нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения), чтобы запустить приложение в выбранном [удаленном эмуляторе для iOS](~/tools/ios-simulator/index.md).

    ![](quickstart-experimental-images/vs/ios-start.png "Панель инструментов Visual Studio для iOS")

    ![](quickstart-experimental-images/vs/notes-ios.png "Приложение Notes в симуляторе iOS")

    Введите примечание и нажмите кнопку **Сохранить**.

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Начало работы с Visual Studio для Mac

1. Запустите Visual Studio для Mac и на начальной странице щелкните **Новый проект...** , чтобы создать новый проект:

    ![](quickstart-experimental-images/vsmac/new-project.png "Создание решения")

2. В диалоговом окне **Выберите шаблон из нового проекта** щелкните **Многоплатформенность > Приложение** и выберите шаблон **Приложение с пустыми формами**, а затем нажмите кнопку **Далее**:

    ![](quickstart-experimental-images/vsmac/choose-template.png "Выбор шаблона")

3. В диалоговом окне **Настройка приложения с пустыми формами** присвойте новому приложению имя **Notes**, убедитесь, что переключатель установлен в положение **Использовать .NET Standard** и нажмите кнопку **Далее**:    

    ![](quickstart-experimental-images/vsmac/configure-app.png "Настройка приложения Forms")

4. В диалоговом окне **Настроить новое приложение с пустыми формами** сохраните для проекта и решения имя **Notes**, выберите подходящее расположение для проекта и нажмите кнопку **Создать** для создания проекта:

    ![](quickstart-experimental-images/vsmac/configure-project.png "Настройка проекта Forms")

    > [!IMPORTANT]
    > Фрагменты кода на C# и XAML из этого краткого руководства предполагают, что решение и проект называются **Notes**. Выбор другого имени приведет к ошибкам сборки при копировании кода из этого краткого руководства в проект.

5. В **панели решения** дважды щелкните файл **MainPage.xaml** в проекте **Notes**, чтобы открыть его:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml.png "MainPage.xaml")

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
            <Editor x:Name="_editor"
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

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [`Label`](xref:Xamarin.Forms.Label) для отображения текста, [`Editor`](xref:Xamarin.Forms.Editor) для ввода текста, а также двух экземпляров [`Button`](xref:Xamarin.Forms.Button), которые помогают приложению сохранить или удалить файл. Два экземпляра `Button` располагаются по горизонтали в [`Grid`](xref:Xamarin.Forms.Grid), а `Label`, `Editor` и `Grid` — по вертикали в [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Сохраните изменения в **MainPage.xaml**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

7. В **панели решений** в проекте **Notes** разверните узел **MainPage.xaml** и дважды щелкните файл **MainPage.xaml.cs**, чтобы открыть его:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

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
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Этот код определяет поле `_fileName`, которое ссылается на файл с именем `notes.txt`, где будут храниться данные с заметками в локальной папке данных для приложения. При выполнении конструктора страниц файл считывается, если он существует, и отображается в [`Editor`](xref:Xamarin.Forms.Editor). При нажатии кнопки **Сохранить** [`Button`](xref:Xamarin.Forms.Button) выполняется обработчик событий `OnSaveButtonClicked`, который сохраняет содержимое `Editor` в файле. При нажатии кнопки **Удалить** `Button` выполняется обработчик событий `OnDeleteButtonClicked`, который удаляет файл при условии, что он существует, и весь текст из `Editor`.

    Сохраните изменения в **MainPage.xaml.cs**, выбрав **Файл > Сохранить** (или нажав клавиши **&#8984; + S**), а затем закройте файл.

### <a name="building-the-quickstart"></a>Сборка примера из краткого руководства

1. В Visual Studio для Mac выберите элемент меню **Сборка > Собрать все** (или нажмите клавиши **&#8984;+B**). Выполняется сборка проектов, а на панели инструментов Visual Studio для Mac отображается сообщение об успешном выполнении:

      ![](quickstart-experimental-images/vsmac/build-successful.png "Успешное выполнение сборки")

    При наличии ошибок повторите предыдущие шаги и исправьте все ошибки, пока сборка проектов не будет проходить успешно.

2. В **панели решения** щелкните правой кнопкой мыши проект **Notes.iOS** и выберите команду **Назначить запускаемым проектом**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-ios.png "Назначение iOS запускаемым проектом")

3. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения) для запуска приложения в выбранном симуляторе iOS:

      ![](quickstart-experimental-images/vsmac/start.png "Панель инструментов Visual Studio для Mac")

      ![](quickstart-experimental-images/vsmac/notes-ios.png "Приложение Notes в симуляторе iOS")

    Введите примечание и нажмите кнопку **Сохранить**.

4. В **панели решения** щелкните правой кнопкой мыши проект **Notes.Droid** и выберите команду **Назначить запускаемым проектом**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-android.png "Назначение Android запускаемым проектом")

5. На панели инструментов Visual Studio для Mac нажмите клавишу **Запустить** (треугольная кнопка, похожая на кнопку воспроизведения) для запуска приложения в выбранном эмуляторе Android:

      ![](quickstart-experimental-images/vsmac/notes-android.png "Приложение Notes в эмуляторе Android")

    Введите примечание и нажмите кнопку **Сохранить**.

::: zone-end

## <a name="related-links"></a>Связанные ссылки

- [Заметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
