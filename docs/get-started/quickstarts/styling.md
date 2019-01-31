---
title: Стиль кросс платформенные приложения Xamarin.Forms
description: В этой статье объясняется, как изменить стиль кросс платформенные приложения Xamarin.Forms с помощью стилей XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 56f8632c9cc7d170b4c6594fd51e6aa3e92ad02b
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293342"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Стиль кросс платформенные приложения Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

В этом кратком руководстве вы узнаете, как:

- Приложение Xamarin.Forms, с помощью стилей XAML в стиле.

Кратком руководстве показано, как изменить стиль кросс платформенные приложения Xamarin.Forms с помощью стилей XAML. Ниже показано итоговое приложение:

[![](styling-images/screenshots1-sml.png "Страницы заметок")](styling-images/screenshots1.png#lightbox "страницы заметок")
[![](styling-images/screenshots2-sml.png "Обратите внимание, первая страница")](styling-images/screenshots2.png#lightbox "Примечание Страница входа")

### <a name="prerequisites"></a>Предварительные требования

При успешном выполнении [предыдущего краткого руководства](database.md) перед попыткой в этом кратком руководстве. Кроме того, скачать [предыдущих примеров](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/) и использовать его в качестве отправной точки для этого краткого руководства.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Обновление приложения с помощью Visual Studio

1. Запустите Visual Studio и откройте решение заметки.

2. В **обозревателе решений**в **заметки** проекта, дважды щелкните **App.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Этот код определяет [ `Thickness` ](xref:Xamarin.Forms.Thickness) значение ряда [ `Color` ](xref:Xamarin.Forms.Color) значения и неявные стили для [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) и [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Обратите внимание на том, что эти стили, размещаются на уровне приложения [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), могут быть использованы в приложении. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **App.xaml** , нажав клавишу **CTRL + S**и закройте файл.

3. В **обозревателе решений**в **заметки** проекта, дважды щелкните **NotesPage.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Этот код добавляет неявный стиль для [ `ListView` ](xref:Xamarin.Forms.ListView) на уровне страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и задает `ListView.Margin` свойству значение, определенное на уровне приложения `ResourceDictionary` . Обратите внимание, что `ListView` неявный стиль был добавлен к страницам `ResourceDictionary`, так как он используется только операцией `NotesPage`. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml** , нажав клавишу **CTRL + S**и закройте файл.

5. В **панели решения**в **заметки** проекта, дважды щелкните **NoteEntryPage.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
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
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Этот код добавляет неявные стили для [ `Editor` ](xref:Xamarin.Forms.Editor) и [ `Button` ](xref:Xamarin.Forms.Button) представления на уровне страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и задает `StackLayout.Margin` свойство, значение, определенное на уровне приложения `ResourceDictionary`. Обратите внимание, что `Editor` и `Button` неявные стили, добавленные на уровне страницы `ResourceDictionary`, так как они используются только с `NoteEntryPage`. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NoteEntryPage.xaml** , нажав клавишу **CTRL + S**и закройте файл.

6. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. На каждой странице наблюдайте за изменении стилей из предыдущего краткого руководства.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Обновление приложения с помощью Visual Studio для Mac

1. Запустите Visual Studio для Mac и открыть проект заметки.

2. В **панели решения**в **заметки** проекта, дважды щелкните **App.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Этот код определяет [ `Thickness` ](xref:Xamarin.Forms.Thickness) значение ряда [ `Color` ](xref:Xamarin.Forms.Color) значения и неявные стили для [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) и [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Обратите внимание на том, что эти стили, размещаются на уровне приложения [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), могут быть использованы в приложении. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **App.xaml** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

3. В **обозревателе решений**в **заметки** проекта, дважды щелкните **NotesPage.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Этот код добавляет неявный стиль для [ `ListView` ](xref:Xamarin.Forms.ListView) на уровне страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и задает `ListView.Margin` свойству значение, определенное на уровне приложения `ResourceDictionary` . Обратите внимание, что `ListView` неявный стиль был добавлен к страницам `ResourceDictionary`, так как он используется только операцией `NotesPage`. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

5. В **обозревателе решений**в **заметки** проекта, дважды щелкните **NoteEntryPage.xaml** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
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
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Этот код добавляет неявные стили для [ `Editor` ](xref:Xamarin.Forms.Editor) и [ `Button` ](xref:Xamarin.Forms.Button) представления на уровне страницы [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)и задает `StackLayout.Margin` свойство, значение, определенное на уровне приложения `ResourceDictionary`. Обратите внимание, что `Editor` и `Button` неявные стили, добавленные на уровне страницы `ResourceDictionary`, так как они используются только с `NoteEntryPage`. Дополнительные сведения о стили XAML, см. в разделе [Стилизация](deepdive.md#styling) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NoteEntryPage.xaml** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

6. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. На каждой странице наблюдайте за изменении стилей из предыдущего краткого руководства.

::: zone-end


## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы узнали, как:

- Приложение Xamarin.Forms, с помощью стилей XAML в стиле.

Дополнительные сведения об основах разработки приложений с помощью Xamarin.Forms, продолжайте подробный обзор быстрого запуска.

> [!div class="nextstepaction"]
> [Вперед](deepdive.md)

## <a name="related-links"></a>Связанные ссылки

- [Заметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Краткое руководство по Xamarin.Forms глубокое погружение в обработку](deepdive.md)
