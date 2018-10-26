---
title: Образы в Xamarin.Mac
description: В этой статье рассматривается работа с изображениями и значками в приложении Xamarin.Mac. Он описывает создание и обслуживание образов, необходимых для создания значок приложения и использование изображений в коде C# и конструктора Interface Builder.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 8df7e14088486d0eff9a6370303e83c5e69d4484
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119108"
---
# <a name="images-in-xamarinmac"></a>Образы в Xamarin.Mac

_В этой статье рассматривается работа с изображениями и значками в приложении Xamarin.Mac. Он описывает создание и обслуживание образов, необходимых для создания значок приложения и использование изображений в коде C# и конструктора Interface Builder._

## <a name="overview"></a>Обзор

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к одного образа и значок средства, работающий в *Objective-C* и *Xcode* does.

Существует несколько способов этого образа, ресурсы, используемые внутри приложения macOS (прежнее название Mac OS X). Просто отображать изображения как часть пользовательского интерфейса приложения, присвоив его к элементу управления пользовательского интерфейса, такие как панели инструментов или элемента списка в источнике, предоставляя значки, Xamarin.Mac позволяет легко добавлять полезные иллюстрации в приложения macOS одним из следующих способов : 

- **Элементы пользовательского интерфейса** -изображения могут отображаться как фоновые рисунки или как часть приложения в виде изображения (`NSImageView`).
- **Кнопка** -изображения могут отображаться в кнопках (`NSButton`).
- **Изображения ячейки** — в рамках элемента управления на основе таблицы (`NSTableView` или `NSOutlineView`), изображения, которые могут использоваться в ячейке образа (`NSImageCell`).
- **Элемент панели инструментов** -образы, которые могут добавляться на панель инструментов (`NSToolbar`) как элемент панели инструментов образа (`NSToolbarItem`).
- **Исходный значок списка** — как часть исходного списка (специальный формат `NSOutlineView`).
- **Значок приложения** -ряд образов могут быть сгруппированы в `.icns` устанавливаться и использоваться в качестве значка приложения. См. в разделе наших [значок приложения](~/mac/deploy-test/app-icon.md) Дополнительные сведения см.

Кроме того macOS предоставляет набор стандартных изображений, которые могут использоваться в приложении.

[![Запустите пример приложения](image-images/intro01.png "Запустите пример приложения")](image-images/intro01-large.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с изображениями и значками в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.


## <a name="adding-images-to-a-xamarinmac-project"></a>Добавление изображений в проект Xamarin.Mac

При добавлении изображения для использования в приложении Xamarin.Mac, существует несколько мест и способов, что разработчик может включить файл изображения для исходного проекта:

- **[Устарело] дерева основного проекта** -непосредственно к дереву проектов можно добавлять изображения. При вызове образы, хранящиеся в дереве основного проекта из кода, местоположение папки, не указано. Например, `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **[Устарело] Папка resources** -специальные **ресурсы** папка — для любого файла, который должен стать частью приложения объединить например значок, экран запуска или общие образы (или любого других образ файла или разработчик хочет добавить). При вызове изображения, хранящиеся в **ресурсы** папку из кода, подобно тому, как образы хранятся в дереве основного проекта, местоположение папки, не указано. Например, `NSImage.ImageNamed("tags.png")`.
- **Пользовательские папки или вложенной папки, которые [устарело]** -разработчика можно добавить пользовательскую папку исходного дерева проектов и хранить изображения существует. Расположение, где он добавляется могут быть вложенными в подпапку, чтобы получить дополнительную помощь при организации проекта. Например, если разработчик добавили `Card` папку для проекта и вложенной папки `Hearts` в этой папке, затем сохранить образ **Jack.png** в `Hearts` папке `NSImage.ImageNamed("Card/Hearts/Jack.png")` загружает изображение в Среда выполнения.
- **Каталог активов: [Основной] наборы изображений** — добавлена в OS X El Capitan, **наборы изображений каталоги активов** содержат всех версий или представление изображения, которые необходимы для поддержки различных устройств и коэффициенты для масштабирования вашего приложение. А не полагаться на имя файла изображения для средств (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Добавление изображений к изображению каталог активов набор

Как уже говорилось, **наборы изображений каталоги активов** содержат всех версий или представление изображения, которые необходимы для поддержки различных устройств и масштабировать факторов для вашего приложения. А не полагаться на имя файла изображения для ресурсов (см. в разделе, независимые изображения с разрешением и номенклатуру изображение выше), **наборы изображений** позволяет указать, какой образ принадлежит какие устройства или разрешение редактор ресурсов.

1. В **панели решения**, дважды щелкните **Assets.xcassets** файл, чтобы открыть его для редактирования: 

    ![Выбрав Assets.xcassets](image-images/imageset01.png "выбора Assets.xcassets")
2. Щелкните правой кнопкой мыши **списка "ресурсы"** и выберите **новый набор изображений**: 

    [![Добавление нового набора изображений](image-images/imageset02.png "Добавление нового набора изображений")](image-images/imageset02-large.png#lightbox)
3. Выберите новый набор изображений и редактора будет отображаться: 

    [![Выбрав новый набор изображений](image-images/imageset03.png "выбрав новый набор изображений")](image-images/imageset03-large.png#lightbox)
4. Отсюда можно перетащить в образы для каждого из различных устройств и необходимые разрешения. 
5. Дважды щелкните новый набор изображений **имя** в **списка "ресурсы"** для редактирования: 

    [![Имя набора редактирования изображения](image-images/imageset04.png "редактирования изображения имя набора")](image-images/imageset04-large.png#lightbox)
    
Специальный **вектор** как были добавлены **наборы изображений** , позволяющий включать _PDF_ векторного изображения в casset, вместо этого в том числе файлы отдельных растрового изображения в формате различные способы их устранения. При использовании этого метода указать файл один вектор для **@1x** разрешение (в формате PDF-файла вектор) и **@2x** и **@3x** версии файла будут создаваться во время компиляции и включенные в пакет приложения.

[![Изображение задать интерфейс редактора](image-images/imageset05.png "изображение задать интерфейс редактора")](image-images/imageset05-large.png#lightbox)

Например, если включить `MonkeyIcon.pdf` файл как вектор каталог активов с разрешением x 150px 150px, следующие растрового изображения, ресурсы должны быть включены в готовом приложении пакета при выполнении компиляции:

1. **MonkeyIcon@1x.png** -разрешение x 150px 150 пикс.
2. **MonkeyIcon@2x.png** -300px x 300px разрешения.
3. **MonkeyIcon@3x.png** -450px x 450px разрешения.

Ниже следует принимать во внимание при использовании PDF векторных изображений в каталоги активов:

- Это не поддерживается полный вектор, так как PDF-ФАЙЛ будет растровое точечного рисунка во время компиляции и точечные рисунки, поставляемых в конечном приложении.
- Невозможно изменить размер изображения, после его установки в каталоге ресурсов. При попытке изменить размер изображения (либо в коде или с помощью автоматического макета и размеры) изображение будет искажен так же, как и любой точечный рисунок.

При использовании **образа наборе** в Interface Builder в Xcode, можно просто выбрать имя набора из раскрывающегося списка в **инспекторе атрибутов**: **

![Выбрав изображение задать в Interface Builder в Xcode](image-images/imageset06.png "выбора изображения задать в Interface Builder в Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Добавление новых коллекций ресурсов

При работе с изображениями в каталоги активов возможны ситуации, когда вы хотите создать новую коллекцию, вместо добавления все рисунки, **Assets.xcassets** коллекции. Например, при разработке ресурсы по запросу.

Чтобы добавить новый каталог ресурсов в проект:

1. Щелкните правой кнопкой мыши проект в **панели решения** и выберите **добавить** > **новый файл...**
2. Выберите **Mac** > **каталог активов**, введите **имя** коллекции и нажмите кнопку **New** кнопки: 

    ![Добавление новый каталог ресурсов](image-images/asset01.png "Добавление новый каталог ресурсов")

Отсюда можно работать с коллекцией в так же, как значение по умолчанию **Assets.xcassets** коллекции автоматически включается в проект.


### <a name="adding-images-to-resources"></a>Добавление изображений к ресурсам

> [!IMPORTANT]
> Этот метод работы с изображениями в приложение macOS устаревшим компанией Apple. Следует использовать [наборы изображений каталога активов](#asset-catalogs) диспетчеру приложения вместо изображения.

Перед использованием в файл изображения в приложении Xamarin.Mac (либо в коде C#, либо из конструктора Interface Builder) он должен быть включен в проекте **ресурсы** папке, что **пакета ресурсов**. Чтобы добавить файл в проект, сделайте следующее:

1. Щелкните правой кнопкой мыши **ресурсы** в проекте в **панели решения** и выберите **добавить** > **добавить файлы...** : 

    ![Добавление файла](image-images/add01.png "Добавление файла")
2. Из **добавить файлы** диалоговом окне выберите файлы изображений для добавления в проект, выберите `BundleResource` для **переопределение действия по построению** и нажмите кнопку **откройте** кнопки:

    [![Выбор файлов для добавления](image-images/add02.png "выбора файлов для добавления")](image-images/add02-large.png#lightbox)
3. Если файлы уже находятся в **ресурсы** папки, появляется необходимость **копирования**, **переместить** или **ссылку** файлы. Выбрать которой каждой масти вашим потребностям, как правило, которые будут **копирования**:

    ![При выборе действия add](image-images/add04.png "выбрав добавить действие")
4. Новые файлы будут включены в проекте и чтение для использования: 

    ![Новый образ файлов, добавленных в панели решения](image-images/add03.png "новых файлов изображений, добавляемый на панели решения")
5. Повторите этот процесс для всех файлов изображений, необходимых.

Можно использовать любой png, jpg или PDF-файл как исходного изображения в приложении Xamarin.Mac. В следующем разделе мы рассмотрим добавление версий высокого разрешения из наших образов и значки для поддержки Retina на основе компьютеров Mac.

> [!IMPORTANT]
> При добавлении изображения **ресурсы** папки, можно оставить **переопределение действия по построению** присвоено **по умолчанию**. По умолчанию используется действие сборки для этой папки `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Укажите всех графических ресурсов приложения в высоком разрешении

Графические ресурсы, добавленном в приложении Xamarin.Mac (значки, пользовательские элементы управления, пользовательские курсоры, пользовательский объект, и т.д.) должны иметь версиям с высоким разрешением в дополнение к их версий стандартное разрешение. Это необходимо, чтобы приложение будет выглядеть старается выполнения на дисплей Retina где есть доступ к компьютеру Mac.


### <a name="adopt-the-2x-naming-convention"></a>Внедрение @2x соглашение об именовании

> [!IMPORTANT]
> Этот метод работы с изображениями в приложение macOS устаревшим компанией Apple. Следует использовать [наборы изображений каталога активов](#asset-catalogs) диспетчеру приложения вместо изображения.

При создании версий standard и с высоким разрешением изображения, выполните это соглашение об именовании для пары «изображения», при включении их в проект Xamarin.Mac.

- **Стандартное разрешение**  - **ImageName.filename-extension** (пример: **tags.png**)
- **С высоким разрешением**   -  **ImageName@2x.filename-extension** (пример: **tags@2x.png**)

При добавлении в проект, они будет выглядеть следующим образом:

![Файлы изображений на панели решения](image-images/add03.png "файлов изображений на панели решения")

При назначении изображения к элементу пользовательского интерфейса в конструктор Interface Builder вы просто выберу файла в _ImageName_**.** _расширение_имени_файла_ формате (пример: **tags.png**). Одинаково для с помощью образа в коде C#, выберу его в _ImageName_**.** _расширение имени файла_ формат.

При запуске на компьютере Mac, вы приложения Xamarin.Mac _ImageName_**.** _расширение имени файла_ на стандартный вывод разрешения, будет использоваться изображение формата **ImageName@2x.filename-extension** образа происходит выборка дисплей Retina баз компьютеры Макинтош.


## <a name="using-images-in-interface-builder"></a>Использование изображений в конструктор Interface Builder

Любой ресурс образа, добавления **ресурсы** папку в вашей Xamarin.Mac проекта и задайте действие сборки **BundleResource** автоматически будут отображаться в конструкторе Interface Builder и может быть выбраны как часть элемента пользовательского интерфейса (если обработки изображений).

Чтобы использовать изображение в конструктор interface builder, сделайте следующее:

1. Добавить изображение в **ресурсы** папка с **действие при построении** из `BundleResource`: 

     ![Ресурс изображения на панели решения](image-images/ib00.png "ресурс изображения на панели решения")
2. Дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в конструкторе Interface Builder: 

     [![Изменение основной раскадровки](image-images/ib01.png "редактирования основной раскадровки")](image-images/ib01-large.png#lightbox)
3. Перетащите элемент пользовательского интерфейса, который принимает изображения в область конструктора (например, **элемент панели инструментов изображения**): 

     ![Редактирование элементов панели инструментов](image-images/ib02.png "редактирование элементов панели инструментов")
4. Выберите образ, который вы добавили **ресурсы** папку в **имя образа** раскрывающегося списка: 

     [![Выбрав изображение для элемента панели инструментов](image-images/ib03.png "выбора изображения для элемента панели инструментов")](image-images/ib03-large.png#lightbox)
5. Выбранный образ отображается в области конструктора. 

     ![Изображения, отображаемого в редактор панелей инструментов](image-images/ib04.png "изображения, отображаемого в редактор панелей инструментов")
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Описанные выше действия работать для любого элемента пользовательского интерфейса, который позволяет их свойство образ для установки **инспекторе атрибутов**. Опять же если вы включили **@2x** версию файла образа, он будет автоматически использоваться на дисплей Retina на основе компьютеры Макинтош.

> [!IMPORTANT]
> Если изображение недоступно в **имя образа** раскрывающийся список, закройте проект .storyboard в Xcode и снова откройте его из Visual Studio для Mac. Если изображение по-прежнему недоступна, убедитесь, что его **действие при построении** — `BundleResource` и что образ был добавлен **ресурсы** папки.

## <a name="using-images-in-c-code"></a>Использование изображений в коде C#

При загрузке изображения в памяти, с помощью кода C# в приложении Xamarin.Mac, изображение будет храниться в `NSImage` объекта. Если файл изображения включена в пакет приложений Xamarin.Mac (входит в ресурсы), используйте следующий код для загрузки изображения:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Приведенный выше код использует статичный `ImageNamed("...")` метод `NSImage` класс для загрузки в память из заданного изображения **ресурсы** папке, если не удается найти изображение, `null` будут возвращены. Образы, назначенные в конструктор Interface Builder, например, если вы включили **@2x** версию файла образа, он будет автоматически использоваться на дисплей Retina на основе компьютеров Mac.

Чтобы загрузить изображения за пределами пакета приложения (из файловой системы Mac), используйте следующий код:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Работа с образами шаблонов

На основании разработки приложения macOS, возможны ситуации при необходимости настроить значок или изображение в пользовательском интерфейсе в соответствии с изменением в цветовой схеме (например, на основе предпочтений пользователя).

Чтобы этого добиться, переключитесь _режим рендеринга_ из ресурса образа **образ шаблона**:

[![Установка образа шаблона](image-images/templateimage01.png "параметр образ шаблона")](image-images/templateimage01-large.png#lightbox)

Из конструктора Interface Builder назначение ресурса изображения к элементу управления пользовательского интерфейса:

![Выбор изображения в Interface Builder в Xcode](image-images/templateimage02.png "выбора изображения в Interface Builder в Xcode")

Или при необходимости задайте источник изображения в коде:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Добавьте следующую функцию, открытый в контроллер представления:

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

Наконец, оттенка образ шаблона, эта функция вызывается с образом для выделения цветом.

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Использование образов с помощью представления таблиц

Для включения изображения как часть ячейки в `NSTableView`, вам потребуется изменить как данных, возвращаемых методом в представлении таблицы `NSTableViewDelegate's` `GetViewForItem` метод, используемый `NSTableCellView` вместо обычного `NSTextField`. Пример:

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

Существует несколько строк и интересуют. Во-первых, для столбцов, для которых нужно включить изображение, мы создадим новый `NSImageView` требуемый размер и расположение, мы также создадим новый `NSTextField` и поместите его положения по умолчанию, в зависимости от того, является ли мы используем образ:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Во-вторых, необходимо включить новые представление изображений и текстового поля в родительском объекте `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Наконец нам нужно сообщить текстового поля, что его можно сжать и развиваться вместе с ячейка представления таблиц:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Пример выходных данных:

[![Пример отображения изображения в приложении](image-images/tables01.png "пример отображения изображения в приложении")](image-images/tables01-large.png#lightbox)

Дополнительные сведения о работе с представлениями таблиц см. в разделе наших [представления таблиц](~/mac/user-interface/table-view.md) документации.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Использование образов с представлениями структур

Для включения изображения как часть ячейки в `NSOutlineView`, вам потребуется изменить как данных, возвращаемых методом представления структуры `NSTableViewDelegate's` `GetView` метод, используемый `NSTableCellView` вместо обычного `NSTextField`. Пример:

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

Существует несколько строк и интересуют. Во-первых, для столбцов, для которых нужно включить изображение, мы создадим новый `NSImageView` требуемый размер и расположение, мы также создадим новый `NSTextField` и поместите его положения по умолчанию, в зависимости от того, является ли мы используем образ:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Во-вторых, необходимо включить новые представление изображений и текстового поля в родительском объекте `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Наконец нам нужно сообщить текстового поля, что его можно сжать и развиваться вместе с ячейка представления таблиц:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Пример выходных данных:

[![Пример изображения, отображаемого в представлении структуры](image-images/outline01.png "пример изображения, отображаемого в представлении структуры")](image-images/outline01-large.png#lightbox)

Дополнительные сведения о работе с представлениями структур см. в разделе наших [представления структуры](~/mac/user-interface/outline-view.md) документации.


## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с изображениями и значками в приложении Xamarin.Mac. Мы видели различные типы и использует образы, как использовать изображения и значки в Interface Builder в Xcode и способы работы с изображениями и значками в коде C#.



## <a name="related-links"></a>Связанные ссылки

- [MacImages (пример)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Представления таблиц](~/mac/user-interface/table-view.md)
- [Представления структуры](~/mac/user-interface/outline-view.md)
- [macOS X рекомендациям по интерфейсам](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Сведения о высоком разрешении для OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
