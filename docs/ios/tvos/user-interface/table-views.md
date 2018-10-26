---
title: Работа с tvOS табличные представления в Xamarin
description: В этой статье рассматривается проектирование и работать с представления и таблицы контроллеров представления таблицы внутри приложения Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a8b31d9f2e9c8921420b1fd50c4092273962f54b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105900"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Работа с tvOS табличные представления в Xamarin

_В этой статье рассматривается проектирование и работать с представления и таблицы контроллеров представления таблицы внутри приложения Xamarin.tvOS._

В tvOS представление таблицы представляется в виде отдельного столбца Прокрутка строк, которые при необходимости могут быть организованы в группы или разделы. Представления таблиц следует использовать, когда необходимо эффективно отображать большой объем данных для пользователя, в незашифрованном виде, чтобы понять способ.

Представления таблиц обычно отображаются в одной стороне [разделенное представление](~/ios/tvos/user-interface/split-views.md) как навигация с подробными сведениями выбранного элемента на противоположной стороне:

[![](table-views-images/intro01.png "Пример представления таблицы")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>О представлениях таблиц

Объект `UITableView` отображает один столбец прокручиваемые строк в виде иерархического списка данных, которые при необходимости могут быть организованы в группы или разделах: 

[![](table-views-images/table01.png "Выбранный элемент")](table-views-images/table01.png#lightbox)

Apple имеет следующие рекомендации для работы с таблицами:

- **Иметь учитывать ширину** -попробуйте найти правильный баланс в вашей ширина таблицы. Если таблица является слишком широкие, он может быть трудно сканирования на расстоянии и можно извлечь из области Доступные содержимого. Если таблица является слишком узкий, это может привести к сведения усечение или переноса по словам, еще раз это может быть трудно пользователю считывать из в комнате.
- **Показать содержимое таблицы быстро** — большой список данных, отложенную загрузку содержимого и отображение сведений, как только таблицы, представленные пользователю. Если таблицы до загрузка занимает много времени, пользователю могут потерять интерес к вашему приложению или он блокирован до обработки.
- **Информировать пользователя из Long содержимого загружает** — Если время загрузки большой таблицы неизбежно, присутствует [индикатор выполнения или индикатор активности](~/ios/tvos/user-interface/progress-indicators.md) , чтобы знать, приложение не ведёт.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Типы ячейки представления таблиц

Объект `UITableViewCell` используется для представления отдельных строк данных в табличном представлении. Apple определено несколько типов ячеек таблицы по умолчанию:

- **По умолчанию** — это тип, создает параметр изображения слева от ячейки и заголовок по левому краю в правой части. 
- **Подзаголовок** — это тип, создает заголовок по левому краю в первой строке и меньшее по левому краю подзаголовок на следующей строке.
- **Значение 1** -этот тип представляет заголовок по левому краю с подзаголовок налегке цветной, по правому краю в той же строке.
- **Значение 2** -этот тип представляет заголовок по правому краю с подзаголовок налегке цветной — по левому краю в той же строке.

Все типы ячейки представление таблицы по умолчанию также поддерживают графические элементы, такие как индикаторы раскрытия или флажки. 

Кроме того, можно определить **Custom** представление в виде ячейки таблицы и настоящее _ячейки прототип_, либо создать в конструкторе интерфейса или с помощью кода.

Apple имеет следующие рекомендации для работы с ячейками таблицы представления.

- **Избегайте обрезки текста** -поддерживать отдельные строки текста короткий таким образом, чтобы они не попадут усеченное. Усеченный слов или фраз трудны для пользователя, для анализа из для комнаты.
- **Рассмотрим текущее состояние строки Focused** — так как строки становится больше, более округленное углов в фокусе, необходимые для тестирования внешний вид ячейки во всех состояниях. Изображения или текста могут быть обрезаны, или найти неверные в состоянии фокуса.
- **Использовать редактируемой таблицы только в случае необходимости** -перемещение или удаление строк таблицы занимает больше времени на tvOS чем iOS. Необходимо тщательно решить, если эта функция будет добавлять или отвлечь от приложения tvOS.
- **Создание пользовательских ячейки типов в обычном** — так как встроенные типы ячейки представления таблиц отлично подходят для многих ситуациях, рассмотрите возможность создания пользовательских типов ячейки нестандартные сведения для предоставления большего контроля и лучше представлять информацию для пользователь.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Работа с представлениями таблиц

Самый простой способ работы с представлениями таблиц в приложении Xamarin.tvOS — для создания и изменения их внешнего вида в конструкторе интерфейса.

Чтобы начать работу, выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
    
1. В Visual Studio для Mac, создайте новый проект приложения tvOS и выберите **tvOS** > **приложения** > **приложение с одним представлением** и нажмите кнопку  **Далее** кнопки: 

    [![](table-views-images/table02.png "Выберите приложение одного представления")](table-views-images/table02.png#lightbox)
1. Введите **имя** для приложения и нажмите кнопку **Далее**: 

    [![](table-views-images/table03.png "Введите имя для приложения")](table-views-images/table03.png#lightbox)
1. Либо измените **имя_проекта** и **имя решения** или примите значения по умолчанию и нажмите кнопку **создать** кнопку, чтобы создать новое решение: 

    [![](table-views-images/table04.png "Имя проекта и имя решения")](table-views-images/table04.png#lightbox)
1. В **панели решения**, дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструкторе iOS: 

    [![](table-views-images/table05.png "Файл Main.storyboard")](table-views-images/table05.png#lightbox)
1. Выберите и удалите **контроллер представления по умолчанию**: 

    [![](table-views-images/table06.png "Выберите и удалите контроллер представления по умолчанию")](table-views-images/table06.png#lightbox)
1. Выберите **контроллер разделенного представления** из **элементов** и перетащите его в область конструктора.
1. По умолчанию, вы получите [разделенное представление](~/ios/tvos/user-interface/split-views.md) с **контроллер представления навигации** и **контроллер представления таблиц** в левой части и **контроллер представления** в правой части. Это предлагается использовать корпорации Apple представления таблицы в tvOS: 

    [![](table-views-images/table08.png "Добавление разделенное представление")](table-views-images/table08.png#lightbox)
1. Необходимо будет выбрать каждая часть представления таблицы и назначить его пользовательский **имя класса** в **мини-приложение** вкладке **обозревателе свойств** таким образом, вы сможете открыть его позже в C#кода. Например **контроллер представления таблиц**: 

    [![](table-views-images/table09.png "Назначьте имя класса")](table-views-images/table09.png#lightbox)
1. Обязательно создайте пользовательский класс для **контроллер представления таблиц**, **табличное представление** , а также **ячеек прототип**. Visual Studio для Mac будет Добавьте пользовательские классы в дерево проектов, так как они создаются: 

    [![](table-views-images/table10.png "Пользовательские классы в дереве проекта")](table-views-images/table10.png#lightbox)
1. Затем выберите представление таблицы в области конструктора и при необходимости измените его свойств. Например, число **ячеек прототип** и **стиля** (обычный или сгруппированные): 

    [![](table-views-images/table11.png "На вкладке мини-приложения")](table-views-images/table11.png#lightbox)
1. Для каждого **ячейки прототип**, выберите его и присваивает уникальный **идентификатор** в **мини-приложение** вкладке **обозревателе свойств**. Этот шаг является _очень важным_ — этот идентификатор понадобится позже при заполнении таблицы. Например `AttrCell`: 

    [![](table-views-images/table12.png "На вкладке мини-приложения")](table-views-images/table12.png#lightbox)
1. Можно также выбрать для представления ячейки, среди [по умолчанию типы ячейки представления таблиц](#Table-View-Cell-Types) через **стиля** раскрывающийся список или установить его на **Custom** и использовать область конструктора, чтобы макет ячейки путем перетаскивания в других мини-приложениях пользовательского интерфейса из **элементов**: 

    [![](table-views-images/table13.png "Макет ячейки")](table-views-images/table13.png#lightbox)
1. Присваивает уникальный **имя** для каждого элемента пользовательского интерфейса в прототипе ячейку Интернет в **мини-приложение** вкладке **обозревателе свойств** , они будут доступны позже в C# кода: 

    [![](table-views-images/table14.png "Присвойте имя")](table-views-images/table14.png#lightbox)
1. Повторите предыдущий шаг для всех ячеек прототипа в табличном представлении.
1. Затем назначить пользовательские классы для остальных Дизайн пользовательского интерфейса, макет в представлении сведений и назначить уникальный **имена** для каждого элемента пользовательского интерфейса в сведениях об просматривать таким образом, вы можете получить доступ к их в C# также. Пример. 

    [![](table-views-images/table15.png "Макет пользовательского интерфейса")](table-views-images/table15.png#lightbox)
1. Сохраните изменения в раскадровку.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. В Visual Studio создайте новый проект приложения tvOS и выберите **tvOS** > **приложение с одним представлением** и введите имя для вашего приложения. Нажмите кнопку **хорошо** кнопку, чтобы создать новое решение: 

    [![](table-views-images/table02-vs.png "Выберите приложение одного представления")](table-views-images/table02-vs.png#lightbox)
1. В **обозревателе решений**, дважды щелкните `Main.storyboard` файл, чтобы открыть его в конструкторе iOS: 

    [![](table-views-images/table05-vs.png "Файл Main.storyboard")](table-views-images/table05-vs.png#lightbox)
1. Выберите и удалите **контроллер представления по умолчанию**: 

    [![](table-views-images/table06-vs.png "Выберите и удалите контроллер представления по умолчанию")](table-views-images/table06-vs.png#lightbox)
1. Выберите **контроллер разделенного представления** из **элементов** и перетащите его в область конструктора: 

    [![](table-views-images/table07-vs.png "Контроллер разделенного представления")](table-views-images/table07-vs.png#lightbox)
1. По умолчанию, вы получите [разделенное представление](~/ios/tvos/user-interface/split-views.md) с **контроллер представления навигации** и **контроллер представления таблиц** в левой части и **контроллер представления** в правой части. Это предлагается использовать корпорации Apple представления таблицы в tvOS: 

    [![](table-views-images/table08-vs.png "Макет пользовательского интерфейса")](table-views-images/table08-vs.png#lightbox)
1. Необходимо будет выбрать каждая часть представления таблицы и назначить его пользовательский **имя класса** в **мини-приложение** вкладке **обозревателе свойств** таким образом, вы сможете открыть его позже в C#кода. Например **контроллер представления таблиц**: 

    [![](table-views-images/table09-vs.png "На вкладке мини-приложения")](table-views-images/table09-vs.png#lightbox)
1. Обязательно создайте пользовательский класс для **контроллер представления таблиц**, **табличное представление** , а также **ячеек прототип**. Visual Studio для Mac будет Добавьте пользовательские классы в дерево проектов, так как они создаются: 

    [![](table-views-images/table10-vs.png "Пользовательские классы в дереве проекта")](table-views-images/table10-vs.png#lightbox)
1. Затем выберите представление таблицы в области конструктора и при необходимости измените его свойств. Например, число **ячеек прототип** и **стиля** (обычный или сгруппированные): 

    [![](table-views-images/table11-vs.png "На вкладке мини-приложения")](table-views-images/table11-vs.png#lightbox)
1. Для каждого **ячейки прототип**, выберите его и присваивает уникальный **идентификатор** в **мини-приложение** вкладке **обозревателе свойств**. Этот шаг является _очень важным_ — этот идентификатор понадобится позже при заполнении таблицы. Например `AttrCell`: 

    [![](table-views-images/table12-vs.png "Назначьте идентификатор")](table-views-images/table12-vs.png#lightbox)
1. Можно также выбрать для представления ячейки, среди [по умолчанию типы ячейки представления таблиц](#Table-View-Cell-Types) через **стиля** раскрывающийся список или установить его на **Custom** и использовать область конструктора, чтобы макет ячейки путем перетаскивания в других мини-приложениях пользовательского интерфейса из **элементов**: 

    [![](table-views-images/table13-vs.png "Раскрывающийся список стилей")](table-views-images/table13-vs.png#lightbox)
1. Присваивает уникальный **имя** для каждого элемента пользовательского интерфейса в прототипе ячейку Интернет в **мини-приложение** вкладке **обозревателе свойств** , они будут доступны позже в C# кода: 

    [![](table-views-images/table14-vs.png "На вкладке мини-приложения")](table-views-images/table14-vs.png#lightbox)
1. Повторите предыдущий шаг для всех ячеек прототипа в табличном представлении.
1. Затем назначить пользовательские классы для остальных Дизайн пользовательского интерфейса, макет в представлении сведений и назначить уникальный **имена** для каждого элемента пользовательского интерфейса в сведениях об просматривать таким образом, вы можете получить доступ к их в C# также. Пример. 

    [![](table-views-images/table15.png "Макет пользовательского интерфейса")](table-views-images/table15.png#lightbox)
1. Сохраните изменения в раскадровку.
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Проектирование модели данных

Для упрощения работы с данными, проще будет отображаться представление таблицы, а также для упрощения представления подробных сведений (как пользователь выбирает или выделяет строки в представлении таблицы), создайте пользовательский класс или классы в качестве модели данных для данных представления. .

Рассмотрим в качестве примера приложение бронирования путешествия, которое содержит список **городов**, каждый, содержащий уникальный список **сеанса** , пользователь может выбрать. Пользователь будет иметь возможность пометить притяжения как *избранные*, выберите, чтобы получить *направлениях* для притяжения и *самолет* заданного городе.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Чтобы создать модель данных для **притяжения**, щелкните правой кнопкой мыши имя проекта в **панели решения** и выберите **добавить** > **новый файл...** . Введите `AttractionInformation` для **имя** и нажмите кнопку **New** кнопки: 

[![](table-views-images/data01.png "Введите AttractionInformation имени")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы создать модель данных для **притяжения**, щелкните правой кнопкой мыши имя проекта в **обозревателе решений** и выберите **добавить** > **новый элемент ...** . Выберите **класс** и введите `AttractionInformation` для **имя** и нажмите кнопку **добавить** кнопки: 

[![](table-views-images/data01-vs.png "Выберите класс и введите AttractionInformation имени")](table-views-images/data01-vs.png#lightbox)

-----

Изменить `AttractionInformation.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Этот класс предоставляет свойства для хранения сведений о заданной **притяжения**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

После этого щелкните правой кнопкой мыши имя проекта в **панели решения** еще раз и выберите **добавить** > **новый файл...** . Введите `CityInformation` для **имя** и нажмите кнопку **New** кнопки: 

[![](table-views-images/data02.png "Введите CityInformation имени")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

После этого щелкните правой кнопкой мыши имя проекта в **обозревателе решений** еще раз и выберите **добавить** > **новый элемент...** . Введите `CityInformation` для **имя** и нажмите кнопку **добавить** кнопки: 

[![](table-views-images/data02-vs.png "Введите CityInformation имени")](table-views-images/data02-vs.png#lightbox)

-----

Изменить `CityInformation.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Этот класс содержит все сведения о назначении **Город**, коллекция **сеанса** для такого города и предоставляет два вспомогательных метода (`AddAttraction`) позволяет упростить добавление сеанса для Город.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>Источник данных для представления таблицы

Каждое представление таблицы требуется источник данных (`UITableViewDataSource`) для предоставления данных для таблицы и создать необходимые строки как запрашиваемые таблицы представления.

В примере выше, щелкните правой кнопкой мыши имя проекта в **обозревателе решений**выберите **добавить** > **новый файл...**  и назовите его `AttractionTableDatasource` и нажмите кнопку **New** кнопку, чтобы создать. Далее следует изменить `AttractionTableDatasource.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Давайте взглянем на несколько частей класса подробно.

Во-первых мы определения константу, которая будет содержать уникальный идентификатор ячейки прототипа (это тот же идентификатор, назначенный в конструкторе интерфейса выше), добавлен ярлык контроллер представления таблиц и создании хранилища данных:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Далее мы сохранить контроллер представления таблиц, а затем сборка и заполнение нашему источнику данных (с помощью моделей данных, определенных выше) при создании класса:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Для примера `PopulateCities` метод просто создает объекты модели данных в памяти, однако они могут легко прочитать из базы данных или веб-службы в реальном приложении:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

`NumberOfSections` Метод возвращает число разделов в таблице:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Для **Plain** стиль таблицы представления, всегда возвращают значение 1.

`RowsInSection` Метод возвращает число строк в текущем разделе:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Опять же, для **Plain** таблицы представления, возвращают общее число элементов в источнике данных.

`TitleForHeader` Метод возвращает заголовок для заданного раздела:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Для **Plain** представление таблицы введите необходимо оставить заголовок пустым (`""`).

Наконец, при запросе таблицы представления, создать и заполнить ячейки прототипа с помощью `GetCell` метод: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Дополнительные сведения о работе с `UITableViewDatasource`, см. в разделе Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) документации.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Делегат представление таблицы

Каждое представление таблицы требуется делегат (`UITableViewDelegate`) реагировать на взаимодействие с пользователем или другие системные события в таблице.

В примере выше, щелкните правой кнопкой мыши имя проекта в **обозревателе решений**выберите **добавить** > **новый файл...**  и назовите его `AttractionTableDelegate` и нажмите кнопку **New** кнопку, чтобы создать. Далее следует изменить `AttractionTableDelegate.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

В сведениях о давайте рассмотрим несколько разделов этого класса.

Сначала нужно создать ярлык со ссылкой на контроллер представления таблиц при создании класса:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Затем, когда выбрана строка (пользователь нажимает Touch в области на удаленном Apple) мы хотим отметить **притяжения** представленной выбранной строки в список избранного:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Далее, когда пользователь выделяет строку (передав его с помощью Apple удаленного Touch области фокуса) необходимо предоставить сведения о **притяжения** представляемое данной строкой, в разделе "Сведения" наши контроллер разделенного представления:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

`CanFocusRow` Метод вызывается для каждой строки, который должен получить фокус в табличном представлении. Вернуть `true` Если строки можно получить фокус, в противном случае возвращает `false`. В нашем примере мы создали пользовательский `AttractionHighlighted` событие, которое будет вызываться для каждой строки, так как он получает фокус.

Дополнительные сведения о работе с `UITableViewDelegate`, см. в разделе Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) документации.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>Ячейка представления таблиц

Для каждой ячейки прототипа, вы добавили в представление таблицы в конструкторе интерфейса, вы также создали настраиваемый экземпляр ячейка представления таблиц (`UITableViewCell`) позволяет вам для заполнения новой ячейки (строка), так как оно создается.

Для примера приложения, дважды щелкните `AttractionTableCell.cs` файл, чтобы открыть его для редактирования и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Этот класс предоставляет хранилище для объекта модели данных притяжения (`AttractionInformation` как указано выше) отображается в данной строке:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

`UpdateUI` Метод заполняет пользовательского интерфейса мини-приложений (которые были добавлены к прототипу ячейки в конструкторе интерфейса) при необходимости:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Дополнительные сведения о работе с `UITableViewCell`, см. в разделе Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) документации.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Контроллер представления таблиц

Контроллер представления таблиц (`UITableViewController`) управляет представление таблицы, которая была добавлена в раскадровку с помощью конструктора интерфейса.

Для примера приложения, дважды щелкните `AttractionTableViewController.cs` файл, чтобы открыть его для редактирования и это должно выглядеть следующим образом:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Давайте более подробно рассмотрим этот класс. Во-первых, мы создали ярлыки для упрощения доступа к данному представлению таблицы `DataSource` и `TableDelegate`. Мы будем использовать их позже для обмена данными между представления таблицы в левой части разделенное представление и представление сведений справа.

Наконец, при загрузке представления таблицы в памяти, мы создаем экземпляры `AttractionTableDatasource` и `AttractionTableDelegate` (как созданной выше) и вложите их в табличном представлении.

Дополнительные сведения о работе с `UITableViewController`, см. в разделе Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) документации.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Как это все вместе

Как уже говорилось в начале этого документа, представления таблиц обычно отображаются в одной стороне [разделенное представление](~/ios/tvos/user-interface/split-views.md) как навигация с подробными сведениями выбранного элемента на противоположной стороне. Пример: 

[![](table-views-images/intro01.png "Запуск примера приложения")](table-views-images/intro01.png#lightbox)

Так как это стандартный шаблон в tvOS, давайте взглянем на заключительных этапах для их объединения и иметь левой и правой сторон представление с разделением, взаимодействуют друг с другом.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>Подробное представление

Для примера приложения travel представленные выше, пользовательский класс (`AttractionViewController`) определяется для стандартных контроллера представления, представленные в правой части разделенное представление в представлении «Подробности»:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

Здесь мы указан **притяжения** (`AttractionInformation`) отображается как свойство и создания `UpdateUI` метод, который заполняет виджеты пользовательского интерфейса добавляются в представление в конструкторе интерфейса.

Мы определили ярлык к контроллер разделенного представления (`SplitView`), мы будем использовать для обмена данными изменяет обратно к представлению таблицы (`AcctractionTableView`).

Наконец, настраиваемые действия (события) были добавлены три `UIButton` экземпляры, созданные в конструкторе интерфейса, который разрешает пользователю пометить притяжения как _избранные_, получить _направлениях_ для притяжения и _самолет_ заданного городе.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Контроллер представления навигации

Поскольку контроллер представления таблиц является вложенным в контроллер представления навигации в левой части представление с разделением, контроллер представления навигации был назначен пользовательский класс (`MasterNavigationController`) в конструкторе интерфейса и определяются следующим образом:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Опять же этот класс определяет только несколько сочетаний клавиш, чтобы упростить для обмена данными через две стороны контроллер разделенного представления:

* `SplitView` — Это ссылка на контроллер представления Split (`MainSpiltViewController`), к которой принадлежит контроллера представления навигации.
* `TableController` — Получает контроллер представления таблиц (`AttractionTableViewController`), представленными в виде верхней представления в контроллер представления навигации.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Контроллер разделенного представления

Поскольку контроллер разделенного представления является основанием наше приложение, мы создали пользовательский класс (`MasterSplitViewController`) для него в конструкторе интерфейса и она определена следующим образом:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

Во-первых, мы создавать ярлыки для **сведения** сторона разделенное представление (`AttractionViewController`) и **Master** стороне (`MasterNavigationController`). Опять же это упрощает для обмена данными между двумя сторонами позже.

Затем при представлении с разделением загружается в память, мы подключить контроллер разделенного представления для обеих сторон разделенное представление и реагировать на них пользователь выделение притяжения в табличном представлении (`AttractionHighlighted`), отображая новый притяжения в **сведения**  сторона разделенное представление.

См. в разделе [tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/) образец приложения для представления таблиц внутри разделенное представление является полной реализацией.

## <a name="table-views-in-detail"></a>Представления таблиц подробно

Поскольку tvOS создан на основе iOS, представления и таблицы контроллеров представления таблицы разработаны и ведут себя таким же образом. Более подробные сведения о работе с табличное представление в приложении Xamarin, см. наш iOS [работа с таблицами и ячейками](~/ios/user-interface/controls/tables/index.md) документации.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работать с представлениями таблиц внутри приложения Xamarin.tvOS. И представлен пример работы с представления таблицы в представление с разделением, являющийся типичное использование объекта таблицы представления в приложении tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
