---
title: Store данных в локальную базу данных для SQLite.NET
description: В этой статье объясняется, как сохранить данные в локальную базу данных для SQLite.NET.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 3cea41aa3c021dbb03f851a4deb443ee86fcad25
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293366"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Store данных в локальную базу данных для SQLite.NET

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

В этом кратком руководстве вы узнаете, как:

- Используйте диспетчер пакетов NuGet для добавления пакета NuGet в проект.
- Store данных локально в базе данных для SQLite.NET.

Кратком руководстве показано, как сохранять данные в локальную базу данных для SQLite.NET. Ниже показано итоговое приложение:

[![](database-images/screenshots1-sml.png "Страницы заметок")](database-images/screenshots1.png#lightbox "страницы заметок")
[![](database-images/screenshots2-sml.png "Обратите внимание, первая страница")](database-images/screenshots2.png#lightbox "Примечание Страница входа")

### <a name="prerequisites"></a>Предварительные требования

При успешном выполнении [предыдущего краткого руководства](multi-page.md) перед попыткой в этом кратком руководстве. Кроме того, скачать [предыдущих примеров](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/) и использовать его в качестве отправной точки для этого краткого руководства.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Обновление приложения с помощью Visual Studio

1. Запустите Visual Studio и откройте решение заметки.

2. В **обозревателе решений**выберите **заметки** проекта, щелкните правой кнопкой мыши и выберите **управление пакетами NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Добавление пакетов NuGet")    

3. В **диспетчер пакетов NuGet**выберите **Обзор** вкладке, поиск **sqlite-net-pcl** пакет NuGet, выберите его и нажмите кнопку **установить**кнопку, чтобы добавить его в проект:

    ![](database-images/vs/add-package.png "Добавление пакета")

    > [!NOTE]
    > Существует ряд пакетов NuGet с одинаковыми именами. Правильный пакет имеет следующие атрибуты:
    > - **Авторов:** Фрэнк А. Крюгер (Frank A. Krueger)
    > - **Идентификатор:** sqlite-net-pcl
    > - **Ссылка в NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Несмотря на название пакета можно использовать этот пакет NuGet в проектах .NET Standard.

    Этот пакет будет использоваться для включения операций базы данных в приложение.

4. В **обозревателе решений**в **заметки** откройте проект **Note.cs** в **моделей** папку и замените имеющийся код с Следующий код:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Этот класс определяет `Note` модели, в которой будут храниться данные о каждом Примечание в приложении. `ID` Свойство помечено атрибутом `PrimaryKey` и `AutoIncrement` атрибуты, чтобы убедиться, что каждый `Note` экземпляр базы данных для SQLite.NET будет иметь уникальный идентификатор, предоставляемый для SQLite.NET.

    Сохраните изменения в **Note.cs** , нажав клавишу **CTRL + S**и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

5. В **обозревателе решений**, добавьте новую папку с именем **данных** для **заметки** проекта.

6. В **обозревателе решений**в **заметки** проект, добавить новый класс с именем **NoteDatabase** для **данных** папки.

7. В **NoteDatabase.cs**, замените существующий код следующим кодом:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Этот класс содержит код, чтобы создать базу данных, чтение данных из него, записать на него данные и удалять данные из нее. Код использует асинхронные интерфейсы API для SQLite.NET, перемещать операции базы данных в фоновых потоках. Кроме того `NoteDatabase` конструктор принимает путь к файлу базы данных в качестве аргумента. Этот путь будет предоставляться системой `App` класс в следующем шаге.

    Сохраните изменения в **NoteDatabase.cs** , нажав клавишу **CTRL + S**и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

8. В **обозревателе решений**в **заметки** проекта, дважды щелкните **App.xaml.cs** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;
    using Notes.Data;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Этот код определяет `Database` свойство, которое создает новый `NoteDatabase` экземпляру, что является одноэлементным множеством, передав имя файла базы данных в качестве аргумента для `NoteDatabase` конструктор. Преимущество использования отдельной базы данных в том, что создается отдельное подключение к базе данных, которое остается открытым, пока работает приложение. Это позволяет избежать затрат, связанных с открытием и закрытием файла базы данных каждый раз, когда выполняется операция с ней.

    Сохраните изменения в файле **App.xaml.cs**, нажав клавиши **CTRL+S**, и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

9. В **обозревателе решений**в **заметки** проекта, дважды щелкните **NotesPage.xaml.cs** чтобы открыть его. Затем замените `OnAppearing` метод следующим кодом:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Этот код заполняет [ `ListView` ](xref:Xamarin.Forms.ListView) с примечаниями, хранящиеся в базе данных.

    Сохраните изменения в **NotesPage.xaml.cs** , нажав клавишу **CTRL + S**и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

10. В **обозревателе решений**, дважды щелкните **NoteEntryPage.xaml.cs** чтобы открыть его. Затем замените `OnSaveButtonClicked` и `OnDeleteButtonClicked` методы следующим кодом:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` Хранит `Note` экземпляр, который представляет отдельного комментария в [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) страницы. Когда `OnSaveButtonClicked` выполняется обработчик событий, `Note` экземпляр сохраняется в базу данных, и приложение переходит назад к предыдущей странице. Когда `OnDeleteButtonClicked` выполняется обработчик событий, `Note` экземпляр удаляется из базы данных, и приложение переходит назад к предыдущей странице.

      Сохраните изменения в **NoteEntryPage.xaml.cs** , нажав клавишу **CTRL + S**и закройте файл.

11. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. После сохранении заметки приложение, произойдет переход обратно к **NotesPage**.

    Введите число заметки различного размера, чтобы наблюдать за поведением приложения.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Обновление приложения с помощью Visual Studio для Mac

1. Запустите Visual Studio для Mac и открыть проект заметки.

2. В **панели решения**выберите **заметки** проекта, щелкните правой кнопкой мыши и выберите **Добавить > Добавить пакеты NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Добавление пакетов NuGet")    

3. В **добавить пакеты** окно, и выполните поиск **sqlite-net-pcl** пакет NuGet, выберите его и нажмите кнопку **Add Package** кнопку, чтобы добавить его в проект:

    ![](database-images/vsmac/add-package.png "Добавление пакета")

    > [!NOTE]
    > Существует ряд пакетов NuGet с одинаковыми именами. Правильный пакет имеет следующие атрибуты:
    > - **Автор:** Фрэнк А. Крюгер (Frank A. Krueger)
    > - **Идентификатор:** sqlite-net-pcl
    > - **Ссылка в NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Несмотря на название пакета можно использовать этот пакет NuGet в проектах .NET Standard.

    Этот пакет будет использоваться для включения операций базы данных в приложение.

4. В **панели решения**в **заметки** откройте проект **Note.cs** в **моделей** папку и замените существующий код приведенным ниже код:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Этот класс определяет `Note` модели, в которой будут храниться данные о каждом Примечание в приложении. `ID` Свойство помечено атрибутом `PrimaryKey` и `AutoIncrement` атрибуты, чтобы убедиться, что каждый `Note` экземпляр базы данных для SQLite.NET будет иметь уникальный идентификатор, предоставляемый для SQLite.NET.

    Сохраните изменения в **Note.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

5. В **панели решения**, добавьте новую папку с именем **данных** для **заметки** проекта.

6. В **панели решения**в **заметки** проект, добавить новый класс с именем **NoteDatabase** для **данных** папки.

7. В **NoteDatabase.cs**, замените существующий код следующим кодом:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Этот класс содержит код, чтобы создать базу данных, чтение данных из него, записать на него данные и удалять данные из нее. Код использует асинхронные интерфейсы API для SQLite.NET, перемещать операции базы данных в фоновых потоках. Кроме того `NoteDatabase` конструктор принимает путь к файлу базы данных в качестве аргумента. Этот путь будет предоставляться системой `App` класс в следующем шаге.

    Сохраните изменения в **NoteDatabase.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

8. В **панели решения**в **заметки** проекта, дважды щелкните **App.xaml.cs** чтобы открыть его. Затем замените существующий код следующим кодом:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;
    using Notes.Data;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }
            ...
        }
    }
    ```

    Этот код определяет `Database` свойство, которое создает новый `NoteDatabase` экземпляру, что является одноэлементным множеством, передав имя файла базы данных в качестве аргумента для `NoteDatabase` конструктор. Преимущество использования отдельной базы данных в том, что создается отдельное подключение к базе данных, которое остается открытым, пока работает приложение. Это позволяет избежать затрат, связанных с открытием и закрытием файла базы данных каждый раз, когда выполняется операция с ней.

    Сохраните изменения в файле **App.xaml.cs**, выбрав **Файл > Сохранить** или нажав клавиши **&#8984;+S**, и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

9. В **панели решения**в **заметки** проекта, дважды щелкните **NotesPage.xaml.cs** чтобы открыть его. Затем замените `OnAppearing` метод следующим кодом:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Этот код заполняет [ `ListView` ](xref:Xamarin.Forms.ListView) с примечаниями, хранящиеся в базе данных.

    Сохраните изменения в **NotesPage.xaml.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

    > [!WARNING]
    > Попытка выполнить сборку приложения на этом этапе приведет к ошибкам, будут устранены в последующих шагах.

10. В **панели решения**, дважды щелкните **NoteEntryPage.xaml.cs** чтобы открыть его. Затем замените `OnSaveButtonClicked` и `OnDeleteButtonClicked` методы следующим кодом:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage` Хранит `Note` экземпляр, который представляет отдельного комментария в [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) страницы. Когда `OnSaveButtonClicked` выполняется обработчик событий, `Note` экземпляр сохраняется в базу данных, и приложение переходит назад к предыдущей странице. Когда `OnDeleteButtonClicked` выполняется обработчик событий, `Note` экземпляр удаляется из базы данных, и приложение переходит назад к предыдущей странице.

      Сохраните изменения в **NoteEntryPage.xaml.cs** , выбрав **файл > Сохранить** (или нажав  **&#8984; + S**) и закройте файл.

11. Постройте и запустите проект на каждой платформе. Дополнительные сведения см. в разделе [создания быстрого запуска](single-page.md#building-the-quickstart).

    На **NotesPage** клавишу **+** кнопки для перехода к **NoteEntryPage** и введите заметку. После сохранении заметки приложение, произойдет переход обратно к **NotesPage**.

    Введите число заметки различного размера, чтобы наблюдать за поведением приложения.

::: zone-end

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы узнали, как:

- Используйте диспетчер пакетов NuGet для добавления пакета NuGet в проект.
- Store данных локально в базе данных для SQLite.NET.

Чтобы приложение в стиле с помощью стилей XAML, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Вперед](styling.md)

## <a name="related-links"></a>Связанные ссылки

- [Заметки (пример)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Краткое руководство по Xamarin.Forms глубокое погружение в обработку](deepdive.md)
