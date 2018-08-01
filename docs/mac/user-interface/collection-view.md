---
title: Представления коллекций в Xamarin.Mac
description: В этой статье описывает работу с представления коллекций в приложении Xamarin.Mac. Она охватывает Создание и поддержание представления коллекций в Xcode и интерфейс построителя и работа с ними программным способом.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: 8e354b1ce273b10758a7d8c1361055b972839943
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792561"
---
# <a name="collection-views-in-xamarinmac"></a>Представления коллекций в Xamarin.Mac

_В этой статье описывает работу с представления коллекций в приложении Xamarin.Mac. Она охватывает Создание и поддержание представления коллекций в Xcode и интерфейс построителя и работа с ними программным способом._

При работе с C# и .NET в приложении Xamarin.Mac, разработчик имеет доступ к тому же AppKit представления коллекции элементов управления, работающий в *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, разработчик использует в Xcode _интерфейс построителя_ для создания и настройки представления коллекций.

Объект `NSCollectionView` отображает сетку организованы с помощью представлений `NSCollectionViewLayout`. Представленный каждого вложенное представление в сетке `NSCollectionViewItem` которой управляет загружать содержимое представления из `.xib` файла.

[![Запустите пример приложения](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

В этой статье рассматриваются основы работы с представлениями коллекции в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Hello, Mac](~/mac/get-started/hello-mac.md) статью, во-первых, в частности [введение в Xcode и интерфейс построителя](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) и [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) части, как он охватывает основные принципы и методы, которые используются в этой статье.

Может потребоваться рассмотреть [предоставление C# классы и методы для Objective-C](~/mac/internals/how-it-works.md) раздел [внутренние компоненты Xamarin.Mac](~/mac/internals/how-it-works.md) документов также объясняется, какие `Register` и `Export` команд используется для передачи до классов C# Objective-C-объекты и элементы пользовательского интерфейса.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>О представлениях коллекции

Основная цель представления коллекции (`NSCollectionView`) является визуально упорядочивать группы объектов структуру предприятия с использованием макета представления коллекции (`NSCollectionViewLayout`), с каждого отдельного объекта (`NSCollectionViewItem`) получение свое представление в крупной коллекции. Представления коллекций работать через методы привязки данных и ключ-значение кодирования и таким образом, рекомендуется ознакомиться с [привязки данных и ключ-значение кодирования](~/mac/app-fundamentals/databinding.md) документации, прежде чем переходить к этой статье.

Представление коллекции имеет нет стандартных, встроенных представления элемента коллекции (например, структуру или табличном представлении делает), поэтому разработчик отвечает за проектирование и реализация _представление прототипа_ использования других AppKit элементов управления, таких как поля изображений , Текстовые поля, метки и т. д. В этом представлении прототип будет использоваться для отображения и работы с каждым элементом, который находится под управлением представление коллекции и хранится в `.xib` файла.

Поскольку разработчик отвечает за внешний вид представления элемента коллекции, представление коллекции не имеет встроенной поддержки для выделения выбранного элемента в сетке. В этой статье рассматриваются реализации этой функции.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Определение модели данных

Прежде чем привязка данных представления коллекции в построителе интерфейс ключ-значение кодирования (KVC) / рассмотрение ключ-значение (KVO)-совместимый класс должен быть определен в приложение Xamarin.Mac в качестве _модели данных_ для привязки. Модель данных предоставляет все данные, которое будет отображаться в коллекции и получает данные, когда пользователь вносит в пользовательском Интерфейсе во время выполнения приложения каких-либо изменений.

Рассмотрим в качестве примера приложения, который управляет группу сотрудников, класс может использоваться для определения модели данных:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

`PersonModel` Модели данных, которые будут использоваться во всей оставшейся части этой статьи.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Работа с представлением коллекции

Привязка данных с помощью представления коллекции определяется во многом like привязки с представлением таблицы `NSCollectionViewDataSource` используется для предоставления данных для коллекции. Поскольку представление коллекции, не имеет формат стиль отображения, больше работы требуется для предоставления отзывов взаимодействие с пользователем и отслеживания выбора пользователя.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Создание прототипов ячейки

Поскольку представление коллекции не содержит прототип ячейки по умолчанию, разработчик должен добавить один или несколько `.xib` файлов Xamarin.Mac приложение, чтобы определить макет и содержимому отдельных ячеек.

Выполните следующие действия:

1. В **обозревателе решений**, щелкните правой кнопкой мыши имя проекта и выберите **добавить** > **новый файл...**
2. Выберите **Mac** > **View Controller**, присвойте ей имя (например, `EmployeeItem` в этом примере) и нажмите кнопку **New** кнопку, чтобы создать: 

    ![Добавление нового представления контроллера](collection-view-images/proto01.png)

    При этом будет добавлено `EmployeeItem.cs`, `EmployeeItemController.cs` и `EmployeeItemController.xib` файла решения.
3. Дважды щелкните `EmployeeItemController.xib` файл, чтобы открыть его для редактирования в построителе интерфейса в Xcode.
4. Добавить `NSBox`, `NSImageView` и два `NSLabel` элементы управления в представление и разместить их следующим образом:

    ![Разработка макета прототип ячейки](collection-view-images/proto02.png)
5. Откройте **помощника редактор** и создать **розетки** для `NSBox` , чтобы он может использоваться для указания состояние выбора ячейки:

    ![Предоставление доступа к NSBox в выхода](collection-view-images/proto03.png)
6. Вернитесь к **стандартный редактор** и выберите представление изображения.
7. В **привязки инспектора**выберите **привязать к** > **владелец файла** и введите **путь к ключу модели** из `self.Person.Icon`:

    ![Значок привязки](collection-view-images/proto04.png)
8. Выберите первую метку и в **привязки инспектора**выберите **привязать к** > **владелец файла** и введите **путь к ключу модели**из `self.Person.Name`:

    ![Имя привязки](collection-view-images/proto05.png)
9. Выберите второй метки и в **привязки инспектора**выберите **привязать к** > **владелец файла** и введите **путь к ключу модели**из `self.Person.Occupation`:

    ![Привязка род занятий](collection-view-images/proto06.png)
10. Сохранить изменения в `.xib` файл и вернуться в Visual Studio, чтобы синхронизировать изменения.

Изменить `EmployeeItemController.cs` файла и сделать его выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

Этот код, подробно касается, этот класс наследует от `NSCollectionViewItem` чтобы мог работать в качестве прототипа для представления коллекции ячейки. `Person` Свойство предоставляет класс, который использовался для привязки данных к представление изображения и метки в Xcode. Это является экземпляром класса `PersonModel` созданной выше.

`BackgroundColor` Свойство является ярлыком для `NSBox` элемента управления `FillColor` будет использоваться для отображения состояния выбора ячейки. Путем переопределения `Selected` свойство `NSCollectionViewItem`, следующий код задает или отменяет это состояние выбора:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Создание источника данных для представления коллекции

Просмотр источников данных коллекции (`NSCollectionViewDataSource`) предоставляет все данные для представления коллекции и создает и заполняет ячейки представления коллекции (с помощью `.xib` прототип) при необходимости для каждого элемента в коллекции.

Добавьте новый класс проекта, она вызывается `CollectionViewDataSource` и сделать его выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

Этот код, подробно касается, этот класс наследует от `NSCollectionViewDataSource` и предоставляет список `PersonModel` экземпляров через его `Data` свойство.

Так как эта коллекция содержит только один раздел, код переопределяет `GetNumberOfSections` метода и всегда возвращает `1`. Кроме того `GetNumberofItems` метод переопределяется на, он возвращает количество элементов в `Data` списка свойств.

`GetItem` Метод вызывается всякий раз, когда новая ячейка является обязательным и имеет следующий вид:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem` Вызывается метод представление коллекции для создания или возврата для повторного использования экземпляр `EmployeeItemController` и его `Person` свойству элемента отображается в ячейке запрошенного. 

`EmployeeItemController` Должен быть зарегистрирован с контроллером представление коллекции заранее, используя следующий код:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

**Идентификатор** (`EmployeeCell`) используется в `MakeItem` вызвать _должен_ соответствует имени контроллера представление, было зарегистрировано в представлении коллекции. Этот шаг будет рассматриваться ниже подробно.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Управление выбором элементов

Для выбора и deselection элементов в коллекции, `NSCollectionViewDelegate` будут необходимы. Поскольку в этом примере будет использовать встроенный в `NSCollectionViewFlowLayout` тип макета `NSCollectionViewDelegateFlowLayout` потребуется конкретной версии этого делегата.

Добавьте новый класс в проект и назовите его `CollectionViewDelegate` и сделать его выглядеть следующим образом:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

`ItemsSelected` И `ItemsDeselected` методы переопределены и используется для установки или снятия `PersonSelected` свойство представление-контроллер, который обрабатывает представление коллекции, если пользователь выбирает или отменяет выделение элемента. Это будет показано ниже подробно.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Создание представления коллекции в построителе интерфейса

Все обязательные вспомогательные элементы на месте основной раскадровки можно изменить, а затем добавить представление коллекции.

Выполните следующие действия:

1. Дважды щелкните `Main.Storyboard` файла в **обозревателе решений** чтобы открыть его для редактирования в Xcode, интерфейс построителя.
2. Перетащите в представление главного представления коллекции и изменить его для заполнения представления:

    ![Добавление представления коллекции к макету](collection-view-images/collection01.png)
3. В представлении коллекции выбран используйте редактор ограничений для закрепления в представление, при изменении его размера:

    ![Добавление ограничений](collection-view-images/collection02.png)
4. Убедитесь, что выбрано представление коллекции **конструктора** (и не **Обычная рамка представлений прокрутки** или **вид отсечения** , содержащего его), перейдите в  **Редактор помощника** и создать **розетки** для представления коллекции:

    ![Добавление ограничений](collection-view-images/collection03.png)
5. Сохранить изменения и вернуться в Visual Studio для синхронизации.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Ее перевод всех Together

Все компоненты, поддерживающие теперь помещенных в месте с помощью класса в качестве модели данных (`PersonModel`), `NSCollectionViewDataSource` был добавлен к передаче данных, `NSCollectionViewDelegateFlowLayout` был создан для обработки Выбор элементов и `NSCollectionView` был добавлен в Main Storyboard и как выхода (`EmployeeCollection`).

Последним шагом является изменение контроллера представления, содержащего представление коллекции и объединить все коды, чтобы заполнить коллекцию и обработать Выбор элемента.

Изменить `ViewController.cs` файла и сделать его выглядеть следующим образом:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

Изучать этот код в сведениях о, `Datasource` для хранения экземпляра определяется свойство `CollectionViewDataSource` , предоставляющего данные для представления коллекции. Объект `PersonSelected` для хранения определяется свойство `PersonModel` представляющий выбранного элемента в представлении коллекции. Это свойство также вызывает `SelectionChanged` событие при изменении выделения.

`ConfigureCollectionView` Класс используется для регистрации представление-контроллер, который выступает в качестве прототипа ячейки с представлением коллекции, используя следующую строку:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Обратите внимание, что **идентификатор** (`EmployeeCell`), используемый для регистрации прототип совпадения вызывается один в `GetItem` метод `CollectionViewDataSource` определенный выше:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Кроме того, тип View Controller **должен** совпадает с именем `.xib` файл, который определяет прототип **точно**. В случае в этом примере `EmployeeItemController` и `EmployeeItemController.xib`.

Фактическое размещение элементов в представлении коллекции осуществляется с помощью класса разметки представления коллекции и может динамически изменяться во время выполнения путем назначения новому экземпляру `CollectionViewLayout` свойство. Изменение этого свойства обновляет внешний вид представления коллекции без анимации изменения.

Apple поставляется в двух типов встроенных макета с представление коллекции, который будет обрабатывать наиболее типичные способы применения: `NSCollectionViewFlowLayout` и `NSCollectionViewGridLayout`. Если разработчику требуется пользовательский формат, например, размещение элементов в круга, можно создать пользовательский экземпляр `NSCollectionViewLayout` и переопределить методы, необходимые для достижения желаемого эффекта.

В этом примере используется потоковый макет по умолчанию, поэтому он создает экземпляр `NSCollectionViewFlowLayout` класса и настраивает его следующим образом:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize` Определяет размер каждой отдельной ячейки в коллекции. `SectionInset` Свойство определяет отступы края ячейки будут размещаться в коллекции. `MinimumInteritemSpacing` Определяет минимальное расстояние между элементами и `MinimumLineSpacing` определяет минимальный интервал между строками в коллекции.

Макет связанное представление коллекции и экземпляр `CollectionViewDelegate` присоединен осуществлять выбор элемента:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData` Метод создает новый экземпляр `CollectionViewDataSource`, заполняет ее данными, присоединяет представление коллекции и вызовы `ReloadData` метод для отображения элементов:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

`ViewDidLoad` Метод переопределяется и вызывает метод `ConfigureCollectionView` и `PopulateWithData` методов для отображения последней представление коллекции для пользователя:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Сводка

Подробное описание работы с представлениями коллекции в приложении Xamarin.Mac вступила в этой статье. Во-первых хотя рассматривались предоставления класса C# для Objective-C с помощью кодирования ключ-значение (KVC) и рассмотрение ключ-значение (KVO). Затем демонстрировалось совместимого класса KVO и данных привязать его к представлениям коллекции в построителе интерфейса в Xcode. Наконец показано, как работать с представлениями коллекции в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacCollectionNew (пример)](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Привязка данных и кодирование типа "ключ — значение"](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
