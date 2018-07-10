---
title: Настройка ViewCell
description: Xamarin.Forms ViewCell представляет собой ячейку, который может быть добавлен к ListView или TableView, который содержит представления, определяемые разработчиком. В этой статье показано, как создать пользовательское средство отрисовки для ViewCell, который размещается в элементе управления ListView Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 3cb4d7f152e0f9540275f12f0ade568cd0552784
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935580"
---
# <a name="customizing-a-viewcell"></a>Настройка ViewCell

_Xamarin.Forms ViewCell представляет собой ячейку, который может быть добавлен к ListView или TableView, который содержит представления, определяемые разработчиком. В этой статье показано, как создать пользовательское средство отрисовки для ViewCell, который размещается в элементе управления ListView Xamarin.Forms. Это предотвратит вычисления макета Xamarin.Forms не многократно вызывается во время прокрутки ListView._

Каждая ячейка Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) отображается в приложении Xamarin.Forms в iOS `ViewCellRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `UITableViewCell` элемента управления. На платформе Android `ViewCellRenderer` класс создает экземпляр собственного `View` элемента управления. В универсальной платформы Windows (UWP), `ViewCellRenderer` класс создает экземпляр собственного `DataTemplate`. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) и соответствующие собственные элементы управления, которые реализуют его:

![](viewcell-images/viewcell-classes.png "Связь между элементом управления ViewCell и реализации собственных элементов управления")

Процесс подготовки отчета можно задействуем преимущества реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Cell) настраиваемой ячейки Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Cell) пользовательской ячейки с Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательского средства визуализации для ячейки на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации `NativeCell` модуль подготовки, который использует преимущества платформы макета для каждой ячейки в Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) элемента управления. Это предотвратит вычисления макета Xamarin.Forms многократно вызова во время `ListView` прокрутка.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Создание пользовательской ячейки

Элемент управления пользовательской ячейки могут создаваться путем создания подклассов [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) класса, как показано в следующем примере кода:

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
`NativeCell` Класс создается в проекте библиотеки .NET Standard и определяет API для пользовательской ячейки. Предоставляет пользовательской ячейки `Name`, `Category`, и `ImageFilename` свойства, которые могут отображаться посредством привязки данных. Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Использование пользовательской ячейки

`NativeCell` Пользовательской ячейки можно ссылаться в Xaml в проекте библиотеки .NET Standard, объявление пространства имен для его расположения и используя префикс пространства имен в элементе пользовательской ячейки. В следующем примере кода показано как `NativeCell` пользовательской ячейки может быть поглощен страницу XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны соответствовать данные пользовательского элемента управления. После объявления пространства имен, префикс используется для ссылки на пользовательской ячейки.

В следующем примере кода показано как `NativeCell` пользовательской ячейки могут использоваться страницей C#:

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) элемент управления используется для отображения списка данных, который заполняется с помощью [ `ItemSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemsSource/) свойство. [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) Стратегию кэширования пытается свести к минимуму `ListView` объем занимаемой памяти и выполнение ускорить, повторное использование ячеек списка. Дополнительные сведения см. в разделе [стратегии кэширования](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Каждая строка в списке содержит три элемента данных — имя, категорию и имя файла изображения. Макет каждой строки в списке определяется `DataTemplate` , можно только через [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemTemplate/) может быть привязано. `DataTemplate` Определяет, что каждая строка данных в списке будет `NativeCell` , отображающий его `Name`, `Category`, и `ImageFilename` свойства посредством привязки данных. Дополнительные сведения о `ListView` управления, см. в разделе [ListView](~/xamarin-forms/user-interface/listview/index.md).

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для настройки макета платформы для каждой ячейки.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `ViewCellRenderer` класс, который выполняет визуализацию пользовательской ячейки.
1. Переопределение метода платформы, который отображает пользовательской ячейки и логику приложения для его настройки.
1. Добавление `ExportRenderer` атрибут в класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для отрисовки пользовательской ячейки Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Для большинства элементов Xamarin.Forms является необязательным для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса элемента управления. Однако пользовательские модули подготовки отчетов необходимы в каждом проекте платформы при отрисовке [ViewCell](xref:Xamarin.Forms.ViewCell) элемент.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](viewcell-images/solution-structure.png "Обязанности проекта NativeCell пользовательский модуль подготовки отчетов")

`NativeCell` Пользовательской ячейки отображается классами платформы модуля подготовки отчетов, которые являются производными от `ViewCellRenderer` класс для каждой платформы. Это приводит к каждой `NativeCell` пользовательских преобразуемая ячейка с макетом для конкретных платформ, как показано на следующем снимке экрана:

![](viewcell-images/screenshots.png "NativeCell на каждой платформе")

`ViewCellRenderer` Класс предоставляет методы платформой для отрисовки пользовательской ячейки. Это `GetCell` метод на платформе iOS `GetCellCore` метод на платформе Android и `GetTemplate` метод для универсальной платформы Windows.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя типа ячейки Xamarin.Forms, визуализация которой выполняется и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются реализацию каждого класса пользовательского средства визуализации для конкретной платформы.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCell` Метод вызывается для создания каждой ячейки для отображения. Каждая ячейка является `NativeiOSCell` экземпляр, который определяет макет элемента ячейки и ее данных. Операция `GetCell` метод зависит от [ `ListView` ](xref:Xamarin.Forms.ListView) стратегию кэширования:

- Когда [ `ListView` ](xref:Xamarin.Forms.ListView) стратегию кэширования является [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), `GetCell` метод будет вызываться для каждой ячейки. Объект `NativeiOSCell` будет создан экземпляр для каждого `NativeCell` экземпляр, который отображается на экране. Как пользователь прокручивает `ListView`, `NativeiOSCell` экземпляры будут использовать повторно. Дополнительные сведения о ячейке iOS повторного использования, см. в разделе [повторное использование ячеек](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Этот код пользовательского средства визуализации выполнит некоторые ячейки повторного использования, даже когда [ `ListView` ](xref:Xamarin.Forms.ListView) установлено сохранение ячеек.

  Данные, отображаемые каждым `NativeiOSCell` экземпляра, созданных или повторно используются, будут ли обновлены с данными из каждого `NativeCell` экземпляра с `UpdateCell` метод.

  > [!NOTE]
  > `OnNativeCellPropertyChanged` Метод никогда не будет вызываться при [ `ListView` ](xref:Xamarin.Forms.ListView) стратегию кэширования установлено сохранение ячеек.

- Когда [ `ListView` ](xref:Xamarin.Forms.ListView) стратегию кэширования является [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), `GetCell` метод будет вызываться для каждой ячейки, которое изначально отображается на экране. Объект `NativeiOSCell` будет создан экземпляр для каждого `NativeCell` экземпляр, который отображается на экране. Данные, отображаемые каждым `NativeiOSCell` экземпляры будут обновляться данными из `NativeCell` экземпляра с `UpdateCell` метод. Тем не менее `GetCell` не будет вызван метод как прокручивая `ListView`. Вместо этого `NativeiOSCell` экземпляры будут использовать повторно. `PropertyChanged` события вызываются на `NativeCell` экземпляра при изменении данных и `OnNativeCellPropertyChanged` обработчик событий будет обновлять данные в каждом повторно использоваться `NativeiOSCell` экземпляра.

В следующем коде показано в примере `OnNativeCellPropertyChanged` метод, который вызывается, когда `PropertyChanged` события:

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Этот метод обновляет данных повторно используется `NativeiOSCell` экземпляров. Для свойства, которое изменяется проверяется, так как метод может вызываться несколько раз.

`NativeiOSCell` Класс определяет макет для каждой ячейки и показано в следующем примере кода:

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Этот класс определяет элементы управления, используемые для отображения содержимого ячейки и их размещения. Этот класс реализует [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) интерфейс, который является обязательным, если [ `ListView` ](xref:Xamarin.Forms.ListView) использует [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) стратегию кэширования. Этот интерфейс указывает, что класс должен реализовывать [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) свойство, которое должен вернуть данным пользовательских ячеек для очистки ячейки.

`NativeiOSCell` Конструктор инициализирует вида `HeadingLabel`, `SubheadingLabel`, и `CellImageView` свойства. Эти свойства используются для отображения данных, хранящихся в `NativeCell` экземпляра, с помощью `UpdateCell` метод, вызываемый для задания значения каждого свойства. Кроме того, когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) использует [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) стратегию, данных, отображаемых объектом кэширования `HeadingLabel`, `SubheadingLabel`, и `CellImageView` свойства могут быть обновление с `OnNativeCellPropertyChanged` метод в пользовательское средство отрисовки.

Макет ячейки осуществляется `LayoutSubviews` переопределить, который задает координаты `HeadingLabel`, `SubheadingLabel`, и `CellImageView` в ячейке.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCellCore` Метод вызывается для создания каждой ячейки для отображения. Каждая ячейка является `NativeAndroidCell` экземпляр, который определяет макет элемента ячейки и ее данных. Операция `GetCellCore` метод зависит от [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) стратегию кэширования:

- Когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) стратегию кэширования является [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), `GetCellCore` метод будет вызываться для каждой ячейки. Объект `NativeAndroidCell` будут создаваться для каждого `NativeCell` экземпляр, который отображается на экране. Как пользователь прокручивает `ListView`, `NativeAndroidCell` экземпляры будут использовать повторно. Дополнительные сведения о Android ячейки повторного использования, см. в разделе [повторное использование представления строк](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Обратите внимание, что этот код пользовательское средство отрисовки выполнит некоторые ячейки повторного использования, даже когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) установлено сохранение ячеек.

  Данные, отображаемые каждым `NativeAndroidCell` экземпляра, созданных или повторно используются, будут ли обновлены с данными из каждого `NativeCell` экземпляра с `UpdateCell` метод.

  > [!NOTE]
  > Обратите внимание, что, хотя `OnNativeCellPropertyChanged` метод будет вызываться при [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) является значение сохранить ячеек, не будет обновлен `NativeAndroidCell` значения свойств.

- Когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) стратегию кэширования является [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), `GetCellCore` метод будет вызываться для каждой ячейки, которое изначально отображается на экране. Объект `NativeAndroidCell` будет создан экземпляр для каждого `NativeCell` экземпляр, который отображается на экране. Данные, отображаемые каждым `NativeAndroidCell` экземпляры будут обновляться данными из `NativeCell` экземпляра с `UpdateCell` метод. Тем не менее `GetCellCore` не будет вызван метод как прокручивая `ListView`. Вместо этого `NativeAndroidCell` экземпляры будут использовать повторно.  `PropertyChanged` события вызываются на `NativeCell` экземпляра при изменении данных и `OnNativeCellPropertyChanged` обработчик событий будет обновлять данные в каждом повторно использоваться `NativeAndroidCell` экземпляра.

В следующем коде показано в примере `OnNativeCellPropertyChanged` метод, который вызывается, когда `PropertyChanged` события:

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Этот метод обновляет данных повторно используется `NativeAndroidCell` экземпляров. Для свойства, которое изменяется проверяется, так как метод может вызываться несколько раз.

`NativeAndroidCell` Класс определяет макет для каждой ячейки и показано в следующем примере кода:

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Этот класс определяет элементы управления, используемые для отображения содержимого ячейки и их размещения. Этот класс реализует [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) интерфейс, который является обязательным, если [ `ListView` ](xref:Xamarin.Forms.ListView) использует [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) стратегию кэширования. Этот интерфейс указывает, что класс должен реализовывать [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) свойство, которое должен вернуть данным пользовательских ячеек для очистки ячейки.

`NativeAndroidCell` Увеличивает конструктор `NativeAndroidCell` макета и инициализирует `HeadingTextView`, `SubheadingTextView`, и `ImageView` свойства в элементы управления в увеличенную макета. Эти свойства используются для отображения данных, хранящихся в `NativeCell` экземпляра, с помощью `UpdateCell` метод, вызываемый для задания значения каждого свойства. Кроме того, когда [ `ListView` ](xref:Xamarin.Forms.ListView) использует [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) стратегию, данных, отображаемых объектом кэширования `HeadingTextView`, `SubheadingTextView`, и `ImageView` свойства могут быть обновление с `OnNativeCellPropertyChanged` метод в пользовательское средство отрисовки.

В следующем примере кода показано определение макета для `NativeAndroidCell.axml` файл макета:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Для этой структуры указывает, что два `TextView` элементов управления и `ImageView` управления используются для отображения содержимого ячейки. Два `TextView` элементы управления являются вертикально в `LinearLayout` управление с помощью всех элементов управления, которые включены в `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского средства визуализации на UWP

В следующем примере кода показано пользовательское средство отрисовки для универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

`GetTemplate` Метод вызывается для возвращения ячейку для отображения для каждой строки данных в списке. Он создает `DataTemplate` для каждого `NativeCell` экземпляр, который будет отображаться на экране с `DataTemplate` определения внешнего вида и содержимым ячейки.

`DataTemplate` Хранятся в словаре ресурсов на уровне приложения и показано в следующем примере кода:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

`DataTemplate` Определяет элементы управления, используемый для отображения содержимого ячейки и их структуру и внешний вид. Два `TextBlock` элементов управления и `Image` управления используются для отображения содержимого ячейки посредством привязки данных. Кроме того, экземпляр `ConcatImageExtensionConverter` используется для сцепления `.jpg` расширением для каждого имени файла изображения. Это гарантирует, что `Image` управления можно загрузить и отобразить изображение в том случае, если это `Source` свойству.

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательское средство отрисовки для [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) , размещенного в Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) элемента управления. Это предотвратит вычисления макета Xamarin.Forms многократно вызова во время `ListView` прокрутка.


## <a name="related-links"></a>Связанные ссылки

- [Производительность элемента управления ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
