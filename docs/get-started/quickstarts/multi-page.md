---
title: Выполнять навигации в приложении Xamarin.Forms многостраничных
description: В этой статье объясняется, как включить одностраничного приложения, который может хранить одно примечание в одностраничное приложение, может хранить несколько заметок.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: f3c5ec1c6d37c74fd9b4fb4980c68a59c9864c05
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197372"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Выполнять навигации в приложении Xamarin.Forms многостраничных

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

В этом кратком руководстве вы узнаете, как:

- Добавление дополнительных страниц в решение Xamarin.Forms.
- Выполните переход между страницами.
- Используйте привязку данных для синхронизации данных между элементами пользовательского интерфейса и источник данных.

Кратком руководстве показано, как включить одностраничное кросс платформенные приложения Xamarin.Forms, который может хранить одно примечание в одностраничное приложение, может хранить несколько заметок. Ниже показано итоговое приложение:

[![](multi-page-images/screenshots1-sml.png "Страницы заметок")](multi-page-images/screenshots1.png#lightbox "страницы заметок")
[![](multi-page-images/screenshots2-sml.png "Обратите внимание, первая страница")](multi-page-images/screenshots2.png#lightbox "Примечание Страница входа")

### <a name="prerequisites"></a>Предварительные требования

При успешном выполнении [предыдущего краткого руководства](single-page.md) перед попыткой в этом кратком руководстве. Кроме того, скачать [предыдущих примеров](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/) и использовать его в качестве отправной точки для этого краткого руководства.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Обновление приложения с помощью Visual Studio

1. Запустите Visual Studio. На начальной странице щелкните **открыть проект / решение**и в **Открытие проекта** диалоговом окне выберите файл решения для проекта примечания:

    ![](multi-page-images/vs/open-solution.png "Открытие проекта")

2. В **обозревателе решений**, щелкните правой кнопкой мыши **заметки** проекта и выберите **Добавить > Новая папка**:

    ![](multi-page-images/vs/add-new-item.png "Добавление нового элемента")

3. В **обозревателе решений**, имя новой папки **моделей**:

    ![](multi-page-images/vs/name-folder.png "Папку Models")

4. В **обозревателе решений**выберите **моделей** папку, щелкните правой кнопкой мыши и выберите **Добавить > новый элемент...** :

    ![](multi-page-images/vs/add-new-models-file.png "Добавление нового файла")

5. В **Добавление нового элемента** диалоговом окне выберите **Visual C# элементы > класс**, назовите новый файл **Примечание**и нажмите кнопку **добавить** кнопки:

    ![](multi-page-images/vs/add-note-class.png "Добавьте класс Примечание")

    Это добавит класс с именем **Примечание** для **моделей** папке **заметки** проекта.

6. В **Note.cs**, удалите весь код шаблона и замените его следующим кодом:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Этот класс определяет `Note` модели, в которой будут храниться данные о каждом Примечание в приложении.    

    Сохраните изменения в **Note.cs** , нажав клавишу **CTRL + S**и закройте файл.

7. В **обозревателе решений**, щелкните правой кнопкой мыши **заметки** проекта и выберите **Добавить > новый элемент...** . В **Добавление нового элемента** диалоговом окне выберите **Visual C# элементы > Xamarin.Forms > страница содержимого**, назовите новый файл **NoteEntryPage**и нажмите кнопку  **Добавление** кнопки:

    ![](multi-page-images/vs/add-note-entry-page.png "Добавление Xamarin.Forms ContentPage")

    Это добавит новую страницу с именем **NoteEntryPage** в корневую папку проекта. Эта страница будет второй страницы в приложении.

8. В **NoteEntryPage.xaml**, удалите весь код шаблона и замените его следующим кодом:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [ `Editor` ](xref:Xamarin.Forms.Editor) для ввода текста, а также два [ `Button` ](xref:Xamarin.Forms.Button) экземпляров, которые направляют сохранения или удаления приложения файл. Два `Button` экземпляров располагаются по горизонтали в [ `Grid` ](xref:Xamarin.Forms.Grid), с помощью `Editor` и `Grid` по вертикали изложенные в [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Кроме того `Editor` использует привязку данных для привязки к `Text` свойство `Note` модели. Дополнительные сведения о привязке данных см. в разделе [привязки данных](deepdive.md#data-binding) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

      Сохраните изменения в **NoteEntryPage.xaml** , нажав клавишу **CTRL + S**и закройте файл.

9. В **NoteEntryPage.xaml.cs**, удалите весь код шаблона и замените его следующим кодом:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Этот код сохраняет `Note` экземпляр, который представляет отдельного комментария в [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) страницы. Когда **Сохранить** [ `Button` ](xref:Xamarin.Forms.Button) нажата `OnSaveButtonClicked` выполняется обработчик событий, который либо сохраняет содержимое `Editor` в новый файл с случайное имя файла, или в существующий файл, если Примечание обновляется. В обоих случаях файл хранится в папке данных локального приложения для приложения. Затем метод выполняет переход к предыдущей странице. Когда **удалить** `Button` нажата `OnDeleteButtonClicked` выполняется обработчик событий, который удаляет файл, в условии, что он существует и переходит обратно к предыдущей странице. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

      Сохраните изменения в **NoteEntryPage.xaml.cs** , нажав клавишу **CTRL + S**и закройте файл.

      > [!WARNING]
      > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

10. В **обозревателе решений**, щелкните правой кнопкой мыши **заметки** проекта и выберите **Добавить > новый элемент...** . В **Добавление нового элемента** диалоговом окне выберите **Visual C# элементы > Xamarin.Forms > страница содержимого**, назовите новый файл **NotesPage**и нажмите кнопку **добавить**  кнопки.

      Это добавит страницы с именем **NotesPage** в корневую папку проекта. Эта страница будет на корневую страницу приложения.

11. В **NotesPage.xaml**, удалите весь код шаблона и замените его следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [ `ListView` ](xref:Xamarin.Forms.ListView) и [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). `ListView` Использует данные привязки для отображения любые заметки, которые извлекаются приложением, а затем выбрав примечание, произойдет переход к `NoteEntryPage` где можно изменить заметку. Кроме того, можно создать новую заметку, нажав клавишу `ToolbarItem`. Дополнительные сведения о привязке данных см. в разделе [привязки данных](deepdive.md#data-binding) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml** , нажав клавишу **CTRL + S**и закройте файл.

12. В **NotesPage.xaml.cs**, удалите весь код шаблона и замените его следующим кодом:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Этот код определяет функциональные возможности для `NotesPage`. Когда откроется страница, `OnAppearing` выполняется метод, который заполняет [ `ListView` ](xref:Xamarin.Forms.ListView) с любые заметки, которые были извлечены из папки данных локального приложения. Когда [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) нажата `OnNoteAddedClicked` выполняется обработчик событий. Этот метод переходит к `NoteEntryPage`, задание [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` в новый `Note` экземпляра. При создании записи в `ListView` выбран `OnListViewItemSelected` выполняется обработчик событий. Этот метод переходит к `NoteEntryPage`, задание [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` к выбранному `Note` экземпляра. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml.cs** , нажав клавишу **CTRL + S**и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

13. В **обозревателе решений**, дважды щелкните **App.xaml.cs** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Этот код добавляет объявление пространства имен для `System.IO` пространства имен и добавляет объявление статического `FolderPath` свойство типа `string`. `FolderPath` Свойство используется для сохранения пути на устройстве, где будут храниться данные Примечание. Кроме того, этот код инициализирует `FolderPath` свойство в `App` конструктор и инициализирует [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойства [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) , на котором размещена экземпляр `NotesPage`. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в файле **App.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

14. В **обозревателе решений**в **заметки** щелкните правой кнопкой мыши проект **MainPage.xaml**и выберите **удалить**. В диалоговом окне, появившемся press **ОК** кнопку, чтобы удалить файл с жесткого диска.

    При этом удаляются страницу, которая больше не используется.

15. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. После сохранении заметки приложение, произойдет переход обратно к **NotesPage**.

    Введите число заметки различного размера, чтобы наблюдать за поведением приложения.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Обновление приложения с помощью Visual Studio для Mac

1. Запуск Visual Studio для Mac На начальной странице щелкните **открыть...** и в диалоговом окне выберите файл решения для проекта примечания:

    ![](multi-page-images/vsmac/open-solution.png "Открытие решения")

2. В **панели решения**выберите **заметки** проекта, щелкните правой кнопкой мыши и выберите **Добавить > Новая папка**:

    ![](multi-page-images/vsmac/add-new-folder.png "Добавить новую папку")

3. В **панели решения**, имя новой папки **моделей**:

    ![](multi-page-images/vsmac/name-folder.png "Папку Models")

4. В **панели решения**выберите **моделей** папку, щелкните правой кнопкой мыши и выберите **Добавить > новый файл...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Добавление нового файла")

5. В **новый файл** диалоговом окне выберите **Общие > пустой класс**, назовите новый файл **Примечание**и нажмите кнопку **New** кнопки:

    ![](multi-page-images/vsmac/add-note-class.png "Добавьте класс Примечание")

    Это добавит класс с именем **Примечание** для **моделей** папке **заметки** проекта.

6. В **Note.cs**, удалите весь код шаблона и замените его следующим кодом:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Этот класс определяет `Note` модели, в которой будут храниться данные о каждом Примечание в приложении.

    Сохраните изменения в **Note.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

7. В **панели решения**выберите **заметки** проекта, щелкните правой кнопкой мыши и выберите **Добавить > новый файл...** . В **новый файл** диалоговом окне выберите **Forms > XAML ContentPage Forms**, назовите новый файл **NoteEntryPage**и нажмите кнопку **New** кнопки:

    ![](multi-page-images/vsmac/add-note-entry-page.png "Добавление Xamarin.Forms ContentPage")

    Это добавит новую страницу с именем **NoteEntryPage** в корневую папку проекта. Эта страница будет второй страницы в приложении.

8. В **NoteEntryPage.xaml**, удалите весь код шаблона и замените его следующим кодом:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [ `Editor` ](xref:Xamarin.Forms.Editor) для ввода текста, а также два [ `Button` ](xref:Xamarin.Forms.Button) экземпляров, которые направляют сохранения или удаления приложения файл. Два `Button` экземпляров располагаются по горизонтали в [ `Grid` ](xref:Xamarin.Forms.Grid), с помощью `Editor` и `Grid` по вертикали изложенные в [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Кроме того `Editor` использует привязку данных для привязки к `Text` свойство `Note` модели. Дополнительные сведения о привязке данных см. в разделе [привязки данных](deepdive.md#data-binding) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

      Сохраните изменения в **NoteEntryPage.xaml** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

9. В **NoteEntryPage.xaml.cs**, удалите весь код шаблона и замените его следующим кодом:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Этот код сохраняет `Note` экземпляр, который представляет отдельного комментария в [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) страницы. Когда **Сохранить** [ `Button` ](xref:Xamarin.Forms.Button) нажата `OnSaveButtonClicked` выполняется обработчик событий, который либо сохраняет содержимое `Editor` в новый файл с случайное имя файла, или в существующий файл, если Примечание обновляется. В обоих случаях файл хранится в папке данных локального приложения для приложения. Затем метод выполняет переход к предыдущей странице. Когда **удалить** `Button` нажата `OnDeleteButtonClicked` выполняется обработчик событий, который удаляет файл, в условии, что он существует и переходит обратно к предыдущей странице. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

      Сохраните изменения в **NoteEntryPage.xaml.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

      > [!WARNING]
      > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

10. В **панели решения**выберите **заметки** проекта, щелкните правой кнопкой мыши и выберите **Добавить > новый файл...** . В **новый файл** диалоговом окне выберите **Forms > XAML ContentPage Forms**, назовите новый файл **NotesPage**и нажмите кнопку **New** кнопки.

      Это добавит страницы с именем **NotesPage** в корневую папку проекта. Эта страница будет на корневую страницу приложения.

11. В **NotesPage.xaml**, удалите весь код шаблона и замените его следующим кодом:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Этот код декларативно определяет пользовательский интерфейс для страницы, которая состоит из [ `ListView` ](xref:Xamarin.Forms.ListView) и [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). `ListView` Использует данные привязки для отображения любые заметки, которые извлекаются приложением, а затем выбрав примечание, произойдет переход к `NoteEntryPage` где можно изменить заметку. Кроме того, можно создать новую заметку, нажав клавишу `ToolbarItem`. Дополнительные сведения о привязке данных см. в разделе [привязки данных](deepdive.md#data-binding) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

12. В **NotesPage.xaml.cs**, удалите весь код шаблона и замените его следующим кодом:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Этот код определяет функциональные возможности для `NotesPage`. Когда откроется страница, `OnAppearing` выполняется метод, который заполняет [ `ListView` ](xref:Xamarin.Forms.ListView) с любые заметки, которые были извлечены из папки данных локального приложения. Когда [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) нажата `OnNoteAddedClicked` выполняется обработчик событий. Этот метод переходит к `NoteEntryPage`, задание [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` в новый `Note` экземпляра. При создании записи в `ListView` выбран `OnListViewItemSelected` выполняется обработчик событий. Этот метод переходит к `NoteEntryPage`, задание [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` к выбранному `Note` экземпляра. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в **NotesPage.xaml.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

13. В **панели решения**, дважды щелкните **App.xaml.cs** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Этот код добавляет объявление пространства имен для `System.IO` пространства имен и добавляет объявление статического `FolderPath` свойство типа `string`. `FolderPath` Свойство используется для сохранения пути на устройстве, где будут храниться данные Примечание. Кроме того, этот код инициализирует `FolderPath` свойство в `App` конструктор и инициализирует [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойства [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) , на котором размещена экземпляр `NotesPage`. Дополнительные сведения о навигации, см. в разделе [навигации](deepdive.md#navigation) в [подробный обзор быстрого запуска Xamarin.Forms](deepdive.md).

    Сохраните изменения в файле **App.xaml.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

14. В **панели решения**в **заметки** щелкните правой кнопкой мыши проект **MainPage.xaml**и выберите **удалить**. В диалоговом окне, появившемся press **удалить** кнопку, чтобы удалить файл с жесткого диска.

    При этом удаляются страницу, которая больше не используется.

15. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. После сохранении заметки приложение, произойдет переход обратно к **NotesPage**.

    Введите число заметки различного размера, чтобы наблюдать за поведением приложения.

::: zone-end

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы узнали, как:

- Добавление дополнительных страниц в решение Xamarin.Forms.
- Выполните переход между страницами.
- Используйте привязку данных для синхронизации данных между элементами пользовательского интерфейса и источник данных.

Чтобы изменить приложение таким образом, чтобы он хранит данные в локальную базу данных для SQLite.NET, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Вперед](database.md)

## <a name="related-links"></a>Связанные ссылки

- [Заметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Краткое руководство по Xamarin.Forms глубокое погружение в обработку](deepdive.md)
