---
title: Работа с tvOS представления коллекций в Xamarin
description: В этом документе описывается работа с представлениями коллекций в приложении tvOS, созданных с помощью Xamarin. Он охватывает коллекции макеты, создание ячеек и дополнительных представлений, реагирование на события пользователя и многое другое.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108032"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Работа с tvOS представления коллекций в Xamarin

Представления коллекций разрешить для группы содержимое будет отображаться с использованием произвольного макетов. С помощью встроенной поддержки, они позволяют для упрощенного создания макетов сеткоподобной или линейной, поддерживая пользовательских макетов.

[![](collection-views-images/collection01.png "Пример представления коллекции")](collection-views-images/collection01.png#lightbox)

Представление коллекции поддерживает коллекцию элементов с помощью делегата и источником данных для обеспечения взаимодействия с пользователем и содержимое коллекции. Так как представление коллекции зависит от подсистемы разметки, который не зависит от самого представления, предоставляя структуру можно легко изменить представление представления коллекции данных на лету.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>О представлениях коллекций

Как уже говорилось, представление коллекции (`UICollectionView`) управляет упорядоченную коллекцию элементов, а также предоставляет настраиваемые макеты этих элементов. Представления коллекций работать аналогичным образом для представления таблиц (`UITableView`), за исключением того, злоумышленник может использовать шаблоны, которые присутствуют элементов больше, чем просто один столбец.

При использовании представления коллекции в tvOS, ваше приложение будет отвечать за предоставление данных, связанные с коллекцией, с использованием источника данных (`UICollectionViewDataSource`). Данные представления коллекции можно при необходимости организованы и представленные в разных группах (разделов).

Представление коллекции представляет отдельные элементы на экране с помощью ячейки (`UICollectionViewCell`), предоставляющий представления данной части сведения из коллекции (например, изображения и заголовка).

При необходимости с дополнительными представлениями могут добавляться к презентации представления коллекции в качестве заголовков и нижних колонтитулов для разделов и ячеек. Макет представления коллекции отвечает за определение размещение этих представлений, а также отдельные ячейки.

Представление коллекции может отвечать на действия пользователя с помощью делегата (`UICollectionViewDelegate`). Этот делегат также отвечает за определение, если данная ячейка получает фокус, если ячейки были выделены или если отмечен. В некоторых случаях делегата определяет размер отдельных ячеек.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Макеты коллекции

Ключевой особенностью представление коллекции — его разделение между данными, он представления и макета. Макет представления коллекции (`UICollectionViewLayout`) отвечает за предоставление организации и расположение ячейки (и все дополнительные представления) в представлении коллекции на экране презентации.

Отдельные ячейки, создаваемые в представлении коллекции из присоединенного источника данных и затем упорядочены и отображаются с указанным макетом представления коллекции.

Макет представления коллекции обычно предоставляется при создании представления коллекции. Тем не менее макет представления коллекции можно изменить в любое время и на экране представлением данных представления коллекции будут автоматически обновлены с помощью нового макета предоставляются.

Макет представления коллекции предоставляет несколько методов, которые можно использовать для анимации перехода между двумя разными макетами (по умолчанию, когда анимация не происходит). Кроме того макеты коллекции можно работать с распознавателей жестов для дальнейшего анимации взаимодействия с пользователем, который приводит к изменению в макете.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Создать ячейки и представления, с дополнительными символами

Источник данных для представления коллекции не только отвечает за предоставление резервного коллекции элементов, но также и данные, которые используются для отображения содержимого ячейки.

Так как представления коллекций были предназначены для обработки больших наборов элементов, отдельные ячейки можно удаляются из очереди и использовать повторно, чтобы предотвратить переполнение ограничения памяти. Существует два метода для выведения представлений:

- `DequeueReusableCell` — Создает или возвращает ячейку данного типа (как указано в раскадровке приложения).
- `DequeueReusableSupplementaryView` — Создает и возвращает представление указанного типа (как указано в раскадровке приложения) с дополнительными символами.

Перед вызовом любого из этих методов, необходимо зарегистрировать класс, раскадровки или `.xib` файл используется для создания ячейки представления с представлением коллекции. Пример:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Где `typeof(CityCollectionViewCell)` предоставляет класс, который поддерживает представление и `CityViewDatasource.CardCellId` содержит идентификатор, используемый при ячейки (или представления) выводится из очереди.

После ячейки из очереди, настройка его с данными для элемента, который он представляет и вернуться к представлению коллекции для отображения.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>О контроллеров представления коллекции

Контроллер представления коллекции (`UICollectionViewController`) — это специализированный контроллер представления (`UIViewController`), предоставляющий следующее поведение:

- Он отвечает за загрузку представление коллекции из его раскадровки или `.xib` файл и создания представления. Если создан в коде, он автоматически создает представление коллекции новая, ненастроенная.
- После загрузки представления коллекции, контроллер пытается загрузить источник данных и делегат из раскадровки или `.xib` файл. Если ни одна не доступна, она устанавливается как источником.
- Гарантирует, что данные загружены прежде, чем представление коллекции заполняется в первую очередь отображается и перезагружает и снимите выбор на каждом из последующих.

Кроме того, контроллер представления коллекций предоставляет переопределяемых методов, которые можно использовать для управления жизненным циклом представления коллекции, такие как `AwakeFromNib` и `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Представления коллекций и раскадровки

Самый простой способ работы с представлением коллекции, в приложении Xamarin.tvOS — добавить в соответствующую раскадровку. Как краткий пример мы собираемся создавать пример приложения, которое представляет изображение, заголовок и кнопки "выбрать". Если пользователь щелкнуть кнопки "выбрать", представление коллекции будет отображаться, которых пользователь может выбрать новый образ. При выборе изображения, представление коллекции закрывается, и отображается новое изображение и заголовок.

Давайте, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

    
1. Запуск нового **единое представление приложения tvOS** в Visual Studio для Mac.
1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его в конструкторе iOS.
1. Добавить представление изображения, метки и кнопка существующее представление и настроить их, чтобы выглядеть следующим образом: 

    [![](collection-views-images/collection02.png "Пример макета")](collection-views-images/collection02.png#lightbox)
1. Назначить **имя** представление изображений и метки в **вкладку графического** из **обозревателе свойств**. Пример: 

    [![](collection-views-images/collection03.png "Задание имени")](collection-views-images/collection03.png#lightbox)
1. Затем перетащите раскадровки контроллера представления коллекций: 

    [![](collection-views-images/collection04.png "Контроллер представления коллекции")](collection-views-images/collection04.png#lightbox)
1. Перетаскивание с помощью кнопки на контроллер представления коллекции и выберите **Push** из всплывающего окна: 

    [![](collection-views-images/collection05.png "Выберите во всплывающем Push-уведомлений")](collection-views-images/collection05.png#lightbox)
1. При запуске приложения, это сделает представление коллекции можно показать всякий раз, когда пользователь нажимает кнопку.
1. Выберите представление коллекции и введите следующие значения в **вкладке "Макет"** из **обозревателе свойств**: 

    [![](collection-views-images/collection06.png "В обозревателе свойств")](collection-views-images/collection06.png#lightbox)
1. Определяет размер отдельных ячеек и границы между ячейками и внешним краем представления коллекции.
1. Выберите контроллер представления коллекций и установите его класса в `CityCollectionViewController` в **вкладку графического**: 

    [![](collection-views-images/collection07.png "Задайте класс CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Выберите представление коллекции и присвоить его класс `CityCollectionView` в **вкладку графического**: 

    [![](collection-views-images/collection08.png "Задайте класс CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Выберите ячейки представления коллекций и установите его класса в `CityCollectionViewCell` в **вкладку графического**: 

    [![](collection-views-images/collection09.png "Задайте класс CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. В **вкладку графического** убедитесь, что **макета** — `Flow` и **направление прокрутки** является `Vertical` для представления коллекции: 

    [![](collection-views-images/collection10.png "На вкладке мини-приложения")](collection-views-images/collection10.png#lightbox)
1. Выберите ячейки представления коллекций и задайте его **удостоверений** для `CityCell` в **вкладку графического**: 

    [![](collection-views-images/collection11.png "Задать удостоверение для CityCell")](collection-views-images/collection11.png#lightbox)
1. Сохраните изменения.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Запуск нового **единое представление приложения tvOS** в Visual Studio.
1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его в конструкторе iOS.
1. Добавить представление изображения, метки и кнопка существующее представление и настроить их, чтобы выглядеть следующим образом: 

    [![](collection-views-images/collection02vs.png "Настройка макета")](collection-views-images/collection02vs.png#lightbox)
1. Назначить **имя** представление изображений и метки в **вкладку графического** из **обозревателе свойств**. Пример: 

    [![](collection-views-images/collection03vs.png "В обозревателе свойств")](collection-views-images/collection03vs.png#lightbox)
1. Затем перетащите раскадровки контроллера представления коллекций: 

    [![](collection-views-images/collection04vs.png "Контроллер представления коллекции")](collection-views-images/collection04vs.png#lightbox)
1. Перетаскивание с помощью кнопки на контроллер представления коллекции и выберите **Push** из всплывающего окна: 

    [![](collection-views-images/collection05vs.png "Выберите во всплывающем Push-уведомлений")](collection-views-images/collection05vs.png#lightbox)
1. При запуске приложения, это сделает представление коллекции можно показать всякий раз, когда пользователь нажимает кнопку.
1. Выберите представление коллекции и в **вкладке "Макет"** из **обозревателе свойств** введите **ширины** как _361_ и  **Высота** как _256_ 
1. Определяет размер отдельных ячеек и границы между ячейками и внешним краем представления коллекции.
1. Выберите контроллер представления коллекций и установите его класса в `CityCollectionViewController` в **вкладку графического**: 

    [![](collection-views-images/collection07vs.png "Задайте класс CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Выберите представление коллекции и присвоить его класс `CityCollectionView` в **вкладку графического**: 

    [![](collection-views-images/collection08vs.png "Задайте класс CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Выберите ячейки представления коллекций и установите его класса в `CityCollectionViewCell` в **вкладку графического**: 

    [![](collection-views-images/collection09vs.png "Задайте класс CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. В **вкладку графического** убедитесь, что **макета** — `Flow` и **направление прокрутки** является `Vertical` для представления коллекции: 

    [![](collection-views-images/collection10vs.png "Вкладка указанное мини-приложения")](collection-views-images/collection10vs.png#lightbox)
1. Выберите ячейки представления коллекций и задайте его **удостоверений** для `CityCell` в **вкладку графического**: 

    [![](collection-views-images/collection11vs.png "Задать удостоверение для CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Сохраните изменения.
    

-----

Если бы мы выбрали `Custom` для представления коллекции **макета**, можно было указать настраиваемый макет. Компания Apple предоставляет встроенный `UICollectionViewFlowLayout` и `UICollectionViewDelegateFlowLayout` , можно легко представить данные в структуру на основе сетки (использует эти порты `flow` стиль макета). 

Дополнительные сведения о работе с раскадровками, см. в разделе наших [по Tvos краткое руководство по](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Предоставление данных для представления коллекции

Теперь, когда у нас есть представление коллекции (из наших контроллера представления коллекций) добавлены в наши раскадровку, необходимо предоставить данные для коллекции. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>Модель данных

Во-первых мы собираемся создать модель данных, содержащий имя файла для изображения, отображаемого в заголовке и флаг разрешения города для выбора.

Создание `CityInfo` класса и это должно выглядеть следующим образом:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>Ячейка представления коллекций

Теперь нам нужно определить способ представления данных для каждой ячейки. Изменить `CityCollectionViewCell.cs` файла (создается автоматически из файла раскадровки) и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Для нашего приложения tvOS мы будет отображаться изображение и необязательный заголовок. Если в этом городе не могут быть выбраны, мы затемнение представление изображений, используя следующий код:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Когда ячейка, содержащая изображения переводится фокус пользователем, мы хотим использовать встроенное эффекта фокусировки на нем настраиваться следующее свойство:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Дополнительные сведения о навигации и фокус, см. в разделе наших [работа с навигацией и фокусом](~/ios/tvos/app-fundamentals/navigation-focus.md) и [контроллеры Siri Remote и контроллеры Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) документации.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>Поставщик данных для представления коллекции

С нашей модели данных, созданной и наш макет ячейки, которые определены давайте создадим источник данных нашего представления коллекции. Источник данных будет не только обеспечивают резервных данных, но также выведения ячеек для отображения на экране отдельные ячейки.

Создание `CityViewDatasource` класса и это должно выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Позвольте рассмотрим этот класс подробно. Во-первых, мы наследовать от `UICollectionViewDataSource` и создания ярлыка для идентификатор ячейки (который мы назначили в конструкторе iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Далее мы используются для хранения наших данных коллекции и предоставить класс для заполнения данных:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

Затем мы переопределяем `NumberOfSections` метода и возврат, количество разделов (групп элементов), которые будут видеть нашей коллекции есть. В этом случае имеется только один:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Далее мы возвращается количество элементов в нашей коллекции, используя следующий код:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Наконец мы вывода из очереди многократно используемые ячейки при запросе представления коллекции следующим кодом:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Получив ячейки представления коллекций из наших `CityCollectionViewCell` типа, мы ее заполнение данного элемента.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Реагирование на события пользователей

Поскольку необходимо, чтобы пользователь мог выбрать элемент из коллекции, необходимо предоставить делегата представление коллекции для обработки этого взаимодействия. Также необходим для предоставления тем, чтобы наши вызывающего представления знать, какой элемент пользователь выбрал.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>Делегат приложения

Необходим способ для связи текущего выбранного элемента из представления коллекций у вызывающего представления. Мы будем использовать пользовательское свойство в наших `AppDelegate`. Изменить `AppDelegate.cs` файл и добавьте следующий код:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Это определяет свойство и задает значение по умолчанию города, показываемое изначально. Позже мы будем использовать это свойство для отображения выбранного пользователем и разрешение select необходимо изменить.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>Делегат представления коллекции

Добавьте новый `CityViewDelegate` класса в проект и это должно выглядеть следующим образом:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Давайте более подробно рассмотрим этот класс. Во-первых, мы наследовать от `UICollectionViewDelegateFlowLayout`. Причина, по которой мы наследуют от этого класса и не `UICollectionViewDelegate` — это то, что мы используем встроенный `UICollectionViewFlowLayout` для представления нашей элементы и не является типом пользовательского макета.

Далее мы возвращаем размер для отдельных элементов, с помощью следующего кода:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Затем мы решаем, если данная ячейка может получить фокус, используя следующий код: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

Мы проверяем, чтобы определить наличие данного фрагмента данных резервного его `CanSelect` флаг значение `true` и возвращают это значение. Дополнительные сведения о навигации и фокус, см. в разделе наших [работа с навигацией и фокусом](~/ios/tvos/app-fundamentals/navigation-focus.md) и [контроллеры Siri Remote и контроллеры Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) документации.

Наконец мы отвечаем на пользователя, выбирающего элемент со следующим кодом:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Здесь мы задаем `SelectedCity` свойство наших `AppDelegate` к элементу, что выбранный пользователем и мы закрыть контроллер представления коллекций, вернуться к представлению, который вызвал нам. Мы еще не определили `ParentController` свойство нашего представления коллекции еще мы сделаем то далее.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Настройка представления коллекции

Теперь нам нужно изменить нашего представления коллекции и назначить наш источник данных и делегата. Изменить `CityCollectionView.cs` файл (создано автоматически из наших раскадровки) и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

Во-первых, мы предоставляем ярлыком для доступа к нашей `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Далее представлена ярлыком для представления коллекции источник данных и свойства для доступа к коллекции контроллер представления (используется наш выше делегат для закрытия коллекции, когда пользователь делает выбор):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Затем мы используем следующий код для инициализации представления коллекции и назначить наш класс ячейки, источник данных и делегат:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Наконец мы хотим заголовка под изображением, чтобы быть видимыми только когда у пользователя есть он выделен (фокус). Мы делаем, следующим кодом:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Мы устанавливаем transparence предыдущего элемента теряет фокус на ноль (0) и transparence следующего элемента сфокусирован на 100%. Эти перехода получить также анимировано.


## <a name="configuring-the-collection-view-controller"></a>Настройка контроллера представления коллекций

Теперь нам нужно сделать Окончательная настройка нашего представления коллекции и разрешить контроллеру задавать свойство, которое мы определили, поэтому представление коллекции может быть закрыт после пользователь делает выбор.

Изменить `CityCollectionViewController.cs` файл (создаваемый автоматически из наших раскадровки) и это должно выглядеть следующим образом:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Окончательная сборка 

Теперь, когда у нас есть все частей собрали для заполнения и управлять наших представление коллекции, нам нужно редактировать окончательный нашего основного представления для их объединения.

Изменить `ViewController.cs` файл (создаваемый автоматически из наших раскадровки) и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

В следующем коде отображается изначально выбранный элемент из `SelectedCity` свойство `AppDelegate` и повторно отображает его, когда пользователь сделал выбор из представления коллекций:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>Тестирование приложения

Все если построить и запустить приложение, основное представление отображается с города по умолчанию:

[![](collection-views-images/run01.png "Главный экран")](collection-views-images/run01.png#lightbox)

Если пользователь щелкнуть **выберите представление** будет отображаться кнопка, представление коллекции:

[![](collection-views-images/run02.png "Представление коллекции")](collection-views-images/run02.png#lightbox)

Все города с его `CanSelect` свойству присвоено `false` отображается серым цветом, и пользователь не сможет установить фокус на него. Когда пользователь выделяет элемент (сделать его с фокусом) отображается заголовок, и они могут использовать эффект фокусировки, чтобы один важный нюанс наклона изображения в трехмерном пространстве.

Когда пользователь щелкает выберите изображение, представление коллекции закрывается, и основное представление отображается повторно с нового образа:

[![](collection-views-images/run03.png "Новый образ на начальном экране")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Создание пользовательского макета и переупорядочение элементов

Одним из ключевых особенностей использования представления коллекции является возможность создавать собственные макеты. Поскольку tvOS наследует от операций ввода-вывода, процесс создания пользовательского макета совпадает. См. наш [введение к представлениям коллекции](~/ios/user-interface/controls/uicollectionview.md) Дополнительные сведения см.

Недавно добавленные к представлениям коллекции для iOS 9 была возможность легко разрешить изменение порядка элементов в коллекции. Опять же, поскольку tvOS 9 — это подмножество iOS 9, это делается их так же. См. наш [изменения представления коллекции](~/ios/user-interface/controls/uicollectionview.md) документа для получения дополнительных сведений.


<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работа с представлениями коллекции внутри приложения Xamarin.tvOS. Во-первых это обсуждалось всех элементов, составляющих представление коллекции. Далее показано, как проектировать и реализовывать представлением коллекции, с помощью раскадровки. Наконец предоставляются ссылки на сведения о создании пользовательских макетов и изменение порядка элементов.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
