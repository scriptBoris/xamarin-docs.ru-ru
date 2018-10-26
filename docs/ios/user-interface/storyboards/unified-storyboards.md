---
title: Унифицированная раскадровка в Xamarin.iOS
description: В этом документе описывается унифицированная раскадровка в Xamarin.iOS. Унифицированная раскадровка позволяют разработчикам для поддержки разных размеров экрана с определением единый интерфейс.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 26aeaa3d230a5c104014edd899b8d9231ced31e9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108526"
---
# <a name="unified-storyboards-in-xamarinios"></a>Унифицированная раскадровка в Xamarin.iOS

iOS 8 включает в себя новый, более простой в использовании механизм для создания пользовательского интерфейса — унифицированной раскадровки. С помощью одного раскадровки, чтобы охватить все размеры экрана другое оборудование, быстрый и гибкий представления могут создаваться в «разработки-один раз, используйте ко многим» стиль.

Как разработчику больше не требуется создавать отдельный и конкретные раскадровки для устройств iPhone и iPad, они имеют возможность разрабатывать приложения с общий интерфейс, а затем настройте этот интерфейс для классов другого размера. Таким образом приложение может быть адаптирован к сильные стороны каждого форм-фактор и каждого пользовательского интерфейса могут быть настроены для обеспечения удобства работы.

<a name="size-classes" />

## <a name="size-classes"></a>Классы размера

До iOS 8, разработчик использовал `UIInterfaceOrientation` и `UIInterfaceIdiom` для различения между режимами альбомной и книжной ориентации, а также между устройств iPhone и iPad. В iOS8, ориентации и устройства определяется с помощью *классы размера*.

Устройства определяются классы размера в вертикальной и горизонтальной оси, и существует два типа классов размер в iOS 8:

-  **Регулярные** — это размер большого экрана (таких как iPad) или мини-приложение, которое создает впечатление большого размера (такие как `UIScrollView`
-  **Compact** – это для небольших устройств (например, iPhone). Этот размер учитывает ориентации устройства.


Если эти два понятия используются совместно, результат — это сетка 2 x 2, определяет различные возможные размеры, которые могут использоваться в обоих разные ориентации, как показано на следующей схеме:

 [![](unified-storyboards-images/sizeclassgrid.png "Сетка 2 x 2, определяет различные возможные размеры, которые могут использоваться в обычном режиме и Compact ориентации")](unified-storyboards-images/sizeclassgrid.png#lightbox)

Разработчик может создать контроллер представления, в котором используется любой из четырех возможности, которые могут привести различные макеты (как показано на рисунке выше).

### <a name="ipad-size-classes"></a>iPad классы размера

IPad, из-за размера, имеет **регулярных** класса размер оба варианта расположения.

 [![](unified-storyboards-images/image1.png "iPad классы размера")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone классы размера

IPhone содержит классы другого размера, в зависимости от ориентации устройства:

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone классы размера")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Когда устройство находится в книжной ориентации, экран содержит **compact** класса по горизонтали и **регулярных** по вертикали
-  Когда устройство находится в альбомной ориентации, классы экрана меняются от книжной ориентации.

### <a name="iphone-6-plus-size-classes"></a>Классы размера iPhone 6 Plus

Размеры отличаются от более ранних устройствах iPhone в книжной ориентации, но разные в альбомной ориентации:

[![](unified-storyboards-images/iphone6sizeclasses.png "Классы размера iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Так как iPhone 6 Plus имеет достаточно большой экран, это может быть обычный класс размера ширины в альбомной ориентации.

### <a name="support-for-a-new-screen-scale"></a>Поддержка нового масштабируемого экрана

IPhone 6 Plus использует новый график Retina HD с коэффициентом масштабирования экрана 3.0 (три раза исходного iPhone разрешения экрана). Чтобы обеспечить наилучшее воспроизведение на этих устройствах, включают графический объект предназначен для такого масштаба экрана. В Xcode 6 и более поздних версий, средства каталогов может включать изображения на 1 x, 2 x и 3 размеров x; просто добавьте новые графические активы и операций ввода-вывода выберете соответствующих ресурсов при работе на iPhone 6 Plus.

Изображение, режим в iOS загрузки также распознает `@3x` суффиксом в виде файлов образа. Например, если разработчик содержит ресурс изображения (с разными разрешениями) в пакет приложения с помощью следующие имена файлов: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, и `MonkeyIcon@3x.png`. На iPhone 6 Plus `MonkeyIcon@3x.png` образ будет использоваться автоматически, если разработчик загружает изображение, используя следующий код:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
Или если они назначены изображения к элементу пользовательского интерфейса, с помощью iOS конструктора как `MonkeyIcon.png`, `MonkeyIcon@3x.png` будет использовать автоматически, на iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Экраны запуска динамической

В ходе запускает приложение iOS отзыв для пользователя, которого приложение фактически начиная сверху файле экрана запуска отображается экран-заставка. До iOS 8, разработчику придется включать несколько `Default.png` ресурсы для каждого устройства типа, ориентации и разрешения экрана, приложение будет выполняться на изображений.

Новое для iOS 8, разработчик может создать один atomic `.xib` файл в Xcode, автоматический макет и размер классов для создания *динамического экран запуска* , будет работать для всех устройств, разрешения и ориентации. Это не только снижает объем работ, необходимый разработчика создавать и обслуживать все необходимые графические активы, но уменьшает объем установленного пакета приложения.

## <a name="traits"></a>Признаки

Признаки являются свойствами, которые можно использовать для определения того, как изменяется макет при его изменении среды. Они состоят из набора свойств ( `HorizontalSizeClass` и `VerticalSizeClass` на основе `UIUserInterfaceSizeClass`), а также интерфейс идиому ( `UIUserInterfaceIdiom`) и масштаб отображения.

Все состояния выше упаковать в контейнер, который ссылается Apple как коллекцию признака ( `UITraitCollection`), который содержит не только свойства, но также их значения.

## <a name="trait-environment"></a>Признак среды

Признак сред являются новый интерфейс в iOS 8 и для возврата коллекции признака для следующих объектов:

-  Экраны ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Просмотр контроллеров ( `UIViewController` ).
-  Представления ( `UIView` ).
-  Контроллер представления ( `UIPresentationController` ).


Разработчик использует признака коллекцию, возвращаемую среде признака для определения того, как должен быть размещен пользовательский интерфейс.

Все среды признака сделать иерархии, как показано на следующей схеме:

 [![](unified-storyboards-images/viewhierarchy.png "Схема иерархии признака сред")](unified-storyboards-images/viewhierarchy.png#lightbox)

Коллекции признака у каждой среды выше признака будет проходить по умолчанию от родительского дочерних среды.

В дополнение к началу текущей коллекции признака, имеет среде признака `TraitCollectionDidChange` метод, который можно переопределить в подклассах представления или контроллера представления. Разработчик, этот метод можно использовать для изменения любого из элементов пользовательского интерфейса, зависящие от признаки, когда эти признаки были изменены.

## <a name="typical-trait-collections"></a>Типичный признака коллекций

В этом разделе рассматриваются типичные виды коллекций признака столкнуться пользователь при работе с iOS 8.

Ниже приведен типичный коллекцию признака, разработчик может появиться на iPhone.

|Свойство.|Значение|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Регулярное|
|`UserInterfaceIdom`|Номер телефона|
|`DisplayScale`|2.0|

Заданное выше представляет полностью полное признака коллекции, так как она содержит значения для всех его свойств признака.

Это также может быть коллекция признака, отсутствуют некоторые из его значений (которое Apple называется *Unspecified*):

|Свойство.|Значение|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Не указан|
|`UserInterfaceIdom`|Не указан|
|`DisplayScale`|Не указан|

Как правило тем не менее при разработчик запрашивает среду признака коллекции признака, он будет возвращать коллекцию полных как показано в приведенном выше примере.

Если среде признака (например, представления или контроллера представления) не внутри текущей иерархии представлений, разработчик может вернуться неуказанных значения для одного или нескольких свойств признака.

Разработчик также будет предоставлена коллекция частичные признака, если они используют один из методов создания, предоставляемых компанией Apple, таких как `UITraitCollection.FromHorizontalSizeClass`, чтобы создать новую коллекцию.

Одна операция, которая может выполняться в нескольких коллекциях признака сравнивает их друг к другу, который включает в себя запросом одну коллекцию признака, если он содержит еще один. Что подразумевается под *вложения* — что, для любого признака, указанный во второй коллекции, значение должно соответствовать в точности значение в первой коллекции.

Для проверки используются два признаки `Contains` метод `UITraitCollection` передавая значение признака, который необходимо протестировать.

Разработчик может выполнять сравнения вручную в код, чтобы определить способ представления макета или контроллеров представлений. Тем не менее `UIKit` этот метод используется внутренне для обеспечения части функциональных возможностей, как показано на внешний прокси, например.

## <a name="appearance-proxy"></a>Внешний вид прокси-сервера

Внешний вид прокси-сервер был введен в более ранних версиях iOS позволяет разработчикам настраивать свойства их представления. Она была расширена в iOS 8 для поддержки коллекций признака.

Внешний вид учетных записей-посредников, теперь включают новый метод `AppearanceForTraitCollection`, который возвращает новый внешний вид прокси для данной коллекции признака, который был передан в. Все настройки, которые разработчик выполняет, внешний вид прокси-сервер будет действовать только для представлений, которые соответствуют в указанную коллекцию признака.

Обычно разработчик будет передавать в частично определенное коллекции признака `AppearanceForTraitCollection` метода, например, только что указали горизонтальный размер класса из Compact, таким образом, чтобы они могли настраивать любого представления в приложении, которое является компактным по горизонтали.

## <a name="uiimage"></a>UIImage

Другой класс, который добавлен Apple коллекция признака — `UIImage`. В прошлом разработчику приходилось указывать @1X и @2x версии любой растровыми изображениями графические средства, которое они предназначались для включения в приложении (например, значок).

iOS 8 был расширен для разработчик может включать несколько версий образа в каталог образа на основе признака коллекции. Например разработчик может содержать изображение меньшего размера для работы с Compact класс признака и полного размера образа для любой другой коллекции.

Если один из образов используется внутри элемента `UIImageView` класс, представление изображения автоматически отобразит правильную версию изображения для коллекции признака. Если среде признака изменяется (например, переключение пользователей устройств с книжной на альбомную), новый размер изображения в соответствии с новой коллекции признака и изменить его размер, чтобы он соответствовал текущей версии образа, автоматически выберет представление изображений отображается.

## <a name="uiimageasset"></a>UIImageAsset

Apple добавлен новый класс для iOS 8 `UIImageAsset` для предоставления разработчикам еще большего контроля над Выбор изображения.

Ресурс изображения обертывает все различные версии изображения и разработчик может задать для конкретного образа, который соответствует коллекции признака, который был передан в. Изображения можно добавлять или удалять из ресурса образа, в режиме реального времени.

Дополнительные сведения о графических ресурсов, см. в разделе Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) документации.

## <a name="combining-trait-collections"></a>Объединение коллекций признака

Другой функции, который разработчик может выполнять в коллекциях признака является добавление двух друг с другом, необходимо привести в объединенный набор разделов, где не указано значений из одной коллекции заменяются указанными значениями в второй. Это делается с помощью `FromTraitsFromCollections` метод `UITraitCollection` класса.

Как уже говорилось выше, если любой из признаки не указывается в одной из коллекций признака и указывается в другой, значение будет присвоено указанной версии. Тем не менее, если существует несколько версий данного значения, который указан, значение из последнего признака коллекция будет значение, которое используется.

## <a name="adaptive-view-controllers"></a>Контроллеры представлений адаптивной

В этом разделе рассматриваются сведения о том, как iOS, представления и контроллеры представлений принявших концепции признаки и размеры автоматически быть более адаптивными в приложениях разработчика.

### <a name="split-view-controller"></a>Контроллер разделенного представления

Один из классов контроллера представления, которые были изменены наиболее в iOS 8 является `UISplitViewController` класса. В прошлом разработчик часто будет использовать контроллер представления разбиения на iPad версию приложения, и они будут иметь для предоставления совершенно другая версия их иерархией представления для версии приложения для iPhone.

В iOS 8 `UISplitViewController` класс доступен на обеих платформах (iPad и iPhone), которое позволяет разработчику для создания одной иерархии контроллер представления, который будет работать для iPhone и iPad.

Когда iPhone в альбомной ориентации, контроллер разделенного представления представит его представления side-by-side, как и в случае, когда будут отображаться на iPad.

### <a name="overriding-traits"></a>Переопределение признаков

Из родительского контейнера до дочерними контейнерами, как показано ниже график, отображающий контроллер представления разбиения на iPad в альбомной ориентации каскад признака сред:

 [![](unified-storyboards-images/cascadingclasses01.png "Контроллер разделенного представления на iPad в альбомной ориентации")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Поскольку iPad имеет обычный размер класс в горизонтальной и вертикальной ориентации, разделенное представление будет отображать основные и подробные представления.

На iPhone, где размер класса compact в оба варианта расположения, контроллер разделенного представления отображаются только представлении «Подробности», как показано ниже:

 [![](unified-storyboards-images/cascadingclasses02.png "Контроллер разделенного представления отображаются в представлении «Подробности»")](unified-storyboards-images/cascadingclasses02.png#lightbox)

В приложении, где разработчик хочет отобразить основные и подробные представления на iPhone в альбомной ориентации разработчик должен вставить родительского контейнера для контроллер разделенного представления и переопределить Признак коллекции. Как показано на следующем рисунке:

 [![](unified-storyboards-images/cascadingclasses03.png "Разработчик должен вставить родительского контейнера для контроллер разделенного представления и переопределить Признак коллекции")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Объект `UIView` задан как родительский контроллера представления разбиения и `SetOverrideTraitCollection` был вызван для представления, передавая коллекция признака и определение целевых объектов контроллер разделенного представления. Новая коллекция признака переопределяет `HorizontalSizeClass`, задав для него `Regular`, таким образом, чтобы контроллер разделенного представления будут отображаться основные и подробные представления на iPhone в альбомной ориентации.

Обратите внимание, что `VerticalSizeClass` было присвоено `unspecified`, который позволяет новой коллекции признака для добавления в коллекцию признаков в родительском объекте, в результате чего `Compact VerticalSizeClass` контроллер разделенного представления дочернего элемента.

### <a name="trait-changes"></a>Изменения признака

В этом разделе будут рассмотрены, подробно, как коллекции признака переход при изменениях в среде признака. Например, при повороте устройства от книжной к альбомной ориентации.

 [![](unified-storyboards-images/traittransitions01.png "Книжной к альбомной ориентации Общие сведения об изменениях признака")](unified-storyboards-images/traittransitions01.png#lightbox)

Во-первых iOS 8 выполняет некоторые программы установки для Подготовьтесь к переходу вступили в силу. Затем система выполняет анимацию переходное состояние. Наконец iOS 8 очищает up любые временные состояния, необходимые во время перехода.

iOS 8 предоставляет несколько обратных вызовов, которые разработчик может использовать для участия в изменении признака, как показано в следующей таблице:

|Phase|Callback|Описание|
|--- |--- |--- |
|Установка|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Этот метод вызывается в начале изменения признака, прежде чем коллекции признака возвращает значение его новое значение.</li><li>Метод вызывается, когда изменилось значение признака коллекции, но до выполнения анимации.</li></ul>|
|Анимация|`WillTransitionToTraitCollection`|Координатор перехода, переданного этому методу `AnimateAlongside` свойство, которое позволяет разработчику добавлять анимации, которые будут выполняться вместе с анимации по умолчанию.|
|Очистка|`WillTransitionToTraitCollection`|Предоставляет метод для разработчиков для включения собственный код очистки после перехода.|

`WillTransitionToTraitCollection` Метод отлично подходит для анимации контроллеров представления вместе с изменениями признака коллекции. `WillTransitionToTraitCollection` Метод доступен только на контроллеры представления ( `UIViewController`), а не на других средах признака, например `UIViews`.

`TraitCollectionDidChange` Отлично подходит для работы с `UIView` класса, где разработчик хочет обновить пользовательский Интерфейс, как изменить признаки.

### <a name="collapsing-the-split-view-controllers"></a>Свертывание контроллеров представлений разбиения

Теперь давайте взгляните ближе на то, что происходит, когда контроллер представления разбиения свертывает из двух столбцов один столбец представления. Как часть этого изменения существуют две процедуры, которые должны применяться:

-  По умолчанию контроллер разделенного представления будет использоваться контроллер первичное представление как представление после свертывания. Разработчик может переопределить это поведение путем переопределения `GetPrimaryViewControllerForCollapsingSplitViewController` метод `UISplitViewControllerDelegate` и предоставляя любого контроллера представления, они должны отображаться в свернутом состоянии.
-  Вторичный контроллер представления должен объединяются в основной контроллер представления. Обычно разработчику не нужно предпринимать никаких действий для выполнения этого шага; Контроллер разделенного представления включает автоматическую обработку этого этапа, на основе оборудования устройства. Тем не менее могут существовать некоторые особые случаи, где разработчик может потребоваться взаимодействовать с этим изменением. Вызов `CollapseSecondViewController` метод `UISplitViewControllerDelegate` позволяет контроллеру главное представление для отображения при возникновении свернуть, а не в представлении сведений.


### <a name="expanding-the-split-view-controller"></a>Развернув контроллер разделенного представления

Теперь давайте взгляните ближе на то, что происходит, когда контроллер разделенного представления будет расширен в свернутом состоянии. Опять же существует два этапа, которые должны применяться.

-  Во-первых определите новый основной контроллер представления. По умолчанию контроллер разделенного представления будет автоматически использовать основной контроллер представления в свернутом представлении. Опять же, разработчик может переопределить это поведение, используя `GetPrimaryViewControllerForExpandingSplitViewController` метод `UISplitViewControllerDelegate` .
-  После выбора основного контроллера представления, необходимо повторно создать вторичный контроллер представления. Опять же контроллер разделенного представления включает автоматическую обработку этого этапа, на основе оборудования устройства. Разработчик может переопределить это поведение путем вызова `SeparateSecondaryViewController` метод `UISplitViewControllerDelegate` .


В контроллер разделенного представления первичного контроллера представления играет роль в развертывание и свертывание представления путем реализации `CollapseSecondViewController` и `SeparateSecondaryViewController` методы `UISplitViewControllerDelegate`. `UINavigationController` реализует эти методы, чтобы автоматически отправка и отображение вторичное представление-контроллер.

### <a name="showing-view-controllers"></a>Отображение контроллеров представления

Еще одно изменение, от которого поступил Apple для iOS 8 — так, что разработчик показывает контроллеров представлений. В прошлом, если приложение должно было контроллер представления конечный (например, контроллер представления таблиц), а разработчик показал его (например, в ответ на пользователя, щелкнув ячейку), приложение достигнуть обратно через иерархию контроллера Контроллер представления навигации и вызов `PushViewController` метода к его, чтобы отобразить новое представление.

Это вызывало очень тесного взаимодействия между контроллером навигации и среде, запущенного в. В iOS 8 Apple отделена от это, предоставляя два новых метода:

-  `ShowViewController` — Адаптируется для отображения нового контроллера представления на основе его среды. Например, в `UINavigationController` его просто помещает в стек новое представление. В контроллер разделенного представления нового контроллера представления откроется с левой стороны как новый основной контроллер представления. Если контроллер представления не контейнер присутствует, новое представление отображается как модальное контроллер представления.
-  `ShowDetailViewController` — Работает таким же образом, чтобы `ShowViewController`, но реализуется на контроллер разделенного представления для замены нового контроллера представления, передаваемые в представление сведений. Если контроллер разделенного представления свернут (как может отображаться в приложении iPhone), вызов будет перенаправляться на `ShowViewController` метод и нового представления будут отображаться в виде основного контроллера представления. Опять же если присутствует контроллер представления нет контейнера, новое представление отображается как модальное контроллер представления.


Эти методы работы, начиная с конечного контроллера представления и выполнить проход вверх Просмотр иерархии, пока они находят в правильном контейнере контроллера представления для управления отображением нового представления.

Разработчики могут реализовать `ShowViewController` и `ShowDetailViewController` в собственных пользовательских контроллеров представлений для получения же автоматизировали функциональные возможности, `UINavigationController` и `UISplitViewController` предоставляет.

### <a name="how-it-works"></a>Принцип работы

В этом разделе мы обратим внимание на самом деле реализация этих методов в iOS 8. Первый давайте взглянем на новую `GetTargetForAction` метод:

 [![](unified-storyboards-images/gettargetforaction.png "Новый метод GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Этот метод проходит цепи иерархии, пока не будет найден правильный контейнер контроллера представления. Пример:

1.  Если `ShowViewController` вызывается метод, первого контроллера представления в цепочке, который реализует этот метод является контроллер навигации, поэтому он используется в качестве родительского для нового представления.
1.  Если `ShowDetailViewController` вместо вызова метода, контроллер разделенного представления является первого контроллера представления для реализации, поэтому он используется в качестве родительского элемента.


`GetTargetForAction` Метод работает контроллер представления, который реализует данное действие и затем запросом, контроллер представления, если требуется получать это действие. Так как этот метод является открытым, разработчики могут создавать свои собственные пользовательские методы, функции так же, как встроенные в `ShowViewController` и `ShowDetailViewController` методы.

## <a name="adaptive-presentation"></a>Адаптивная презентации

В iOS 8, Apple сделала контекстном презентации ( `UIPopoverPresentationController`) адаптивной также. Контроллер представления презентации контекстном автоматически предоставит обычному отображению контекстном в обычный класс размер, но будет отображать его во весь экран в классе размер по горизонтали compact (такие как на устройстве iPhone).

В соответствии с изменениями в системе единой раскадровки, создан новый объект контроллера для управления контроллерами представления представленных — `UIPresentationController`. Этот контроллер создается с момента контроллера представления представляется до его закрытия. Так как это класс управления, его можно считать суперкласса через контроллер представления с учетом изменений в устройства, которые влияют на контроллер представления (например, ориентация) управляет которого затем последовательно сбоев в контроллер представления контроллера представления.

Когда разработчик отображает контроллера представления с помощью `PresentViewController` метода управления процессом презентации передается `UIKit`. UIKit обрабатывает (среди прочего) правильные контроллер для создания стиля, с единственным исключением является, когда контроллер представления имеет стиль, заданный `UIModalPresentationCustom`. Здесь, приложение может предоставить собственные PresentationController а не с помощью `UIKit` контроллера.

### <a name="custom-presentation-styles"></a>Стили пользовательского представления

Стиль пользовательского представления разработчики имеют возможность использовать пользовательский контроллер представления. Этот настраиваемый контроллер позволяют изменять внешний вид и поведение он является allied для представления.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Работа с классами размер

Адаптивная фотографии проект Xamarin, включенную в этой статье предоставляет рабочий пример с помощью классов размера и адаптивной контроллеров представлений в приложении единого интерфейса iOS 8.

Хотя приложение создает пользовательский Интерфейс полностью в коде, а не с помощью конструктора IOS и создания единой раскадровки, применяются те же методы. Далее в этой статье будет показано, как использовать классы размера с помощью единой раскадровки и конструктора в приложении Xamarin iOS.

Теперь давайте более подробно рассмотрим как проект адаптивной фотографии реализует некоторые функции класса размера в iOS 8 для создания адаптивное управление приложениями.

### <a name="adapting-to-trait-environment-changes"></a>Адаптация к изменениям среды признака

При запуске приложения Adaptive фотографии на устройстве iPhone, при повороте устройства с книжной на альбомную, контроллер разделенного представления для отображения основные и сведения о представлении:

 [![](unified-storyboards-images/rotation.png "Контроллер разделенного представления будут отображаться оба образце и представление сведений, как показано ниже")](unified-storyboards-images/rotation.png#lightbox)

Это достигается путем переопределения `UpdateConstraintsForTraitCollection` контроллер представления и корректировки ограничения на основании значения `VerticalSizeClass`. Пример:

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Добавление анимации перехода

Если контроллер разделенного представления в адаптивной фотографий, приложение выходит из свернуть в развернутом, анимации добавляются к анимации по умолчанию путем переопределения `WillTransitionToTraitCollection` метод контроллера представления. Пример:

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Переопределение среде признака

Как показано выше, адаптивной фотографии приложение вызывает контроллер разделенного представления как для получения сведений о и главного представления, когда устройство iPhone находится в представлении в альбомной ориентации.

Это осуществлялось с помощью следующего кода в контроллер представления:

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Развертывание и свертывание контроллер разделенного представления

Далее рассмотрим реализации расширения и свертывания поведение контроллер разделенного представления в Xamarin. В `AppDelegate`, когда контроллер разделенного представления создается, его делегат назначается для обработки этих изменений:

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

`SeparateSecondaryViewController` Проверяет метод, если фотографии отображается и выполняет действие, исходя из этого состояния. Если не фотография показывается его сворачивает вторичного контроллера представления для отображения контроллера представления главного.

`CollapseSecondViewController` Метод используется при расширении контроллер разделенного представления на предмет существования все фотографии в стеке, поэтому оно сворачивается обратно к этому представлению.

### <a name="moving-between-view-controllers"></a>Перемещение между контроллерами представления

Теперь давайте рассмотрим перемещение между контроллерами представления приложения Adaptive фотографии. В `AAPLConversationViewController` класса, когда пользователь выбирает ячейку в таблице `ShowDetailViewController` метод вызывается для отображения в представлении сведений:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>Отображение раскрытие индикаторы

В приложении Adaptive Photo существует несколько мест, где индикаторы раскрытие скрытых или отобразятся на основе изменений в среду признака. Это делается следующим кодом:

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Они реализованы с помощью `GetTargetViewControllerForAction` подробно рассматривается выше метода.

При отображении контроллер представления таблицы данных он использует методы, реализованное выше ли Принудительная отправка должно произойти, и ли отобразить или скрыть раскрытие индикатор соответствующим образом:

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Новый `ShowDetailTargetDidChangeNotification` типа

Apple добавлен новый тип уведомления для работа с классами размер и сред признака из в контроллер разделенного представления `ShowDetailTargetDidChangeNotification`. Это уведомление отправляется при каждом изменении целевого подробное представление для контроллер разделенного представления, например когда контроллер разворачивает или сворачивает.

Адаптивная фотографии приложение использует это уведомление для обновления состояния индикатора раскрытия, при изменении контроллера представления сведений:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Взгляните ближе на приложение адаптивной фотографий, чтобы просмотреть все способы, что классы размера, признака коллекций и адаптивной контроллеров представления позволяют легко создавать приложения, единой в Xamarin.iOS.

## <a name="unified-storyboards"></a>Унифицированная раскадровка

Новые унифицированным раскадровкам iOS 8, позволяют разработчикам создавать, унифицированная файл раскадровки, который может отображаться на устройствах iPhone и iPad, предназначенных для нескольких классов размера. С помощью унифицированным раскадровкам, разработчик пишет меньше определенного код пользовательского интерфейса и имеет только один интерфейс разработки для создания и обслуживания.

Основные преимущества унифицированным раскадровкам приведены.

-  Используйте один и тот же файл раскадровки для iPhone и iPad.
-  Разверните обратной iOS 6 и iOS 7.
-  Предварительный просмотр макета для различных устройств, ориентации и версии ОС из в конструктор iOS Xamarin.

Эта функция полностью поддерживается в Visual Studio для Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Включение размеров

По умолчанию любым новым проектом Xamarin.iOS будет нам классы размера. Чтобы использовать классы размера и адаптивной переходы внутри раскадровки из старого проекта, необходимо преобразовать в формат раскадровки единой Xcode 6 из внутри конструктора iOS.

Для этого отправьте раскадровки для преобразования в iOS Designer и проверка **классы размера используйте** флажок:

 [![](unified-storyboards-images/sizeclass01.png "Флажок использования классов размера")](unified-storyboards-images/sizeclass01.png#lightbox)

Конструктор iOS убедитесь, что разработчику необходимо преобразовать формат раскадровки, которую следует использовать классы размера:

 [![](unified-storyboards-images/sizeclass02.png "Использование классов размера оповещение")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Автоматический макет также должны быть проверены на наличие классы размера для правильной работы.

### <a name="generic-device-types"></a>Типы универсальных устройств

После преобразования раскадровки для использования классов размер его будут отображены повторно в рабочей области конструирования и **просмотреть как** устройство будет универсальный:

 [![](unified-storyboards-images/sizeclass03.png "Просматривать как тип универсального устройства")](unified-storyboards-images/sizeclass03.png#lightbox)

При выборе типа универсального устройства, все контроллеры представления будет изменен до 600 x 600 квадрата. Этот квадрат представляет размеры любой ширине и любой высотой. Когда конструктор iOS — в этом режиме, любые изменения, применяемые ко всем классов размера.

Разработчик также имеет возможность просмотра области конструктора как iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Просмотр области конструктора в виде iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Или просмотр его iPad:

 [![](unified-storyboards-images/sizeclass05.png "Просмотр области конструктора в виде iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Выбор размера класса

Кнопка селектор класса размера, расположенная в левом верхнем углу области конструктора (рядом с представление в виде раскрывающегося списка). Он позволяет разработчику выбирать, какие классы размера редактируемый в настоящий момент:

 [![](unified-storyboards-images/sizeclass06.png "Выбор размера класса")](unified-storyboards-images/sizeclass06.png#lightbox)

Селектор представляется выделении размер класса в виде сетки 3 x 3. Каждый из квадратов в сетке представляет комбинацию классов ширины и высоты. Центральный квадрат выбирает Any Width/Any высота размер класса (который является представлением по умолчанию для единой раскадровки). При выборе этого квадратный разработчику редактирует макет по умолчанию, который наследуется другие конфигурации.

Квадрат в левом верхнем углу сетки представляет класс Compact размера ширины/Compact Высота:

 [![](unified-storyboards-images/sizeclass07.png "Класса размера высоты Compact ширина/Compact")](unified-storyboards-images/sizeclass07.png#lightbox)

Этот режим соответствует iPhone с альбомной ориентацией. Квадрат в правом нижнем углу сетки представляет регулярные ширина/Regular высота размер класс, который представляет iPad:

 [![](unified-storyboards-images/sizeclass08.png "Класса размера высоты регулярных ширина/Regular")](unified-storyboards-images/sizeclass08.png#lightbox)

Чтобы изменить макет для iPhone в книжной ориентации, выберите квадрат в левом нижнем углу. Представляет Compact класс размера ширины/Regular Высота:

 [![](unified-storyboards-images/sizeclass09.png "Класса размера высоты Compact ширина/Regular")](unified-storyboards-images/sizeclass09.png#lightbox)

Щелкните в квадрат, чтобы выбрать его, и в область конструктора приведет к изменению размера контроллеров представлений для сопоставления нового выделения:

 [![](unified-storyboards-images/sizeclass10.png "В область конструктора приведет к изменению размера контроллеров представлений для сопоставления нового выделения, как показано")](unified-storyboards-images/sizeclass10.png#lightbox)

См. в разделе класса размера в этой статье подробнее на размеры и как они влияют на макет для iPhone и iPad.

### <a name="adaptive-segue-types"></a>Адаптивный Segue типы

Если разработчик использовал раскадровки, прежде чем, то они будут знакомы с существующие типы segue **Push**, **модального** и **контекстном**. При классы размера включены в файл единой раскадровки, становятся доступными следующие адаптивной Segue типы (которые соответствуют новый API контроллера представления, описанных выше): **Показать** и **структура** .

> [!IMPORTANT]
> Если включены классы размеров, все существующие переходы будут преобразовываться в новые типы.

Рассмотрим в качестве примера из iOS 8 приложение, которое используется раскадровка единой с контроллер разделенного представления, с меню навигации простой игры в основное представление. Если пользователь нажимает на кнопку меню, контроллер представления выбранного элемента должно быть показано в разделе сведений контроллер разделенного представления при выполнении на iPad. На iPhone контроллер представления элемента должен быть в стек навигации.

Чтобы этого добиться, в конструкторе iOS удерживая нажатой кнопку и перетащите линию на контроллер представления для отображения. При отпускании кнопки мыши, выберите `Show Detail` Segue типа во всплывающем меню:

 [![](unified-storyboards-images/segue01.png "Выберите структура Segue типа во всплывающем меню")](unified-storyboards-images/segue01.png#lightbox)

Будет создан новый переход между кнопкой "и" контроллер представления. Теперь запустите приложение в симуляторе iPhone и будет отображаться в главном меню:

 [![](unified-storyboards-images/segue02.png "Главное меню")](unified-storyboards-images/segue02.png#lightbox)

Щелкните **игры выберите** кнопка "и" контроллер представления элемента будет помещено в стек навигации:

 [![](unified-storyboards-images/segue03.png "Элементы контроллера представления будет помещено в стек навигации, как показано")](unified-storyboards-images/segue03.png#lightbox)

Остановка симулятора iPhone и запустите приложение на iPad симулятора. Переключиться в режим альбомной ориентации и основной снова отображается меню:

 [![](unified-storyboards-images/segue04.png "Главное меню отображается")](unified-storyboards-images/segue04.png#lightbox)

Снова нажмите кнопку на **игры выберите** кнопку и контроллер представления элемента отображается в разделе сведений контроллер разделенного представления:

 [![](unified-storyboards-images/segue05.png "Элементы контроллера представления, показано в разделе сведений контроллер разделенного представления")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Исключение элемента из класса размера

Бывают случаи, когда заданный элемент (например, представления, элемент управления или ограничении) не требуется внутри определенного размера класса. Чтобы исключить элемент из класса размера, выберите нужный элемент, чтобы исключить в **конструктора**. Прокрутите до нижней части **обозреватель свойств** и нажмите кнопку **шестеренки** раскрывающееся меню. Выберите сочетание **ширины** и **высота** исключить элемент из:

[![](unified-storyboards-images/exclude-a.png "Выберите сочетание ширины и высоты")](unified-storyboards-images/exclude-a.png#lightbox)

Новый *случай исключения* добавляется к элементу в нижней части **обозреватель свойств**. Затем снимите флажок **установленные** флажок для данного класса размера:

[![](unified-storyboards-images/exclude-b.png "Снимите этот флажок, установленные")](unified-storyboards-images/exclude-b.png#lightbox)

Перейдите в область конструктора в ширину и высоту, элемент был исключен из, он был удален из заданного класса размера, но не весь макет пользовательского интерфейса:

 [![](unified-storyboards-images/exclude02.png "Перейдите в область конструктора в ширину и высоту, элемент был исключен из")](unified-storyboards-images/exclude02.png#lightbox)

Вернуться к классу Any высота Width/Any размер и элемент все еще находится в месте:

 [![](unified-storyboards-images/exclude03.png "Вернуться к размеру Any Width/Any высоты")](unified-storyboards-images/exclude03.png#lightbox)

При запуске приложения в симуляторе iPad находится этот элемент:

 [![](unified-storyboards-images/exclude04.png "Элемент, отображаемого, когда выполнение приложения в симуляторе iPad")](unified-storyboards-images/exclude04.png#lightbox)

И при запуске приложения на симуляторе iPhone, отсутствует элемент:

 [![](unified-storyboards-images/exclude05.png "Элемент отсутствует, когда выполнение приложения в симуляторе iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Чтобы удалить ветвь исключения из элемента, просто выбрать элемент на **конструктора**, прокрутите до нижней части **обозреватель свойств** и нажмите кнопку **-** кнопку рядом с полем так, чтобы удалить.

Чтобы увидеть реализацию унифицированным раскадровкам, просмотрите `UnifiedStoryboard` пример приложения Xamarin iOS 8, подключенных к этому документу.

## <a name="dynamic-launch-screens"></a>Экраны запуска динамической

В ходе запускает приложение iOS отзыв для пользователя, которого приложение фактически начиная сверху файле экрана запуска отображается экран-заставка. До iOS 8, разработчику придется включать несколько `Default.png` ресурсы для каждого устройства типа, ориентации и разрешения экрана, приложение будет выполняться на изображений. Например `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`и т. д.

Разложение на новых устройствах iPhone 6 "и" iPhone 6 Plus устройств (и предстоящих Apple Watch) с все устройства iPad и iPhone существующие, это представляет большой массив разного размера, ориентации и устранения `Default.png` запуска экрана графические активы, которые необходимо Создание и поддержку. Кроме того эти файлы могут быть довольно большими и будет «Раздувание» пакет приложений конечного результата, увеличивает время, необходимое для загрузки приложения в iTunes App Store (в возможно обновление он не сможет доставить по сотовой сети) и увеличение объема пространства хранения на устройстве конечного пользователя.

Новое для iOS 8, разработчик может создать один atomic `.xib` файл в Xcode, автоматический макет и размер классов для создания *динамического экран запуска* , будет работать для всех устройств, разрешения и ориентации. Это не только снижает объем работ, необходимый разработчика создавать и обслуживать все необходимые графические активы, но он значительно уменьшает размер установленного пакета приложения.


Динамические экраны запуска имеют следующие ограничения и рекомендации:

 - Используйте только `UIKit` классов.
 - Используйте представление один корневой, `UIView` или `UIViewController` объекта.
 - Не вносите подключения к коду приложения (не добавляйте **действия** или **розеток**).
 - Не добавляйте `UIWebView` объектов.
 - Не используйте любые пользовательские классы.
 - Не используйте атрибуты среды выполнения.

С приведенным выше рекомендациям в виду, давайте рассмотрим добавление динамических экран запуска в существующий проект Xamarin iOS 8.

Выполните следующие действия:

1. Откройте **Visual Studio для Mac** и загрузить **решение** Добавление динамических экрана запуска для.
2. В **обозревателе решений**, щелкните правой кнопкой мыши `MainStoryboard.storyboard` файл и выберите **открыть с помощью** > **конструктора Interface Builder Xcode**:

    [![](unified-storyboards-images/dls01.png "Открыть в построителе интерфейса Xcode")](unified-storyboards-images/dls01.png#lightbox)
3. В Xcode, выберите **файл** > **New** > **файла...** :

    [![](unified-storyboards-images/dls02.png "Выберите файл / создать")](unified-storyboards-images/dls02.png#lightbox)
4. Выберите **iOS** > **пользовательский интерфейс** > **запуска экрана** и нажмите кнопку **Далее** кнопки:

    [![](unified-storyboards-images/dls03.png "Выберите iOS и интерфейс пользователя и запуска экрана")](unified-storyboards-images/dls03.png#lightbox)
5. Назовите файл `LaunchScreen.xib` и нажмите кнопку **создать** кнопки:

    [![](unified-storyboards-images/dls04.png "Назовите файл LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Измените структуру на экране запуска путем добавления графических элементов и с помощью ограничения макета для размещения их для данного устройства, ориентации и размеры экрана:

    [![](unified-storyboards-images/dls05.png "Редактирование конструктора экрана запуска")](unified-storyboards-images/dls05.png#lightbox)
7. Сохраните изменения в `LaunchScreen.xib`.
8. Выберите **целевого приложения** и **Общие** вкладке:

    [![](unified-storyboards-images/dls06.png "Выберите целевой объект приложения и вкладке \"Общие\"")](unified-storyboards-images/dls06.png#lightbox)
9. Нажмите кнопку **выберите Info.plist** кнопку, выберите `Info.plist` приложение Xamarin и нажмите кнопку **Выбор** кнопки:

    [![](unified-storyboards-images/dls07.png "Выбор файла Info.plist приложения Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. В **значки приложений и изображения при запуске** раздела, откройте **запустите файл экрана** раскрывающийся список и выберите `LaunchScreen.xib` созданный выше:

    [![](unified-storyboards-images/dls08.png "Выберите LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Сохранить изменения в файл и вернитесь в Visual Studio для Mac.
12. Подождите, пока Visual Studio для Mac завершить синхронизацию изменений с Xcode.
13. В **обозревателе решений**, щелкните правой кнопкой мыши **ресурсов** папку и выберите **добавить** > **добавить файлы...** :

    [![](unified-storyboards-images/dls09.png "Выберите Добавить / добавить файлы...")](unified-storyboards-images/dls09.png#lightbox)
14. Выберите `LaunchScreen.xib` ранее созданный файл и нажмите кнопку **откройте** кнопки:

    [![](unified-storyboards-images/dls10.png "Выберите файл LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Постройте приложение.

### <a name="testing-the-dynamic-launch-screen"></a>Тестирование на экране запуска динамической

В Visual Studio для Mac установите имитатора сетчатку iPhone 4 и запустить приложение. Динамические экране запуска будет отображаться в правильном формате и ориентация:

[![](unified-storyboards-images/dls11.png "Динамические отображаться в вертикальной ориентацией экрана запуска")](unified-storyboards-images/dls11.png#lightbox)

Остановить приложение в Visual Studio для Mac и выберите устройства iPad с iOS 8. Запустите приложение и на экране запуска будет правильно отформатирован для этого устройства и ориентация:

[![](unified-storyboards-images/dls12.png "Динамические отображаются в горизонтальной ориентации экрана запуска")](unified-storyboards-images/dls12.png#lightbox)

Вернитесь в Visual Studio для Mac и остановить запуск приложения.

### <a name="working-with-ios-7"></a>Работа с iOS 7

Чтобы обеспечить обратную совместимость с iOS 7, просто включите обычные `Default.png` ресурсы в обычном режиме в приложении iOS 8 изображений. iOS будет вернуть к предыдущему поведению и использовать эти файлы в качестве заставки, при запуске на устройстве iOS 7.

Чтобы увидеть реализацию динамических экрана запуска в Xamarin, просмотрите [динамического экраны запуска](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) пример приложения iOS 8, подключенных к этому документу.

## <a name="summary"></a>Сводка

В этой статье рассмотрен быстрые классы размера и как они влияют на макет на устройствах iPhone и iPad. Это обсуждалось, работе признаки, признака сред и признака коллекций с помощью классов размера для создания единого интерфейса. Она заняла краткий обзор адаптивной контроллеры представлений и как они работают с помощью классов размера внутри единой интерфейсов. Хотя реализация размер классы и интерфейсы, единой, полностью из рассматривались C# кода внутри приложения Xamarin iOS 8.

Наконец в этой статье рассматриваются основы создания унифицированным раскадровкам в конструкторе, который будет работать на устройствах iOS IOS Xamarin и создание одинарный, динамические экрана запуска, которая будет отображаться как заставки на любых устройствах с iOS 8.

## <a name="related-links"></a>Связанные ссылки

- [Адаптивная фотографии (пример)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Пример StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Динамические экраны запуска (пример)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Введение в iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Динамические макеты в iOS8 - развиваться 2014 (видео)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
