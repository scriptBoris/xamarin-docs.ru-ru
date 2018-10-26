---
title: Основы сообщений расширение приложения в Xamarin.iOS
description: В этой статье показано как включать расширение сообщений приложения в решении Xamarin.iOS, которое интегрируется с приложения для обмена сообщениями и представляет новые функциональные возможности для пользователя.
ms.prod: xamarin
ms.assetid: 0CFB494C-376C-449D-B714-9E82644F9DA3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7bd4a87843852e940da96f688371ddbecbf7e0b4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105783"
---
# <a name="message-app-extension-basics-in-xamarinios"></a>Основы сообщений расширение приложения в Xamarin.iOS

_В этой статье показано как включать расширение сообщений приложения в решении Xamarin.iOS, которое интегрируется с приложения для обмена сообщениями и представляет новые функциональные возможности для пользователя._

Новое в iOS 10, расширение сообщения приложение интегрируется с **сообщений** приложения, по возможности представлены новые функциональные возможности для пользователя. Расширения можно отправлять текст, наклейки, файлов мультимедиа и интерактивных сообщений.

## <a name="about-message-app-extensions"></a>О расширениях сообщение приложения

Как уже говорилось выше, расширение сообщения приложение интегрируется с **сообщений** приложения, по возможности представлены новые функциональные возможности для пользователя. Расширения можно отправлять текст, наклейки, файлов мультимедиа и интерактивных сообщений. Доступны два типа расширения приложения сообщение:

- **Пакеты наклейки** -содержит коллекцию наклейки, которые пользователь может добавить сообщение. Наклейка с пакеты могут создаваться без написания кода.
- **iMessage приложения** -можно представить пользовательский интерфейс пользователя в приложения "сообщения" для выбора наклейки, ввода текста, включая файлы мультимедиа (с преобразованием необязательно типов) и создание, изменение и отправки сообщений взаимодействия.

Сообщения приложений расширения предоставляют три основных типа содержимого:

- **Интерактивные сообщения** -— это тип содержимого пользовательское сообщение, которое создает приложение, когда пользователь касается сообщения, приложение будет запускаться на переднем плане.
- **Наклейки** -представляют собой образы, созданные в приложения, которое может быть включено в сообщения, отправленные между пользователями.
- **Другое содержимое поддерживается** - приложение может предоставить содержимое, например фотографий, видео, текста или ссылки на любое другое содержимое, типы, которые всегда поддерживаются приложения для обмена сообщениями.

Новое в iOS 10, приложение сообщения теперь содержит свой собственный выделенный, встроенные App Store. Все приложения, которые включают расширения приложений сообщение будет отображаться и предлагаются в это хранилище. Новый контейнер приложения и сообщения будут отображаться все приложения, которые были загружены из Store приложения сообщений для предоставления быстрого доступа для пользователей.

Также новые в iOS 10, Apple добавил однозначного соответствия примитивов встроенного приложения позволяющие пользователям легко находить приложение. Например если один пользователь отправляет содержимое в другой из приложения, второй пользователь не имеет (например, наклейку на примере), имя приложения, отправляющего находится в списке содержимого в журнале сообщений. Если пользователь касается приложения имя, App Store сообщения, мы открыть и приложения, выбранного в хранилище.

Сообщение расширений приложений похожи на существующие приложения iOS, что разработчик знаком с созданием и Администраторы получат доступ ко всем стандартной платформы и возможности стандартных операций ввода-вывода приложения. Пример:

- Они имеют доступ к покупки в приложении.
- Они имеют доступ к Apple Pay.
- Они имеют доступ к оборудования устройства, например камеры.

Сообщение расширений приложений поддерживаются только в iOS 10, однако содержимое, которые отправляют эти расширения можно просматривать на устройствах, watchOS и macOS. Новый _недавних объектов страницы_ добавлен watchOS 3, отображает последние наклейки, который был отправлен с телефона, включая полученные сообщения расширений приложений, и разрешить пользователю отправлять эти наклейки в часах Apple Watch.

## <a name="about-the-messages-framework"></a>О платформе сообщений

Новое в iOS 10, платформа сообщений предоставляет интерфейс между расширение приложений сообщения и сообщения приложения на устройстве iOS пользователя. Когда пользователь запускает приложение на основе внутри приложения для обмена сообщениями, эта платформа позволяет приложению быть обнаружены и предоставляет данные и контекст, необходимый для размещения его пользовательского интерфейса.

После запуска приложения пользователь взаимодействует с его, чтобы создать новое содержимое для совместного использования через сообщение. Затем приложение использует framework сообщений для передачи только что созданный содержимого приложения для обмена сообщениями для обработки.

Framework сообщения и сообщения расширений приложений создаются на основе существующих iOS технологии расширения приложения. Дополнительные сведения о расширениях приложения, см. в разделе Apple [руководство по программированию расширения приложения](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

В отличие от других точек расширения, который Apple предоставила во всей системе разработчик может не предоставляет приложение host для расширений приложений свои сообщения, так как само приложение сообщения используется в качестве контейнера. Тем не менее разработчик имеет возможность расширения приложений сообщения внутри приложения iOS на новый или существующий и доставить его вместе с пакета.

Если расширений приложений сообщение входит в пакет приложения iOS, будет отображаться значок приложения на начальном экране устройства и на панели приложения сообщения из в приложения для обмена сообщениями. Если он не включен в комплект приложения, расширение сообщения приложений отображался только в панели сообщения приложения.

Даже если расширений приложений сообщение не включен в пакет приложений узла, разработчику нужно будет предоставлять значка приложения в пакете расширения приложений сообщение, так как это значок, который будет отображаться в других частях системы, например контейнер приложения и сообщений или параметров , для расширения.

## <a name="about-stickers"></a>О наклейки

Apple разработанная наклейки как новый способ для пользователей iMessage для обмена данными, позволяя наклейки отправляться встроенного, как любое другое содержимое сообщения, или они могут присоединяться к предыдущей всплывающие сообщения в диалоге.

Что такое наклейки

- Они являются образы, которые предоставляет расширение сообщения приложений.
- Они могут быть либо анимированного или статического изображения.
- Они предоставляют новый способ совместного использования содержимого образа из внутри приложения.

Существует два способа создания наклейки:

1. Наклейки пакет сообщений приложения расширений могут создаваться из в Xcode без включения любого кода. Все, что требуется — ресурсы для наклейки и значки приложений.
2. Путем создания стандартным расширением сообщение приложения, предоставляющий наклейки из кода с помощью платформы сообщения.

### <a name="creating-sticker-packs"></a>Создание пакетов наклейки

Наклейки пакеты создаются с помощью специальных шаблона в Xcode и достаточно просто указать статический набор графических ресурсов, которые можно использовать в качестве наклейки. Как уже говорилось выше, они не требуют любой код, разработчик просто перетаскивает файлов изображений в папку пакета наклейку внутри каталога активов наклейки.

Для изображения должны быть включены в пакет наклейку она должна удовлетворять следующим требованиям:

- Образы должны быть в формате PNG, APNG, GIF или JPEG. Apple предлагает, использовать только форматы PNG и APNG, при предоставлении средств наклейки.
- Анимированный наклейки поддерживают только форматы APNG и GIF.
- Наклейка с изображения должны предоставлять прозрачный фон, так как они могут быть помещены на всплывающие сообщения в диалоге пользователем.
- Файлы отдельно взятого изображения должны быть меньше, чем 500 КБ.
- Изображения не может быть меньше, чем 100 x 100 точек или большего размера, указывающий 206 x 206.

> [!IMPORTANT]
> Образы наклейки должно всегда предоставляться `@3x` разрешение в 300 x 300 для 618 x 618 пикселей. Система автоматически создаст `@2x` и `@1x` версий во время выполнения при необходимости.

Apple предлагает тестирование графические активы наклейки от различных разных цветной фон (например, белый, черный, красный, желтый и несколькими цветные) и более чем фотографий, чтобы убедиться, что они выглядят лучшие во всех возможных ситуациях.

Наклейки пакетов можно предоставить наклейки в одном из трех доступных размеров:

- **Небольшой** — 100 x 100 точек.
- **Средний** - 136 x 136 точек. Это размер по умолчанию.
- **Большой** - 206 x 206 точек.

Инспектор атрибутов Xcode можно задать размер для всего пакета наклейки и только предоставляют графические активы, которые соответствуют запрошенного размера, для получения наилучших результатов в браузере наклейку внутри приложения для обмена сообщениями.

Дополнительные сведения см. в разделе наших [построитель мороженого](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) приложение и Apple [ссылку сообщения](https://developer.apple.com/reference/messages).

## <a name="creating-a-custom-sticker-experience"></a>Создает интерфейс пользовательского наклейки

Если приложению требуется больше элемента управления или гибкость, чем обеспечивается пакетом наклейку, его можно включать расширение приложения сообщение и предоставить наклейки через framework сообщений наклейку, если пользовательский работать.

Каковы преимущества создает пользовательский интерфейс наклейки?

1. Позволяет приложению настраивать способ отображения наклейки для пользователей приложения. Например чтобы присутствует наклейки в формат, отличный от Стандартная сетка макета или на разных цветном фоне.
2. Позволяет наклейки для динамически создаваться из кода вместо включения как статическое изображение активы.
3. Позволяет наклейку графические активы для динамически загружаться из разработчика веб-сервер без необходимости выпуске новой версии в App Store.
4. Позволяет доступ камеры устройства для создания наклейки на лету.
5. Позволяет для покупки из приложений, поэтому пользователи могут купить дополнительные наклейки из внутри приложения.

Чтобы создает пользовательский интерфейс наклейку, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Запустите Visual Studio для Mac.
2. Откройте решение, чтобы добавить расширение приложения для сообщения. 
3. Выберите **iOS** > **расширения** > **расширение iMessage** и нажмите кнопку **Далее** кнопки: 

    [![](intro-to-message-app-extensions-images/message01.png "Выберите расширение iMessage")](intro-to-message-app-extensions-images/message01.png#lightbox)
4. Введите **имя расширения** и нажмите кнопку **Далее** кнопки: 

    [![](intro-to-message-app-extensions-images/message02.png "Введите имя расширения")](intro-to-message-app-extensions-images/message02.png#lightbox)
5. Нажмите кнопку **создать** кнопки для построения расширения: 

    [![](intro-to-message-app-extensions-images/message03.png "Нажмите кнопку \"Создать\"")](intro-to-message-app-extensions-images/message03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Запустите Visual Studio.
2. Откройте решение, чтобы добавить расширение сообщения приложения.
3. Выберите ** расширения iOS > расширение iMessage (iOS) ** и щелкните **Далее** кнопки:

    [![Выберите расширение iMessage (iOS)](intro-to-message-app-extensions-images/message01.w157-sml.png)](intro-to-message-app-extensions-images/message01.w157.png#lightbox)

4. Введите **имя** и нажмите кнопку **ОК** кнопки

-----

По умолчанию `MessagesViewController.cs` файл будет добавлен в решение. Это главная точка входа в расширение; он наследуется от `MSMessageAppViewController` класса.

Сообщения framework предоставляет классы для представления доступных наклейки для пользователя:

- `MSStickerBrowserViewController` — Управляет наклейки, отображаются в представление. Он также соответствует `IMSStickerBrowserViewDataSource` интерфейса для возврата величина наклейки и наклейки для индекса заданного браузера.
- `MSStickerBrowserView` — Это представление, доступных наклейки будет отображаться в.
- `MSStickerSize` -Решает размеры отдельных ячеек сетки наклейки, представленные в представлении браузера.

### <a name="creating-a-custom-sticker-browser"></a>Создание пользовательских наклейки браузер

Разработчик также можно настроить взаимодействие наклейки для пользователя, предоставляя пользовательский браузер наклейки (`MSMessageAppBrowserViewController`) в расширении приложения сообщения. Пользовательский браузер наклейки изменяет представления наклейки для пользователя они выбирается наклейки, чтобы включить в поток сообщений.

Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. В **панели решения**, правой кнопкой мыши имя проекта расширения и выберите **добавить** > **новый файл...**   >  **iOS | Apple Watch** > **контроллер интерфейса**.
2. Введите `StickerBrowserViewController` для **имя** и нажмите кнопку **New** кнопки: 

    [![](intro-to-message-app-extensions-images/browser01.png "Введите StickerBrowserViewController имени")](intro-to-message-app-extensions-images/browser01.png#lightbox)
3. Откройте `StickerBrowserViewController.cs` файл для редактирования.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. В **обозревателе решений**, правой кнопкой мыши имя проекта расширения и выберите **добавить** > **новый файл...**   >  **iOS | Apple Watch** > **контроллер интерфейса**.
2. Введите `StickerBrowserViewController` для **имя** и нажмите кнопку **New** кнопки: 

    [![](intro-to-message-app-extensions-images/browser01.w157-sml.png "Введите StickerBrowserViewController имени")](intro-to-message-app-extensions-images/browser01.w157.png#lightbox)
3. Откройте `StickerBrowserViewController.cs` файл для редактирования.

-----

Сделать `StickerBrowserViewController.cs` выглядеть следующим образом:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MonkeyStickers
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MSStickerSize stickerSize) : base (stickerSize)
        {
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            ...
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();
        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            return Stickers[(int)index];
        }
        #endregion
    }
}
```

Взгляните на код выше подробно. Создает хранилище для наклейки, которые данное расширение предоставляет:

```csharp
public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
```

Переопределяет два метода `MSStickerBrowserViewController` класс, предоставляющий данные для браузера из источника данных:

```csharp
public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
{
    return Stickers.Count;
}

public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
{
    return Stickers[(int)index];
}
```

`CreateSticker` Метод получает путь к ресурс изображения из пакета расширения и использует его для создания нового экземпляра `MSSticker` из этого ресурса, то он добавляет в коллекцию:

```csharp
private void CreateSticker (string assetName, string localizedDescription)
{

    // Get path to asset
    var path = NSBundle.MainBundle.PathForResource (assetName, "png");
    if (path == null) {
        Console.WriteLine ("Couldn't create sticker {0}.", assetName);
        return;
    }

    // Build new sticker
    var stickerURL = new NSUrl (path);
    NSError error = null;
    var sticker = new MSSticker (stickerURL, localizedDescription, out error);
    if (error == null) {
        // Add to collection
        Stickers.Add (sticker);
    } else {
        // Report error
        Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
    }
}
```

`LoadSticker` Метод вызывается из `ViewDidLoad` создать наклейку на именованного ресурса изображения (входит в пакет приложения) и добавьте его в коллекцию наклейки.

Чтобы реализовать пользовательский браузер наклейку, измените `MessagesViewController.cs` файл и это должно выглядеть следующим образом:

```csharp
using System;
using UIKit;
using Messages;

namespace MonkeyStickers
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public StickerBrowserViewController BrowserViewController { get; set;}
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();


            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);
        }
        #endregion
    }
}
```

Рассмотреть этот код подробно, он создает хранилище для пользовательского браузера:

```csharp
public StickerBrowserViewController BrowserViewController { get; set;}
```

И в `ViewDidLoad` метод, он создает и настраивает новый браузер:

```csharp
// Create new browser and configure it
BrowserViewController = new StickerBrowserViewController (MSStickerSize.Regular);
BrowserViewController.View.Frame = View.Frame;
BrowserViewController.ChangeBackgroundColor (UIColor.Gray);
```

Затем он добавляет браузер в представление, чтобы отобразить ее:

```csharp
// Add to view
AddChildViewController (BrowserViewController);
BrowserViewController.DidMoveToParentViewController (this);
View.AddSubview (BrowserViewController.View);
```

### <a name="further-sticker-customization"></a>Дальнейшая настройка наклейки

Дальнейшая настройка наклейки можно, включив только два класса в расширение приложения сообщение:

- `MSStickerView`
- `MSSticker`

Используя указанные выше методы, расширение может поддерживать выделение наклейку, не использует стандартный метод наклейки браузера. Кроме того можно переключить отображение наклейки между двух различных режимов просмотра:

- **Compact** -этот режим используется по умолчанию где наклейки занимает последние 25% представления сообщения.
- **Расширен** -наклейки вид заполняет все представление сообщения.

В этом представлении наклейки могут переключаться между этих режимов программным способом или вручную пользователем.

Взгляните на следующий пример обработки переключение между двух различных режимов просмотра. Для каждого состояния потребуются два разных контроллеров представлений. `StickerBrowserViewController` Дескрипторы **Compact** представление и выглядит следующим образом:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Messages;
using Foundation;

namespace MessageExtension
{
    public partial class StickerBrowserViewController : MSStickerBrowserViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set; }
        public List<MSSticker> Stickers { get; set; } = new List<MSSticker> ();
        #endregion

        #region Constructors
        public StickerBrowserViewController (MessagesViewController messagesAppViewController, MSStickerSize stickerSize) : base (stickerSize)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Private Methods
        private void CreateSticker (string assetName, string localizedDescription)
        {

            // Get path to asset
            var path = NSBundle.MainBundle.PathForResource (assetName, "png");
            if (path == null) {
                Console.WriteLine ("Couldn't create sticker {0}.", assetName);
                return;
            }

            // Build new sticker
            var stickerURL = new NSUrl (path);
            NSError error = null;
            var sticker = new MSSticker (stickerURL, localizedDescription, out error);
            if (error == null) {
                // Add to collection
                Stickers.Add (sticker);
            } else {
                // Report error
                Console.WriteLine ("Error, couldn't create sticker {0}: {1}", assetName, error);
            }
        }

        private void LoadStickers ()
        {

            // Load sticker assets from disk
            CreateSticker ("add", "Add New Sticker");
            CreateSticker ("canada", "Canada Sticker");
            CreateSticker ("clouds", "Clouds Sticker");
            CreateSticker ("tree", "Tree Sticker");
        }
        #endregion

        #region Public Methods
        public void ChangeBackgroundColor (UIColor color)
        {
            StickerBrowserView.BackgroundColor = color;

        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            LoadStickers ();

        }

        public override nint GetNumberOfStickers (MSStickerBrowserView stickerBrowserView)
        {
            return Stickers.Count;
        }

        public override MSSticker GetSticker (MSStickerBrowserView stickerBrowserView, nint index)
        {
            // Wanting to add a new sticker?
            if (index == 0) {
                // Yes, ask controller to present add sticker interface
                MessagesAppViewController.AddNewSticker ();
                return null;
            } else {
                // No, return existing sticker
                return Stickers [(int)index];
            }
        }
        #endregion
    }
}
```

`AddStickerViewController` Обрабатывающий **разреженный** наклейку представление и внешний вид, как показано ниже:

```csharp
using System;
using Foundation;
using UIKit;
using Messages;

namespace MessageExtension
{
    public class AddStickerViewController : UIViewController
    {
        #region Computed Properties
        public MessagesViewController MessagesAppViewController { get; set;}
        public MSSticker NewSticker { get; set;}
        #endregion

        #region Constructors
        public AddStickerViewController (MessagesViewController messagesAppViewController)
        {
            // Initialize
            this.MessagesAppViewController = messagesAppViewController;
        }
        #endregion

        #region Override Method
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Build interface to create new sticker
            var cancelButton = new UIButton (UIButtonType.RoundedRect);
            cancelButton.TouchDown += (sender, e) => {
                // Cancel add new sticker
                MessagesAppViewController.CancelAddNewSticker ();
            };
            View.AddSubview (cancelButton);

            var doneButton = new UIButton (UIButtonType.RoundedRect);
            doneButton.TouchDown += (sender, e) => {
                // Add new sticker to collection
                MessagesAppViewController.AddStickerToCollection (NewSticker);
            };
            View.AddSubview (doneButton);
            
            ...
        }
        #endregion
    }
}
```

`MessageViewController` Реализует эти контроллеры представления для запрошенного состояние диска:

```csharp
using System;
using UIKit;
using Messages;

namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        #region Computed Properties
        public bool IsAddingSticker { get; set;}
        public StickerBrowserViewController BrowserViewController { get; set; }
        public AddStickerViewController AddStickerController { get; set;}
        #endregion

        #region Constructors
        protected MessagesViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Public Methods
        public void PresentStickerBrowser ()
        {
            // Is the Add sticker view being displayed?
            if (IsAddingSticker) {
                // Yes, remove it from view
                AddStickerController.RemoveFromParentViewController ();
                AddStickerController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (BrowserViewController);
            BrowserViewController.DidMoveToParentViewController (this);
            View.AddSubview (BrowserViewController.View);

            // Save mode
            IsAddingSticker = false;
        }

        public void PresentAddSticker ()
        {
            // Is the sticker browser being displayed?
            if (!IsAddingSticker) {
                // Yes, remove it from view
                BrowserViewController.RemoveFromParentViewController ();
                BrowserViewController.View.RemoveFromSuperview ();
            }

            // Add to view
            AddChildViewController (AddStickerController);
            AddStickerController.DidMoveToParentViewController (this);
            View.AddSubview (AddStickerController.View);

            // Save mode
            IsAddingSticker = true;
        }

        public void AddNewSticker ()
        {
            // Switch to expanded view mode
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void CancelAddNewSticker ()
        {
            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }

        public void AddStickerToCollection (MSSticker sticker)
        {
            // Add sticker to collection
            BrowserViewController.Stickers.Add (sticker);

            // Switch to compact view mode
            Request (MSMessagesAppPresentationStyle.Compact);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Create new browser and configure it
            BrowserViewController = new StickerBrowserViewController (this, MSStickerSize.Regular);
            BrowserViewController.View.Frame = View.Frame;
            BrowserViewController.ChangeBackgroundColor (UIColor.Gray);

            // Create new Add controller and configure it as well
            AddStickerController = new AddStickerViewController (this);
            AddStickerController.View.Frame = View.Frame;

            // Initially present the sticker browser
            PresentStickerBrowser ();
        }

        public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            base.DidTransition (presentationStyle);

            // Take action based on style
            switch (presentationStyle) {
            case MSMessagesAppPresentationStyle.Compact:
                PresentStickerBrowser ();
                break;
            case MSMessagesAppPresentationStyle.Expanded:
                PresentAddSticker ();
                break;
            }
        }
        #endregion
    }
}
```

Когда пользователь запрашивает для добавления новых наклейку их доступных коллекцию, новый `AddStickerViewController` становится видимым контроллером и представлением наклейки вводит **разреженный** представления:

```csharp
// Switch to expanded view mode
Request (MSMessagesAppPresentationStyle.Expanded);
```

При выборе пользователем, чтобы добавить наклейку, он добавляется к их доступные коллекции и **Compact** запрашивается представление:

```csharp
public void AddStickerToCollection (MSSticker sticker)
{
    // Add sticker to collection
    BrowserViewController.Stickers.Add (sticker);

    // Switch to compact view mode
    Request (MSMessagesAppPresentationStyle.Compact);
}
```

`DidTransition` Метод переопределяется, чтобы обрабатывать переключение между двумя режимами:

```csharp
public override void DidTransition (MSMessagesAppPresentationStyle presentationStyle)
{
    base.DidTransition (presentationStyle);

    // Take action based on style
    switch (presentationStyle) {
    case MSMessagesAppPresentationStyle.Compact:
        PresentStickerBrowser ();
        break;
    case MSMessagesAppPresentationStyle.Expanded:
        PresentAddSticker ();
        break;
    }
}
``` 

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается расширение сообщений приложения в решении Xamarin.iOS, которое интегрируется с **сообщений** приложения и присутствуют новые функциональные возможности для пользователя. Он рассматривается использование расширения для отправки текста, наклейки, файлов мультимедиа и интерактивные сообщения.



## <a name="related-links"></a>Связанные ссылки

- [Построитель мороженого (пример)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Справочник по сообщений](https://developer.apple.com/reference/messages)
- [Руководство по программированию расширения приложения](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
