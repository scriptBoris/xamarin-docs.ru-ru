---
title: Настройка ListView
description: ListView Xamarin.Forms — это представление, которое отображает набор данных в виде списка по вертикали. В этой статье показано, как создать пользовательское средство отрисовки, инкапсулирующий специфические для платформы список элементов управления и макеты собственного ячейки, позволяя больший контроль над производительностью собственного списка элемента управления.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: b3b73d542faebdb8ab85c989d7812368f4f3ffac
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997495"
---
# <a name="customizing-a-listview"></a>Настройка ListView

_ListView Xamarin.Forms — это представление, которое отображает набор данных в виде списка по вертикали. В этой статье показано, как создать пользовательское средство отрисовки, инкапсулирующий специфические для платформы список элементов управления и макеты собственного ячейки, позволяя больший контроль над производительностью собственного списка элемента управления._

Каждое представление Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `ListView` ](xref:Xamarin.Forms.ListView) отображается в приложении Xamarin.Forms в iOS `ListViewRenderer` создается экземпляр класса, который в свою очередь создает экземпляр собственного `UITableView` элемента управления. На платформе Android `ListViewRenderer` класс создает экземпляр собственного `ListView` элемента управления. В универсальной платформы Windows (UWP), `ListViewRenderer` класс создает экземпляр собственного `ListView` элемента управления. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `ListView` ](xref:Xamarin.Forms.ListView) управления и соответствующие собственные элементы управления, которые реализуют его:

![](listview-images/listview-classes.png "Связь между элементом управления ListView и реализации собственных элементов управления")

Процесс подготовки отчета можно задействуем преимущества реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `ListView` ](xref:Xamarin.Forms.ListView) на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_ListView_Control) пользовательского элемента управления Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Control) пользовательского элемента управления с Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательское средство отрисовки для элемента управления на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации `NativeListView` модуля подготовки отчетов, в котором используются преимущества платформы список элементов управления и макеты собственного ячейки. Этот сценарий полезен при переносе существующего собственного приложения, содержащий список и ячейки кода, который можно использовать повторно. Кроме того он предоставляет подробные настройки списка возможностей элементов управления, которые могут повлиять на производительность, например виртуализации данных.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Создание элемента управления ListView по пользовательских

Пользовательский [ `ListView` ](xref:Xamarin.Forms.ListView) элемент управления может создаваться путем создания подклассов `ListView` класса, как показано в следующем примере кода:

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

`NativeListView` Создается в проекте библиотеки .NET Standard и определяет API для пользовательского элемента управления. Этот элемент управления предоставляет `Items` свойство, используемое для заполнения `ListView` с данными, и который может быть привязан к для данных в целях отображения. Он также предоставляет `ItemSelected` событие, которое будет запускаться каждый раз, когда элемент выбран в элементе управления собственного списка конкретных платформ. Дополнительные сведения о привязке данных см. в статье [Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Использование пользовательского элемента управления

`NativeListView` Пользовательского элемента управления можно ссылаться в Xaml в проекте библиотеки .NET Standard, объявление пространства имен для его расположения и используя префикс пространства имен в элементе управления. В следующем примере кода показано как `NativeListView` пользовательский элемент управления может быть поглощен страницу XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*" />
            </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
            <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
          </Grid>
      </ContentPage.Content>
</ContentPage>
```

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны соответствовать данные пользовательского элемента управления. После объявления пространства имен, префикс используется для ссылки на пользовательский элемент управления.

В следующем примере кода показано как `NativeListView` пользовательский элемент управления может быть поглощен страницы C#:

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

`NativeListView` Пользовательский элемент управления использует пользовательские отрисовщики платформы для отображения списка данных, который заполняется с помощью `Items` свойство. Каждая строка в списке содержит три элемента данных — имя, категорию и имя файла изображения. Макет каждой строки в списке определяется пользовательское средство отрисовки конкретной платформы.

> [!NOTE]
> Так как `NativeListView` пользовательский элемент управления отрисовывается с помощью элементов управления список конкретных платформ, которые включают возможность прокрутки, пользовательский элемент управления не должен размещаться в элементах управления прокручиваемые макета например [ `ScrollView` ](xref:Xamarin.Forms.ScrollView).

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для создания списка с платформой и компоновок собственного ячейки.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `ListViewRenderer` класс, который выполняет визуализацию пользовательского элемента управления.
1. Переопределить `OnElementChanged` метода, который отображает пользовательскую логику управления и записи для его настройки. Этот метод вызывается, когда соответствующий Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) создается.
1. Добавление `ExportRenderer` атрибут класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для подготовки к просмотру пользовательский элемент управления Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Это необязательно для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса в ячейке.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](listview-images/solution-structure.png "Обязанности проекта NativeListView пользовательский модуль подготовки отчетов")

`NativeListView` Пользовательский элемент управления отрисовывается классами платформы модуля подготовки отчетов, которые являются производными от `ListViewRenderer` класс для каждой платформы. Это приводит к каждой `NativeListView` пользовательского элемента управления, отображаемого с элементами управления платформы списка и макеты собственного ячейки, как показано на следующем снимке экрана:

![](listview-images/screenshots.png "NativeListView на каждой платформе")

`ListViewRenderer` Предоставляет `OnElementChanged` метод, который вызывается при создании пользовательского элемента управления Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, который содержит `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В приложении-примере `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `NativeListView` экземпляра.

Переопределенная версия `OnElementChanged` метод в каждом классе платформы модуль подготовки отчетов используется, выполните настройку собственного элемента управления. Типизированную ссылку на собственный элемент управления, используется на платформе может осуществляться через `Control` свойство. Кроме того, можно получить ссылку на элемент управления Xamarin.Forms, который готовится к просмотру с помощью `Element` свойство.

Будьте осторожны при подписке на обработчики событий в `OnElementChanged` метод, как показано в следующем примере кода:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Собственный элемент управления должна быть настроена только и подписывать на обработчики событий при присоединении пользовательского отрисовщика к новому элементу Xamarin.Forms. Аналогичным образом все обработчики событий, которые были созданы подписки следует отменять подписку на только в том случае, если элемента с подключенным отрисовщиком. Этот подход поможет вам создать пользовательское средство отрисовки, не страдает от утечек памяти.

Переопределенная версия `OnElementPropertyChanged` метод в каждом классе платформы модуля подготовки отчетов — место для реагирования на изменения может быть привязано на пользовательском элементе управления Xamarin.Forms. Проверяет наличие измененного свойства должны быть выполнены, так как это переопределение может вызываться многократно.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя пользовательского элемента управления Xamarin.Forms визуализируемого типа и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматриваются реализацию каждого класса пользовательского средства визуализации для конкретной платформы.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

`UITableView` Управления настроен путем создания экземпляра `NativeiOSListViewSource` класса, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Этот класс предоставляет данные для `UITableView` управления путем переопределения `RowsInSection` и `GetCell` методы из `UITableViewSource` класса и путем предоставления `Items` свойство, содержащее список данных для отображения. Этот класс также предоставляет `RowSelected` переопределяющий метод, который вызывает `ItemSelected` события, предоставляемые `NativeListView` пользовательского элемента управления. Дополнительные сведения о методе, переопределяет, см. в разделе [UITableViewSource подклассы](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). `GetCell` Возвращает метод `UITableCellView` , заполняется данными для каждой строки в списке и показано в следующем примере кода:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Этот метод создает `NativeiOSListViewCell` экземпляра для каждой строки данных, которое будет отображаться на экране. `NativeiOSCell` Экземпляра определяет макет каждой ячейки и ячейки данных. Если ячейка пропадает с экрана, из-за прокрутка, ячейки будут доступны для повторного использования. Это позволяет избежать расхода памяти, гарантируя, что существуют только `NativeiOSCell` экземпляров для данных, отображаемых на экране, а не все данные в списке. Дополнительные сведения о повторное использование ячеек, см. в разделе [повторное использование ячеек](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). `GetCell` Метод также считывает `ImageFilename` свойства каждой строки данных, что он существует и считывает изображения и сохраняет его как из `UIImage` экземпляра, перед обновлением `NativeiOSListViewCell` для экземпляра с данными (имя категории и изображение) Строка.

`NativeiOSListViewCell` Класс определяет макет для каждой ячейки и показано в следующем примере кода:

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Этот класс определяет элементы управления, используемые для отображения содержимого ячейки и их размещения. `NativeiOSListViewCell` Конструктор создает экземпляры `UILabel` и `UIImageView` элементов управления и инициализирует их внешний вид. Эти элементы управления используются для отображения каждой строки данных, с помощью `UpdateCell` метод, который используется для настройки этих данных на `UILabel` и `UIImageView` экземпляров. Расположение этих экземпляров имеет значение переопределенным `LayoutSubviews` метод, указывая их координаты в ячейке.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Реагирование на изменения свойства для пользовательского элемента управления

Если `NativeListView.Items` свойство изменяется, из-за элементов, добавляемых или удален из списка, пользовательское средство отрисовки должен отвечать путем отображения изменений. Это можно сделать путем переопределения `OnElementPropertyChanged` метод, который показан в следующем примере кода:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

Метод создает новый экземпляр класса `NativeiOSListViewSource` класс, который предоставляет данные для `UITableView` управления, при условии, что привязки `NativeListView.Items` свойство изменилось.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

Собственный `ListView` управления настроен, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Эта конфигурация требует создания экземпляра `NativeAndroidListViewAdapter` класс, предоставляющий данные собственному `ListView` управления и регистрации обработчика событий для обработки `ItemClick` событий. В свою очередь, вызовет этот обработчик `ItemSelected` события, предоставляемые `NativeListView` пользовательского элемента управления. `ItemClick` Событие не отменена элементу Xamarin.Forms модуль подготовки отчетов, подключенного к изменениям.

`NativeAndroidListViewAdapter` Является производным от `BaseAdapter` класс и предоставляет `Items` свойство, содержащее список данных для отображения, а также переопределение `Count`, `GetView`, `GetItemId`, и `this[int]` методы. Дополнительные сведения о эти переопределения методов, см. в разделе [реализация ListAdapter](~/android/user-interface/layouts/list-view/populating.md). `GetView` Метод возвращает представление для каждой строки, заполненную данными и показано в следующем примере кода:

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

`GetView` Метод вызывается для возвращения ячейки для отрисовки как `View`, для каждой строки данных в списке. Он создает `View` экземпляра для каждой строки данных, которое будет отображаться на экране с оформлением `View` экземпляра определенные в файле макета. Если ячейка пропадает с экрана, из-за прокрутка, ячейки будут доступны для повторного использования. Это позволяет избежать расхода памяти, гарантируя, что существуют только `View` экземпляров для данных, отображаемых на экране, а не все данные в списке. Дополнительные сведения о представлении повторного использования, см. в разделе [повторное использование представления строк](~/android/user-interface/layouts/list-view/populating.md).

`GetView` Метод также заполняет `View` экземпляра с данными, включая чтение графических данных с именем, указанным в `ImageFilename` свойство.

Макет каждой ячейки dispayed по собственному `ListView` определяется в `NativeAndroidListViewCell.axml` файл макета, который увеличивается за счет `LayoutInflater.Inflate` метод. В следующем примере кода показано определение макета:

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Реагирование на изменения свойства для пользовательского элемента управления

Если `NativeListView.Items` свойство изменяется, из-за элементов, добавляемых или удален из списка, пользовательское средство отрисовки должен отвечать путем отображения изменений. Это можно сделать путем переопределения `OnElementPropertyChanged` метод, который показан в следующем примере кода:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

Метод создает новый экземпляр класса `NativeAndroidListViewAdapter` класс, предоставляющий данные собственному `ListView` управления, при условии, что привязки `NativeListView.Items` свойство изменилось.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского средства визуализации на UWP

В следующем примере кода показано пользовательское средство отрисовки для универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

Собственный `ListView` управления настроен, при условии, что пользовательское средство отрисовки подключен к новому элементу Xamarin.Forms. Эта конфигурация включает в себя параметр как собственный `ListView` элемент управления будет отвечать на элементы выбора, заполнение данных, отображаемого элементом управления, определяющий внешний вид и содержимое каждой ячейки и регистрации обработчика событий для обработки `SelectionChanged` событий. В свою очередь, вызовет этот обработчик `ItemSelected` события, предоставляемые `NativeListView` пользовательского элемента управления. `SelectionChanged` Событие не отменена элементу Xamarin.Forms модуль подготовки отчетов, подключенного к изменениям.

Внешний вид и содержимое каждого машинный код `ListView` ячейки определяются `DataTemplate` с именем `ListViewItemTemplate`. Это `DataTemplate` хранятся в словаре ресурсов на уровне приложения и показано в следующем примере кода:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Реагирование на изменения свойства для пользовательского элемента управления

Если `NativeListView.Items` свойство изменяется, из-за элементов, добавляемых или удален из списка, пользовательское средство отрисовки должен отвечать путем отображения изменений. Это можно сделать путем переопределения `OnElementPropertyChanged` метод, который показан в следующем примере кода:

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

Метод повторно заполняет собственного `ListView` элемента управления с помощью измененных данных, при условии, что привязки `NativeListView.Items` свойство изменилось.

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательское средство отрисовки, инкапсулирующий специфические для платформы список элементов управления и макеты собственного ячейки, позволяя больший контроль над производительностью собственного списка элемента управления.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererListView (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
