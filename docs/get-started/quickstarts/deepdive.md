---
title: Краткое руководство по Xamarin.Forms глубокое погружение в обработку
description: Эта статья содержит общие сведения о разработке приложений с использованием Xamarin.Forms. Помимо прочего, здесь описываются структура приложения Xamarin.Forms и принципы его работы, а также пользовательский интерфейс.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: aacc63af53d7408b61f01054d49fa2b436588fc7
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55293347"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Краткое руководство по Xamarin.Forms глубокое погружение в обработку

В [краткое руководство по Xamarin.Forms](~/get-started/index.md), было создано приложение заметки. В этой статье выполняется проверка созданных компонентов, позволяющая получить представление об основных принципах работы приложений Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Введение в Visual Studio

Код в Visual Studio упорядочен по *решениям* и *проектам*. Решение — это контейнер для одного или нескольких проектов. Проект может представлять собой приложение, вспомогательную библиотеку, тестовое приложение и т. д. Заметки о приложение состоит из одно решение, содержащее четыре проекта, как показано на следующем снимке экрана:

![](deepdive-images/vs/solution.png "Обозреватель решений Visual Studio")

Решение содержит следующие проекты:

- Примечание – Этот проект является проекта библиотеки .NET Standard, который содержит весь общий код и общий пользовательский Интерфейс.
- Notes.Android — этот проект содержит код для Android и представляет собой точку входа для приложения Android.
- Notes.iOS — этот проект содержит код для iOS и представляет собой точку входа для приложения iOS.
- Notes.UWP — этот проект содержит код для универсальной платформы Windows (UWP) и представляет собой точку входа для приложения универсальной платформы Windows.

## <a name="anatomy-of-a-xamarinforms-application"></a>Структура приложения Xamarin.Forms

Следующем снимке экрана показано содержимое проекта библиотеки заметки о .NET Standard в Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Содержимое проекта .NET Standard Phoneword")

В этом проекте есть узел **Зависимости**, который содержит узлы **NuGet** и **SDK**:

- **NuGet** &ndash; Xamarin.Forms и пакеты NuGet sqlite-net-pcl, которые были добавлены в проект.
- **SDK** &ndash; метапакет `NETStandard.Library`, который ссылается на полный набор пакетов NuGet, определяющих библиотеку .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Введение в Visual Studio для Mac

В [Visual Studio для Mac](/visualstudio/mac/), так же как в Visual Studio, код упорядочивается по *решениям* и *проектам*. Решение — это контейнер для одного или нескольких проектов. Проект может представлять собой приложение, вспомогательную библиотеку, тестовое приложение и т. д. Заметки о приложение состоит из одно решение, содержащее три проекта, как показано на следующем снимке экрана:

![](deepdive-images/vsmac/solution.png "Область решений в Visual Studio для Mac")

Решение содержит следующие проекты:

- Примечание – Этот проект является проекта библиотеки .NET Standard, который содержит весь общий код и общий пользовательский Интерфейс.
- Notes.Android — этот проект содержит код для Android и представляет собой точку входа для приложений Android.
- Notes.iOS — этот проект содержит код-iOS и представляет собой точку входа для приложений iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Структура приложения Xamarin.Forms

Следующем снимке экрана показано содержимое проекта библиотеки заметки о .NET Standard в Visual Studio для Mac:

![](deepdive-images/vsmac/net-standard-project.png "Содержимое проекта библиотеки .NET Standard Phoneword")

В этом проекте есть узел **Зависимости**, который содержит узлы **NuGet** и **SDK**:

- **NuGet** &ndash; Xamarin.Forms и пакеты NuGet sqlite-net-pcl, которые были добавлены в проект.
- **SDK** &ndash; метапакет `NETStandard.Library`, который ссылается на полный набор пакетов NuGet, определяющих библиотеку .NET Standard.

::: zone-end

Проект также состоит из нескольких файлов:

- **Data\NoteDatabase.cs** — этот класс содержит код, чтобы создать базу данных, чтение данных из него, записать на него данные и удалять данные из нее.
- **Models\Note.cs** — этот класс определяет `Note` модели, экземпляры которого хранения данных о каждом Примечание в приложении.
- **App.xaml** — разметка XAML для класса `App`, который определяет словарь ресурсов для приложения.
- **App.xaml.cs** — код программной части для класса `App`, который обеспечивает создание экземпляра первой страницы, отображаемой приложением на каждой платформе, а также обработку событий жизненного цикла приложения.
- **NotesPage.xaml** — разметка XAML для `NotesPage` класс, который определяет пользовательский Интерфейс страницы, отображаемой при запуске приложения.
- **NotesPage.xaml.cs** — кода для `NotesPage` класс, который содержит бизнес-логики, который выполняется при взаимодействии пользователя со страницей.
- **NoteEntryPage.xaml** — разметка XAML для `NoteEntryPage` класс, который определяет пользовательский Интерфейс страницы, отображаемой, если пользователь вводит Примечание.
- **NoteEntryPage.xaml.cs** — кода для `NoteEntryPage` класс, который содержит бизнес-логики, который выполняется при взаимодействии пользователя со страницей.

Дополнительные сведения о структуре приложения Xamarin.iOS см. [здесь](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Дополнительные сведения о структуре приложения Xamarin.Android см. [здесь](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Основы архитектуры и приложения

По своей архитектуре приложения Xamarin.Forms не отличаются от традиционных кроссплатформенных приложений. Общий код обычно помещается в библиотеку .NET Standard и используется приложениями для конкретных платформ. В примере ниже показан обзор этой связи для него заметки:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Архитектура заметки")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Архитектура заметки")

::: zone-end

Чтобы код запуска как можно больше использовался повторно, в приложениях Xamarin.Forms есть отдельный класс `App`, который отвечает за создание экземпляра первой страницы, отображаемой приложением на каждой платформе, как показано в следующем примере кода:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

Этот код задает `MainPage` свойство `App` класс [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) экземпляр, содержимое которого является `NotesPage` экземпляра. Кроме того, атрибут [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) включает компилятор XAML, что позволяет компилировать код XAML непосредственно в промежуточный язык. Дополнительные сведения см. в разделе [Компиляция XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="launching-the-application-on-each-platform"></a>Запуск приложения на каждой платформе

### <a name="ios"></a>iOS

Для запуска начальной страницы Xamarin.Forms в iOS, определяет проект Notes.iOS `AppDelegate` класс, наследующий от `FormsApplicationDelegate` класса:

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

Переопределение `FinishedLaunching` инициализирует платформу Xamarin.Forms, вызывая метод `Init`. В результате реализация Xamarin.Forms для iOS загружается в приложении, прежде чем корневой контроллер представления задается путем вызова метода `LoadApplication`.

### <a name="android"></a>Android

Для запуска начальной страницы Xamarin.Forms в Android, Notes.Android проект содержит код, создающий `Activity` с `MainLauncher` атрибут, причем действие наследуется от `FormsAppCompatActivity` класса:

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

Переопределение `OnCreate` инициализирует платформу Xamarin.Forms, вызывая метод `Init`. В результате реализация Xamarin.Forms для Android загружается в приложении перед загрузкой самого приложения Xamarin.Forms.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Универсальная платформа Windows

В приложениях универсальной платформы Windows (UWP) метод `Init`, который инициализирует платформу Xamarin.Forms, вызывается из класса `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

В результате в приложении загружается реализация Xamarin.Forms для UWP. Начальная страница Xamarin.Forms запускается с `MainPage` класса:

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

Приложение Xamarin.Forms загружается с помощью метода `LoadApplication`.

> [!NOTE]
> Приложений универсальной платформы Windows можно создавать с помощью Xamarin.Forms, но только с помощью Visual Studio в Windows.

::: zone-end

## <a name="user-interface"></a>Пользовательский интерфейс

Существует четыре основные группы элементов управления для создания пользовательского интерфейса приложения Xamarin.Forms.

1. **Страницы** — страницы Xamarin.Forms представляют экраны в кроссплатформенных мобильных приложениях. Заметки о приложении используется [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) класс единый окон. Дополнительные сведения о страницах см. в статье [Страницы Xamarin.Forms](~/xamarin-forms/user-interface/controls/pages.md).
1. **Представления** — представления Xamarin.Forms являются элементами управления, отображаемыми в пользовательском интерфейсе, например метки, кнопки и поля ввода текста. Готовое приложение заметки использует [ `ListView` ](xref:Xamarin.Forms.ListView), [ `Editor` ](xref:Xamarin.Forms.Editor), и [ `Button` ](xref:Xamarin.Forms.Button) представления. Дополнительные сведения о представлениях см. в статье [Представления Xamarin.Forms](~/xamarin-forms/user-interface/controls/views.md).
1. **Макеты** — макеты Xamarin.Forms представляют собой контейнеры, которые служат для объединения представлений в логические структуры. Заметки о приложении используется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) класса для размещения представления в вертикальном столбце и [ `Grid` ](xref:Xamarin.Forms.Grid) класса, чтобы упорядочить кнопки по горизонтали. Дополнительные сведения о макетах см. в статье [Макеты Xamarin.Forms](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Ячейки** — ячейки Xamarin.Forms являются специальными объектами, представляющими элементы списка и описывающими способ их отрисовки. Заметки о приложении используется [ `TextCell` ](xref:Xamarin.Forms.TextCell) для отображения двух элементов для каждой строки в списке. Дополнительные сведения о ячейках см. в статье [Ячейки Xamarin.Forms](~/xamarin-forms/user-interface/controls/cells.md).

Во время выполнения каждый элемент управления сопоставляется с собственным аналогом, который и будет отрисовываться.

### <a name="layout"></a>Макет

Заметки о приложении используется [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) для упрощения разработки кросс платформенных приложений благодаря автоматической компоновке представления на экране независимо от размера экрана. Дочерние элементы размещаются поочередно (по горизонтали или по вертикали) в порядке добавления. Область, занимаемая макетом `StackLayout`, зависит от значений свойств [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) и [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), но по умолчанию макет `StackLayout` будет пытаться использовать весь экран.

В следующем коде XAML показан пример использования [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) макет `NoteEntryPage`:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

По умолчанию [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) предполагает вертикальную ориентацию. Тем не менее, его можно изменить на горизонтальную, задав [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) свойства [ `StackOrientation.Horizontal` ](xref:Xamarin.Forms.StackOrientation.Horizontal) член перечисления.

> [!NOTE]
> Можно задать размер представления с помощью `HeightRequest` и `WidthRequest` свойства.

Дополнительные сведения о классе [`StackLayout`](xref:Xamarin.Forms.StackLayout) см. в статье [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Реакция на действия пользователя

Объект, определенный в XAML, может инициировать событие, которое обрабатывается в файле кода программной части. В следующем коде показано в примере `OnSaveButtonClicked` метод в коде программной части для `NoteEntryPage` класс, который выполняется в ответ на [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) запуск события на *Сохранить* кнопки .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

`OnSaveButtonClicked` Метод сохраняет заметки в базе данных и переходит обратно к предыдущей странице.

> [!NOTE]
> Файл кода программной части для класса XAML может получать доступ к объекту, определенному в XAML, по имени, которое назначено ему в атрибуте `x:Name`. Значение, присваиваемое этому атрибуту, должно соответствовать правилам C# в отношении переменных, то есть должно начинаться с буквы или знака подчеркивания и не содержать внутренних пробелов.

Кнопки подключения сохранения `OnSaveButtonClicked` метода происходит в разметке XAML для `NoteEntryPage` класса:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Списки

[ `ListView` ](xref:Xamarin.Forms.ListView) Отвечает за отображение коллекции элементов по вертикали в списке. Каждый элемент в `ListView` будет содержаться в одной ячейке.

В следующем коде показано в примере [ `ListView` ](xref:Xamarin.Forms.ListView) из `NotesPage`:

```xaml
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
```

Макет каждой строки в [ `ListView` ](xref:Xamarin.Forms.ListView) определяется внутри [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) элемент и использует данные привязки для отображения любые заметки, которые извлекаются приложением. [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) Свойству источника данных, в `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Этот код заполняет [ `ListView` ](xref:Xamarin.Forms.ListView) с примечаниями, хранящиеся в базе данных.

При выборе строки в [ `ListView` ](xref:Xamarin.Forms.ListView), [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) вызывает событие. Обработчик событий с именем `OnListViewItemSelected`, выполняется при возникновении события:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) Событий доступа к объекту, который был связан с ячейкой, посредством [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) свойство.

Дополнительные сведения о [ `ListView` ](xref:Xamarin.Forms.ListView) , представлена в разделе [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Навигация

Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа объекта [`Page`](xref:Xamarin.Forms.Page). Для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) навигации экземпляров может быть иерархические или модальные. Сведения о модальной навигации см. в разделе [модальные страницы Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Классы [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) и [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) предоставляют альтернативные способы перехода. Дополнительные сведения см. в разделе [Переходы](~/xamarin-forms/app-fundamentals/navigation/index.md).

При иерархической навигации [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) класс используется для перехода по стеку объектов [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) объектов, вперед и назад по своему усмотрению. Этот класс реализует навигацию на основе стека объектов [`Page`](xref:Xamarin.Forms.Page) по методу LIFO (последним поступил — первым обслужен). Для перехода с одной страницы на другую приложение помещает новую страницу в стек навигации, где она становится активной страницей. Для возврата к предыдущей странице приложение выбирает текущую страницу из стека навигации, после чего активной становится верхняя страница в стеке.

Класс `NavigationPage` также добавляет панель навигации в верхнюю часть страницы, где отображается заголовок и соответствующая платформе кнопка **Назад**, позволяющая вернуться на предыдущую страницу.

Первая страница, добавленная в стек навигации называется *корневой* странице приложения, а также в следующем примере кода показано, как это происходит в заметки о приложении:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

У всех экземпляров [`ContentPage`](xref:Xamarin.Forms.ContentPage) есть свойство[`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation), которое предоставляет методы для изменения стека страниц. Эти методы должны вызываться только в случае, если приложение содержит класс [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Для перехода к `NoteEntryPage` необходимо вызвать метод [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)), как показано в следующем примере кода:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

В результате новый `NoteEntryPage` объект помещается в стек навигации, где он становится активной страницей.

Активная страница может быть извлечена из стека навигации путем нажатия кнопки *Назад* на устройстве, причем это может быть как физическая кнопка, так и кнопка на экране. Чтобы вернуться на исходную страницу программным образом, объект `NoteEntryPage` должен вызвать метод [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), как показано в следующем примере кода:

```csharp
await Navigation.PopAsync();
```

Дополнительные сведения об иерархической навигации см. в статье [Иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>привязка данных,

Привязка данных используется для упрощения способа, которым приложение Xamarin.Forms отображает данные и взаимодействует с ними. Она устанавливает связь между пользовательским интерфейсом и базовым приложением. Класс [`BindableObject`](xref:Xamarin.Forms.BindableObject) содержит основную часть инфраструктуры для поддержки привязки данных.

Привязка данных связывает два объекта, которые называются *источником* и *целевым объектом*. *Источник* предоставляет данные. *Целевой* объект будет использовать (и часто отображать) данные из источника. Например, свойство [`Text`](xref:Xamarin.Forms.Editor.Text) элемента [`Editor`](xref:Xamarin.Forms.Editor) (*целевого* объекта) часто связывается с открытым свойством `string` *источника*. На следующей схеме показано отношение привязки:

![](deepdive-images/data-binding.png "Привязка данных")

Главным преимуществом привязки данных является то, что вам больше не нужно беспокоиться о синхронизации данных между представлениями и источником данных. Изменения в *источнике* автоматически передаются в *целевой* объект платформой привязки. При необходимости изменения в целевом объекте также могут передаваться в *источник*.

Установление привязки данных состоит из двух этапов:

- Свойству [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) *целевого* объекта должен быть присвоен *источник*.
- Между *целевым* объектом и *источником* должна быть установлена привязка. В XAML для этого используется расширение разметки [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

В приложении заметки, целевым объектом привязки является [ `Editor` ](xref:Xamarin.Forms.Editor) , выводится сообщение, пока `Note` как набор экземпляров [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` является привязка источник.

`BindingContext` Из `NoteEntryPage` устанавливается во время навигации по страницам, как показано в следующем примере кода:

```csharp
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
```

В `OnNoteAddedClicked` метод, который выполняется при добавлении новой заметки к приложению, [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из `NoteEntryPage` присваивается новый `Note` экземпляра. В `OnListViewItemSelected` метод, который выполняется при выборе существующей заметки в [ `ListView` ](xref:Xamarin.Forms.ListView), `BindingContext` из `NoteEntryPage` набор к выбранному `Note` экземпляра, доступном через [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) свойство.

> [!IMPORTANT]
> Хотя свойство [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) каждого *целевого* объекта можно задавать по отдельности, это не является обязательным. `BindingContext` — это особое свойство, которое наследуется всеми дочерними объектами. Таким образом, когда `BindingContext` на [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) присваивается `Note` экземпляра, все дочерние элементы `ContentPage` имеют одинаковые `BindingContext`и можно привязать к открытым свойствам объекта `Note`объекта.

[ `Editor` ](xref:Xamarin.Forms.Editor) В `NoteEntryPage` затем привязывается к `Text` свойство `Note` объекта:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Создается привязка между свойством [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) и свойством `Text` *источника*. Изменения, внесенные в `Editor` будут автоматически распространены `Note` объекта. Аналогично Если изменения вносятся `Note.Text` , подсистема привязки Xamarin.Forms будет также обновлять свойство содержимое `Editor`. Это называется *двусторонней привязкой*.

Дополнительные сведения о привязке данных см. в разделе [привязки данных Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Задание стиля

Приложения Xamarin.Forms часто содержат несколько визуальных элементов, имеющих идентичным внешним видом. Настройка внешнего вида каждый визуальный элемент может быть повторяющихся и подвержено ошибкам. Вместо этого могут быть созданы стили, которые определяют внешний, а затем применяются к необходимые визуальные элементы.

[ `Style` ](xref:Xamarin.Forms.Style) Класс группирует коллекцию значений свойств в один объект, который затем можно применить к нескольким экземплярам визуальный элемент. Стили хранятся в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), либо на уровне приложения, странице или представлении. Выбор места для определения `Style` последствий, где его можно использовать:

- [`Style`](xref:Xamarin.Forms.Style) экземпляры, определенные на уровне приложения можно применять в приложении.
- [`Style`](xref:Xamarin.Forms.Style) экземпляры, определенные на уровне страницы могут применяться к странице и с его дочерними элементами.
- [`Style`](xref:Xamarin.Forms.Style) экземпляры, определенных на уровне представления могут применяться к представлению и с его дочерними элементами.

> [!IMPORTANT]
> Все стили, которые используются в приложении, хранятся в словаре ресурсов приложения, чтобы избежать дублирования. Тем не менее XAML, который относится к странице не должно быть включено в словаре ресурсов приложения, как ресурсы, затем можно проанализировать при запуске приложения, а не требуется для страницы.

Каждый [ `Style` ](xref:Xamarin.Forms.Style) экземпляр содержит коллекцию из одного или нескольких [ `Setter` ](xref:Xamarin.Forms.Setter) объектов, каждый `Setter` необходимости [ `Property` ](xref:Xamarin.Forms.Setter.Property) и [`Value`](xref:Xamarin.Forms.Setter.Value). `Property` Имя привязываемые свойства элемента применяется стиль, и `Value` является значением, которое применяется к свойству. В следующем примере кода показано стиля из `NoteEntryPage`:

```xaml
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
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

Этот стиль применяется к любому [ `Editor` ](xref:Xamarin.Forms.Editor) экземпляров на странице.

При создании [ `Style` ](xref:Xamarin.Forms.Style), [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) свойство всегда является обязательным.

> [!NOTE]
> Задание стиля приложений Xamarin.Forms, обычно осуществляется с помощью стилей XAML. Тем не менее Xamarin.Forms также поддерживает стили визуальных элементов с помощью каскадных таблиц стилей (CSS). Дополнительные сведения см. в разделе [приложений стиля Xamarin.Forms с помощью каскадных таблиц стилей (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Дополнительные сведения о стилях XAML см. в разделе [Задание стиля приложений Xamarin.Forms с помощью стилей XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Предоставляя стили для конкретных платформ

`OnPlatform` Расширения разметки позволяют настраивать внешний вид пользовательского интерфейса на каждой платформы:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

Это [ `Style` ](xref:Xamarin.Forms.Style) задает разные [ `Color` ](xref:Xamarin.Forms.Color) значений в параметре [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) и [ `BarTextColor` ](xref:Xamarin.Forms.NavigationPage.BarTextColor) свойства [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), в зависимости от используемой платформы.

Дополнительные сведения о расширениях разметки XAML см. в статье [Расширения разметки XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Сведения о `OnPlatform` расширения разметки, см. в разделе [расширение разметки OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Тестирование и развертывание

Как Visual Studio для Mac, так и Visual Studio предоставляют множество возможностей для тестирования и развертывания приложения. Одной из неотъемлемых стадий жизненного цикла приложения является отладка, которая позволяет выявить проблемы в коде. Дополнительные сведения см. в разделах [Установка точки останова](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Пошаговая отладка кода](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) и [Вывод данных в окно журнала](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Симуляторы позволяют начать тестирование и развертывание приложения, реализуя полезные функции для тестирования приложений. Тем не менее пользователи будут работать с готовым приложением не в симуляторе, поэтому любое приложение необходимо тестировать на реальных устройствах как можно раньше и чаще. Дополнительные сведения о процессе подготовки для устройств iOS см. в разделе [Подготовка устройства](~/ios/get-started/installation/device-provisioning/index.md). Дополнительные сведения о процессе подготовки для устройств Android см. в разделе [Настройка устройства для разработки](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Следующие шаги

Этот видеокурс определил основ разработки приложений с помощью Xamarin.Forms. Далее рекомендуется ознакомиться с перечисленными ниже функциональными возможностями.

- Для создания пользовательского интерфейса приложения Xamarin.Forms используются четыре основные группы элементов управления. Дополнительные сведения см. в разделе [Справочник по элементам управления](~/xamarin-forms/user-interface/controls/index.md).
- Привязка данных — это метод связывания свойства двух объектов, чтобы изменения в одном свойстве автоматически отражаются в другое свойство. Дополнительные сведения см. в разделе [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа страницы. Дополнительные сведения см. в разделе [Переходы](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Стили помогают снизить повтора разметки и разрешить внешний вид приложения, проще изменять. Дополнительные сведения см. в разделе [Задание стиля приложений Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Расширения разметки XAML расширить функциональность и гибкость XAML, позволяя атрибутов элемента из источников, отличных от литеральные текстовые строки. Дополнительные сведения см. в разделе [расширения разметки XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Шаблоны данных позволяют настраивать представление данных на поддерживаемых представлений. Дополнительные сведения см. в статье [Шаблоны данных](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Каждой страницы, макет и вид преобразуется для просмотра по-разному в разных платформах с помощью `Renderer` класс, который в свою очередь создает собственный элемент управления, размещает его на экране и реализует поведение, определенное в общем коде. Разработчики могут реализовывать пользовательские классы `Renderer` для настройки внешнего вида или работы элемента управления. Дополнительные сведения см. в статье [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Эффекты также позволяют настраивать собственные элементы управления на каждой платформе. Эффекты создаются в проекты под конкретные платформы путем создания подклассов [ `PlatformEffect` ](xref:Xamarin.Forms.PlatformEffect`2) класса и используются путем их присоединения к соответствующему элементу управления Xamarin.Forms. Дополнительные сведения см. в статье [Эффекты](~/xamarin-forms/app-fundamentals/effects/index.md).
- Общий код может получать доступ к собственным функциональным возможностям посредством класса [`DependencyService`](xref:Xamarin.Forms.DependencyService). Дополнительные сведения см. в статье, посвященной [доступу к собственным функциональным возможностям с помощью DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Много полезной информации о платформе Xamarin.Forms можно почерпнуть из книги Чарльза Петцольда (Charles Petzold) [_Creating Mobile Apps with Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Создание мобильных приложений с помощью Xamarin.Forms). Книга доступна в формате PDF, а также в нескольких форматах электронных книг.

## <a name="related-links"></a>Связанные ссылки

- [Расширяемый язык разметки приложений (XAML)](~/xamarin-forms/xaml/index.md)
- [Привязка данных](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Справочник по элементам управления](~/xamarin-forms/user-interface/controls/index.md)
- [Расширения разметки XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Примеры для начала работы](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/)
- [Справочник по API для Xamarin.Forms](xref:Xamarin.Forms)
- [Бесплатное самостоятельное обучение (видео)](https://university.xamarin.com/self-guided/)
