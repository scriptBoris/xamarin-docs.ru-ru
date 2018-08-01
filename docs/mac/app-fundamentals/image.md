---
title: Образы в Xamarin.Mac
description: В этой статье рассматривается работа с изображениями и значков в приложении Xamarin.Mac. Он описывает создание и обслуживание образов, необходимые для создания значок приложения и использование изображений в код C# и в Xcode интерфейс построителя.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: ae0a42051d56eb8bf002c61dfbc60c99924ff301
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792100"
---
# <a name="images-in-xamarinmac"></a>Образы в Xamarin.Mac

_В этой статье рассматривается работа с изображениями и значков в приложении Xamarin.Mac. Он описывает создание и обслуживание образов, необходимые для создания значок приложения и использование изображений в код C# и в Xcode интерфейс построителя._

## <a name="overview"></a>Обзор

При работе с C# и .NET в приложении Xamarin.Mac, изображения и доступа значок средства, работающий в *Objective-C* и *Xcode* does.

Существует несколько способов, изображение, которое ресурсы используются внутри приложения macOS (прежнее название — Mac OS X). Просто отображать изображение как часть пользовательского интерфейса приложения, назначая его для элемента управления пользовательского интерфейса, такие как панели инструментов или элемента списка источника, значки, предоставляя Xamarin.Mac позволяет легко добавлять большой иллюстрации в macOS приложения одним из следующих способов : 

- **Элементы пользовательского интерфейса** -изображения могут быть отображены в рамках приложения в графическом режиме или фон (`NSImageView`).
- **Кнопка** -изображения могут быть отображены в кнопок (`NSButton`).
- **Изображения ячейки** - как часть элемента управления на основе таблицы (`NSTableView` или `NSOutlineView`), изображения, которые могут использоваться в ячейке изображения (`NSImageCell`).
- **Элемент панели инструментов** -изображения можно добавить на панель инструментов (`NSToolbar`) как элемент панели инструментов изображения (`NSToolbarItem`).
- **Значок списка источника** - как часть исходного списка (специально отформатированного `NSOutlineView`).
- **Значок приложения** -набора изображений могут быть сгруппированы в `.icns` установить и использовать в качестве значка приложения. См. наш [значок приложения](~/mac/deploy-test/app-icon.md) Дополнительные сведения см.

Кроме того macOS предоставляет набор стандартных образов, которые могут использоваться во всем приложении.

[![Пример запуска приложения](image-images/intro01.png "запуска примера приложения")](image-images/intro01-large.png#lightbox)

В этой статье мы обсудим основы работы с изображениями и значков в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Hello, Mac](~/mac/get-started/hello-mac.md) статью, во-первых, в частности [введение в Xcode и интерфейс построителя](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) и [выходов и действия](~/mac/get-started/hello-mac.md#Outlets_and_Actions) разделы, как он охватывает основные принципы и методы, которые будут использоваться в этой статье.


## <a name="adding-images-to-a-xamarinmac-project"></a>Добавление изображений в проект Xamarin.Mac

При добавлении изображения для использования в приложении Xamarin.Mac, существует несколько мест и способами, что разработчик может содержать файл изображения для исходного проекта:

- **Основной проект дерева [устарело]** -изображения можно добавить непосредственно в дерево проектов. При вызове изображения, хранимые в главный проект дерева из кода, местоположение папки не указано. Например, `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Папка resources [устарело]** -специальные **ресурсов** является папка для изображений (или любого других файла изображения или разработчик любой файл, который станет частью приложения объединить например значок, запустите экрана или общие желает добавить). При вызове изображения, хранимые в **ресурсов** папку из кода, как при использовании изображения хранятся в дереве основного проекта, местоположение папки не указано. Например, `NSImage.ImageNamed("tags.png")`.
- **Пользовательские папки или вложенной папки [устарело]** -разработчик можно добавлять пользовательские папки исходного дерева проектов и хранения образов существует. Расположение, где будет добавлен файл могут быть вложены во вложенной папке для дальнейшей организации проекта. Например, если разработчик добавили `Card` папки для проекта и вложенной папкой `Hearts` в этой папке, затем сохранить образ **Jack.png** в `Hearts` папке `NSImage.ImageNamed("Card/Hearts/Jack.png")` загружает изображение в Среда выполнения.
- **[Основной] наборы изображений каталога активов** — добавлены в Capitan El OS X **наборов изображений каталоги активов** содержат версии или представление изображения, необходимые для поддержки различных устройств и масштабировать факторы для вашей приложение. Не полагаясь на имя файла изображения активы (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Добавление изображений в образ ОС каталога значение

Как упоминалось выше, **наборов изображений каталоги активов** содержат версии или представление изображения, необходимые для поддержки различных устройств и масштабировать факторы для вашего приложения. Не полагаясь на имя файла изображения активы (см. независимых изображения с разрешением и номенклатуру изображения выше), **наборов изображений** укажите образ, который принадлежит какие устройства и/или разрешения с помощью редактора активов.

1. В **Pad решения**, дважды щелкните **Assets.xcassets** файл, чтобы открыть его для редактирования: 

    ![При выборе Assets.xcassets](image-images/imageset01.png "выбора Assets.xcassets")
2. Щелкните правой кнопкой мыши **списка активов** и выберите **новый набор изображения**: 

    [![Добавление нового набора изображений](image-images/imageset02.png "Добавление нового набора изображений")](image-images/imageset02-large.png#lightbox)
3. Выберите новый набор изображения и редакторе будет отображаться: 

    [![При выборе нового набора изображений](image-images/imageset03.png "при выборе нового набора изображений")](image-images/imageset03-large.png#lightbox)
4. Отсюда можно перетащить в изображениях для каждого из различных устройств и необходимые разрешения. 
5. Дважды щелкните новый набор изображения **имя** в **списка активов** , чтобы изменить его: 

    [![Имя набора редактирования изображения](image-images/imageset04.png "редактирования изображения имя набора данных")](image-images/imageset04-large.png#lightbox)
    
Специальный **вектор** как были добавлены **наборов изображений** , позволяющий включать _PDF_ векторный рисунок в casset, вместо этого, включая файлы отдельных точечного рисунка в формате различные способы их устранения. При использовании этого метода указать файл один вектор для **@1x** разрешение (в формате PDF-файла вектор) и **@2x** и **@3x** версии файла будут создаваться во время компиляции и включенные в пакет приложения.

[![Изображение настроить интерфейс редактора](image-images/imageset05.png "набор интерфейс редактора изображений")](image-images/imageset05-large.png#lightbox)

Например, при включении `MonkeyIcon.pdf` файл как вектор из каталога активов с разрешением x 150px 150 пикс., следующие активы должны быть включены в пакет окончательного приложения при его компиляции точечный рисунок:

1. **MonkeyIcon@1x.png** -x 150px разрешение 150 пикс.
2. **MonkeyIcon@2x.png** -x 300px разрешение 300 пикс.
3. **MonkeyIcon@3x.png** -450px x 450px разрешения.

Ниже следует принять во внимание при использовании PDF векторных изображений в каталогах активов:

- Это не поддерживается полный вектор как PDF-ФАЙЛ будет растеризованного к растровому изображению во время компиляции и растровые изображения, поставляется в конечного приложения.
- Размер изображения не может настроить после установки в каталоге активов. При попытке изменить размер изображения (или в коде или с помощью автоматический макет и размер классы) изображение будет искажен так же, как другие растрового изображения.

При использовании **задать изображение** в построителе интерфейса в Xcode, можно просто выбрать имя набора из раскрывающегося списка в **инспектора атрибут**: **

![Выбора изображения задание в Xcode интерфейс построителя](image-images/imageset06.png "выбора изображения задайте в построителе интерфейса в Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Добавление новых коллекций активы

При работе с изображениями в каталогах активы возможны ситуации, если вы хотите создать новую коллекцию, вместо добавления все рисунки, **Assets.xcassets** коллекции. Например, при разработке ресурсы по требованию.

Чтобы добавить новый каталог активы проекта:

1. Правой кнопкой мыши проект в **Pad решения** и выберите **добавить** > **новый файл...**
2. Выберите **Mac** > **каталога активов**, введите **имя** коллекции и нажмите кнопку **New** кнопки: 

    ![Добавление нового каталога активов](image-images/asset01.png "Добавление нового каталога активов")

Здесь можно работать с коллекцией таким же образом, по умолчанию **Assets.xcassets** коллекцию, автоматически включаются в проект.


### <a name="adding-images-to-resources"></a>Добавление изображений к ресурсам

> [!IMPORTANT]
> Работа с изображениями в приложении macOS этот метод является устаревшим в Apple. Следует использовать [наборов изображений активов каталога](#asset-catalogs) диспетчеру приложения вместо изображения.

Прежде чем использовать файл изображения в приложении Xamarin.Mac (или в коде C#, или из интерфейса построителя) его необходимо включить в проекте **ресурсов** папке, что **пакета ресурсов**. Чтобы добавить файл в проект, выполните следующие действия:

1. Щелкните правой кнопкой мыши **ресурсов** в папке проекта в **Pad решения** и выберите **добавить** > **добавить файлы...** : 

    ![Добавление файла](image-images/add01.png "Добавление файла")
2. Из **добавить файлы** диалоговое окно, выберите файлы изображений для добавления в проект, установите `BundleResource` для **действие построения переопределение** и нажмите кнопку **откройте** кнопки:

    [![Выбор файлов для добавления](image-images/add02.png "выбора файлов для добавления")](image-images/add02-large.png#lightbox)
3. Если файлы уже находятся в **ресурсов** папки, запрашивается необходимость **копирования**, **переместить** или **ссылку** файлы. Выбрать которой каждого средства вашим потребностям, как правило, будет **копирования**:

    ![Выбрав действие add](image-images/add04.png "выбрав действие add")
4. Новые файлы будут включены в проект и считывать для использования: 

    ![Новые файлы изображений, панель решения добавлена возможность](image-images/add03.png "панель решения добавлена возможность новые файлы изображений")
5. Повторите процесс для все необходимые файлы изображений.

Можно использовать любой png, jpg или pdf-файл как образ источника Xamarin.Mac приложения. В следующем разделе мы рассмотрим добавлении высокого разрешения версий наших изображений и значков для поддержки Сетчатка на основе компьютеров Mac.

> [!IMPORTANT]
> При добавлении изображения, которые **ресурсов** папки, можно оставить **действие построения переопределение** значение **по умолчанию**. Значение по умолчанию — действие построения для этой папки `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Укажите утвержденными графических ресурсов, все приложения

Любой графических средств, добавить к приложению Xamarin.Mac (значки, пользовательские элементы управления, пользовательские курсоры, пользовательские иллюстрации, т. д.) должны иметь версиям с высоким разрешением в дополнение к их версии standard разрешения. Это необходимо, чтобы ваше приложение будет выглядеть мы рекомендуем выполнить дисплеем Retina доступом к компьютеру Mac.


### <a name="adopt-the-2x-naming-convention"></a>Адаптация @2x соглашение об именовании

> [!IMPORTANT]
> Работа с изображениями в приложении macOS этот метод является устаревшим в Apple. Следует использовать [наборов изображений активов каталога](#asset-catalogs) диспетчеру приложения вместо изображения.

При создании стандартных и с высоким разрешением версии образа, выполните это соглашение об именовании для пары «изображения», при включении их в проекте Xamarin.Mac.

- **Стандартное разрешение**  - **ImageName.filename расширения** (пример: **tags.png**)
- **С высоким разрешением**   -  **ImageName@2x.filename-extension** (пример: **tags@2x.png**)

При добавлении в проект, они будут выглядеть следующим образом:

![Файлы изображений в панель решения](image-images/add03.png "файлов изображений в панель решения")

При назначении изображения элемента пользовательского интерфейса в интерфейс построителя необходимо будет выбрать файл в _ImageName_**.** _расширение имени файла_ формат (пример: **tags.png**). То же самое для с помощью образа в коде C#, будет выбрать файл в _ImageName_**.** _расширение имени файла_ формат.

Если вы Xamarin.Mac приложение запускается на Mac, _ImageName_**.** _расширение имени файла_ на стандартный вывод разрешения, будет использоваться изображение формата **ImageName@2x.filename-extension** изображение происходит выборка дисплей Retina оснований компьютеров Mac.


## <a name="using-images-in-interface-builder"></a>Использование изображений в интерфейс построителя

Любой ресурс изображения, добавления **ресурсов** папку в вашей Xamarin.Mac проекта и задание действие построения **BundleResource** автоматически будут отображаться в построителе интерфейса и может быть выбран как часть элемента пользовательского интерфейса (если обработки изображений).

Чтобы использовать изображение в построителе интерфейса, выполните следующие действия:

1. Добавить изображение в **ресурсов** папка с **действие при построении** из `BundleResource`: 

     ![Ресурс изображения в панель решения](image-images/ib00.png "ресурса изображения в панель решения")
2. Дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в построителе интерфейса: 

     [![Изменение основного раскадровки](image-images/ib01.png "редактирования основного раскадровки")](image-images/ib01-large.png#lightbox)
3. Перетащите элемент пользовательского интерфейса, который принимает изображения на поверхность разработки (например, **элементом панели инструментов изображения**): 

     ![Изменение элемента панели инструментов](image-images/ib02.png "редактирование элементов панели инструментов")
4. Выберите образ, добавленную в **ресурсов** папки в **имя образа** раскрывающегося списка: 

     [![При выборе изображения для элемента панели инструментов](image-images/ib03.png "выбора изображения для элемента панели инструментов")](image-images/ib03-large.png#lightbox)
5. Выбранное изображение отображается в области конструктора: 

     ![Изображения, отображаемого в редакторе панелей инструментов](image-images/ib04.png "изображения, отображаемого в редакторе панелей инструментов")
6. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации с Xcode.

Указанные выше шаги подойдет любой элемент пользовательского интерфейса, который позволяет их должно быть задано свойство изображения **инспектора атрибут**. Еще раз Если вы включили **@2x** версии файла изображения, он будет автоматически использоваться на компьютерах Mac, на основе дисплей Retina.

> [!IMPORTANT]
> Если изображение недоступно в **имя образа** раскрывающийся список, закройте проект .storyboard в Xcode и снова откройте его из Visual Studio для Mac. Если изображение по-прежнему недоступна, убедитесь, что его **действие при построении** — `BundleResource` и что образ будет добавлен к **ресурсов** папки.

## <a name="using-images-in-c-code"></a>Использование изображений в коде C#

При загрузке изображения в памяти с помощью кода C# в приложении Xamarin.Mac, изображение будет храниться в `NSImage` объекта. Если файл изображения был включен в пакете приложения Xamarin.Mac (включен в ресурсы), используйте следующий код для загрузки изображения:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Приведенный выше код используется статическое `ImageNamed("...")` метод `NSImage` класс для загрузки в память из заданного изображения **ресурсов** папке, если не удается найти изображение, `null` будут возвращены. Изображения, назначенный в построителе интерфейса, например если вы включили **@2x** версии файла изображения, он будет автоматически использоваться на дисплей Retina на основе компьютеров Mac.

Чтобы загрузить изображения за пределами пакета приложения (из файловой системы Mac), используйте следующий код:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Работа с образами шаблонов

На основании конструирования macOS приложения, возможны ситуации, когда требуется настроить значка или изображения в пользовательский интерфейс для соответствия Изменение цветовой схемы (например, на основе параметров пользователя).

Чтобы этого добиться, переключитесь _режим отрисовки_ из образа актива в **образ шаблона**:

[![Изображение шаблона](image-images/templateimage01.png "изображение шаблона")](image-images/templateimage01-large.png#lightbox)

В построителе интерфейс Xcode назначьте ресурса изображения элемента управления пользовательского интерфейса:

![Выбор изображения в Xcode интерфейс построителя](image-images/templateimage02.png "выбора изображения в построителе интерфейса в Xcode")

Также при необходимости задать источник изображения в коде:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Добавьте следующие открытые функцию контроллер представление:

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

Наконец, оттенка образ шаблона, эта функция вызывается для изображения для выделения цветом.

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Использование изображений с представления таблиц

Включить изображение в рамках ячейки в `NSTableView`, вам потребуется изменить как данные возвращаются в табличном представлении `NSTableViewDelegate's` `GetViewForItem` метод, используемый `NSTableCellView` вместо стандартное `NSTextField`. Пример:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Есть несколько строк интерес. Во-первых, для столбцов, которые нужно включить изображение, создается новый `NSImageView` требуемый размер и расположение, также создается новый `NSTextField` и поместите его положение по умолчанию, в зависимости от того, является ли использование изображения:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Во-вторых, нам нужно включать новые представления изображения и текстового поля в родительском объекте `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Наконец необходимо сказать текстовое поле, можно сжать и с ростом ячейки таблицы представления:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Пример выходных данных:

[![Пример отображения изображения в приложении](image-images/tables01.png "пример отображения изображения в приложении")](image-images/tables01-large.png#lightbox)

Дополнительные сведения о работе с представлениями таблицы см. в разделе нашей [представления таблиц](~/mac/user-interface/table-view.md) документации.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Использование изображений с представления структуры

Включить изображение в рамках ячейки в `NSOutlineView`, вам потребуется изменить как данные возвращаются в представлении структуры `NSTableViewDelegate's` `GetView` метод, используемый `NSTableCellView` вместо стандартное `NSTextField`. Пример:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Есть несколько строк интерес. Во-первых, для столбцов, которые нужно включить изображение, создается новый `NSImageView` требуемый размер и расположение, также создается новый `NSTextField` и поместите его положение по умолчанию, в зависимости от того, является ли использование изображения:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Во-вторых, нам нужно включать новые представления изображения и текстового поля в родительском объекте `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Наконец необходимо сказать текстовое поле, можно сжать и с ростом ячейки таблицы представления:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Пример выходных данных:

[![Пример изображения, отображаемого в представлении структуры](image-images/outline01.png "примером изображения, отображаемого в представлении структуры")](image-images/outline01-large.png#lightbox)

Дополнительные сведения о работе с представления структуры см. в разделе нашей [представления структуры](~/mac/user-interface/outline-view.md) документации.


## <a name="summary"></a>Сводка

Подробное описание работы с изображениями и значки, в приложении Xamarin.Mac вступила в этой статье. Мы уже видели различных типов и использует образы, как использовать изображения и значки в построителе интерфейс Xcode и способы работы с изображениями и значков в коде C#.



## <a name="related-links"></a>Связанные ссылки

- [MacImages (пример)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Представления таблиц](~/mac/user-interface/table-view.md)
- [Представления структуры](~/mac/user-interface/outline-view.md)
- [macOS X рекомендациям по интерфейсам](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Сведения о высоком разрешении для OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
