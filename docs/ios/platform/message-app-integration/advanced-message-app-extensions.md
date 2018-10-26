---
title: Расширения приложений дополнительные сообщения в Xamarin.iOS
description: В этой статье показано дополнительные методы для работы с расширений сообщений приложения в решении Xamarin.iOS, которое интегрируется с приложения для обмена сообщениями и представляет новые функциональные возможности для пользователя.
ms.prod: xamarin
ms.assetid: 394A1FDA-AF70-4493-9B2C-4CFE4BE791B6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: baceb59116dd907918b34eca4f44293051190954
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120532"
---
# <a name="advanced-message-app-extensions-in-xamarinios"></a>Расширения приложений дополнительные сообщения в Xamarin.iOS

_В этой статье показано дополнительные методы для работы с расширений сообщений приложения в решении Xamarin.iOS, которое интегрируется с приложения для обмена сообщениями и представляет новые функциональные возможности для пользователя._


Новое в iOS 10, расширение сообщения приложение интегрируется с **сообщений** приложения, по возможности представлены новые функциональные возможности для пользователя. Расширения можно отправлять текст, наклейки, файлов мультимедиа и интерактивных сообщений.

## <a name="about-message-app-extensions"></a>О расширениях сообщение приложения

Как уже говорилось выше, расширение сообщения приложение интегрируется с **сообщений** приложения, по возможности представлены новые функциональные возможности для пользователя. Расширения можно отправлять текст, наклейки, файлов мультимедиа и интерактивных сообщений. Доступны два типа расширения приложения сообщение:

- **Пакеты наклейки** -содержит коллекцию наклейки, которые пользователь может добавить сообщение. Наклейка с пакеты могут создаваться без написания кода.
- **iMessage приложения** -можно представить пользовательский интерфейс пользователя в приложения "сообщения" для выбора наклейки, ввода текста, включая файлы мультимедиа (с преобразованием необязательно типов) и создание, изменение и отправки сообщений взаимодействия.

Сообщения приложений расширения предоставляют три основных типа содержимого:

- **Интерактивные сообщения** -— это тип содержимого пользовательское сообщение, которое создает приложение, когда пользователь касается сообщения, приложение будет запускаться на переднем плане.
- **Наклейки** -представляют собой образы, созданные в приложения, которое может быть включено в сообщения, отправленные между пользователями. См. в разделе наших [построитель мороженого](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/) пример приложения пример реализации наклейки пакет приложения.
- **Другое содержимое поддерживается** -приложение может предоставить содержимое, например фотографий, видео, текста или ссылки типа, который всегда поддерживается приложения для обмена сообщениями.

Новое в iOS 10, приложение сообщения теперь содержит свой собственный выделенный, встроенные App Store. Все приложения, которые включают расширения приложений сообщение будет отображаться и предлагаются в это хранилище. Новый контейнер приложения и сообщения будут отображаться все приложения, которые были загружены из Store приложения сообщений для предоставления быстрого доступа для пользователей.

Также новые в iOS 10, Apple добавил однозначного соответствия примитивов встроенного приложения позволяющие пользователям легко находить приложение. Например если один пользователь отправляет содержимое в другой из приложения, второй пользователь не имеет (например, наклейку на примере), имя приложения, отправляющего находится в списке содержимого в журнале сообщений. Если пользователь касается приложения имя, App Store сообщения, мы открыть и приложения, выбранного в хранилище.

Сообщение расширений приложений похожи на существующие приложения iOS, что разработчик знаком для создания и Администраторы получат доступ ко всем стандартной платформы и возможности стандартных операций ввода-вывода приложения. Пример:

- Они имеют доступ к покупки в приложении.
- Они имеют доступ к Apple Pay.
- Они имеют доступ к оборудования устройства, например камеры.

Сообщение расширений приложений поддерживаются только в iOS 10, однако содержимое, которые отправляют эти расширения можно просматривать на устройствах, watchOS и macOS. Новый _недавних объектов страницы_ добавлен watchOS 3, отображает последние наклейки, который был отправлен с телефона, включая полученные сообщения расширений приложений, и разрешить пользователю отправлять эти наклейки в часах Apple Watch.

## <a name="about-interactive-messages"></a>Об интерактивных сообщений

Интерактивные сообщения представления пузырек пользовательские сообщения и предоставляемые расширением сообщения приложения. Они позволяют пользователю создавать интерактивные содержимого сообщений, вставьте его в поле ввода сообщения и отправить его.

[![](advanced-message-app-extensions-images/interactive01.png "Создание содержимого интерактивных сообщений")](advanced-message-app-extensions-images/interactive01.png#lightbox)

Получателя может отвечать на сообщения об интерактивных, коснувшись ее пузырьковой сообщения в журнале сообщений, чтобы загрузить расширение сообщение приложения, создавшего его. Расширение будет запущенное весь экран и разрешает пользователю для составления ответа и отправить его обратно исходного пользователя.

[![](advanced-message-app-extensions-images/interactive02.png "Расширение запустить весь экран")](advanced-message-app-extensions-images/interactive02.png#lightbox)


Ниже подробно рассматриваются следующие темы:

- Общие сведения об API сообщений
- Жизненный цикл расширения
- Сообщения
- Отправка сообщения

## <a name="messages-api-overview"></a>Общие сведения об API сообщений

При вызове пользователем расширение приложения сообщение будет отображаться в нижней части сообщения из журнала в режиме компактное представление:

[![](advanced-message-app-extensions-images/interactive03.png "Общие сведения об API сообщений")](advanced-message-app-extensions-images/interactive03.png#lightbox)

1. `MSMessageAppViewController` Объект в расширение сообщений приложения является основной класс, который вызывается, когда пользователю отображается представление расширения.
2. Диалог представляется пользователю как `MSConversation` экземпляр объекта.
3. `MSMessage` Класс представляет заданного пузырек сообщения в диалоге.
4. `MSSession` Определяет способ отправки сообщения.
5. `MSMessageTemplateLayout` Управляет отображением сообщения

## <a name="the-extension-lifecycle"></a>Жизненный цикл расширения

Взгляните на процесс расширения приложения сообщение активации:

[![](advanced-message-app-extensions-images/interactive04.png "Процесс расширения приложения сообщения, активацию")](advanced-message-app-extensions-images/interactive04.png#lightbox)

1. При запуске расширения (например, из панели приложений) сообщения приложение запустит процесс.
2. `DidBecomeActive` Вызывается и передается метод `MSConversation` , представляющий диалога, который выполняется расширение приложения сообщения.
3. Так как расширение основан `UIViewController` оба `ViewWillAppear` и `ViewDidAppear` вызываются.

Теперь взгляните на процесс расширения приложения сообщение становится деактивации:

[![](advanced-message-app-extensions-images/interactive05.png "Процесс расширения приложения сообщение становится деактивации")](advanced-message-app-extensions-images/interactive05.png#lightbox)

1. При деактивации расширение приложения сообщение `ViewWillDisappear` сначала вызвать метод.
2. Затем `ViewDidDisappear` метод будет вызываться.
3. `WillResignActive` Вызывается и передается метод `MSConversation` , представляющий диалога, который выполняется расширение приложения сообщения. На этом этапе соединение между приложения для обмена сообщениями и расширение, подготовке к освобождению.
4. В более позднем этапе процесс завершается, приложения для обмена сообщениями.

Так как расширение является небольшим периодом жизни процесса, она прекращается агрессивно системой в целях экономии обработки и электропитания от батареи. Разработчику следует помнить это при проектировании и реализации расширения сообщения приложения.

## <a name="composing-a-message"></a>Сообщения

После расширения приложения сообщения выполняется в процессе и отображается пользовательский интерфейс, для создания нового сообщения можно использовать следующий код:

```csharp
MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
{
    var components = new NSUrlComponents {
        QueryItems = iceCream.QueryItems
    };

    var layout = new MSMessageTemplateLayout {
        Image = iceCream.RenderSticker (true),
        Caption = caption
    };

    var message = new MSMessage (session ?? new MSSession()) {
        Url = components.Url,
        Layout = layout
    };

    return message;
}
```

Этот код создает новый `MSMessage` и задает несколько свойств (таких как `Url`). Хотя сообщения могут создаваться только на устройствах iOS, отправкой для iOS и macOS для отображения.

Если пользователь нажимает на пузырьковой сообщения диалога в Mac OS, Mac попытается открыть адрес, указанный в URL-адрес в веб-браузере. Таким образом показать, что машины под управлением некоторых представление сообщения в веб-браузер на macOS можно разработчика веб-сайта.

`AccessibilityLabel` Свойство используется программами чтения с экрана для прочитать описание диалога для пользователя. `Layout` Свойство определяет, каким образом сообщение отображается, в настоящее время только `MSMessageTemplateLayout` поддерживается и выглядит следующим образом:

[![](advanced-message-app-extensions-images/interactive06.png "Шаблон MSMessageTemplateLayout")](advanced-message-app-extensions-images/interactive06.png#lightbox)

`Image` Свойство `MSMessageTemplateLayout` предоставляет содержимое для основной части MessageBubble на экране. `MediaFileUrl` Свойство также предоставляет содержимое тела сообщения пузырька, но позволяет для содержимого, которое не поддерживается `UIImage` (например, видеофайл, необходимо выполнить цикл в фоновом режиме). Если оба `Image` и `MediaFileUrl` предоставляются свойства, `Image` свойство будет иметь приоритет. `MediaFileUrl` Поддерживает PNG, JPEG, GIF и видео (в любом формате, который может воспроизводиться платформой проигрывателя мультимедиа) форматы файлов мультимедиа.

Рекомендуемый размер — 300 x 300 пикселей с разрешением в 3 раза. Немного более крупные и мелкие активы, также являются допустимыми, и Apple предлагает тестирование с помощью нескольких различных размеров для получения наилучших результатов. Сообщение приложения будет уменьшить выборку и масштабировать этот носитель при необходимости.

Если ресурсы отправляются получателю, любые мультимедиа, прикрепленного будет автоматически перекодированные приложением сообщения для оптимизации их из передачи по сети. По этой причине Apple не рекомендует включая текст на носителе, который присоединяется к сообщению, так как мультимедиа будет уменьшен и сжимаются для передачи, тем самым потенциально визуализации текста американские – нет.

`ImageTitle` И `ImageSubtitle` свойства укажите описание для медиафайлов, которые представлены в пузырьковой сообщения. Эти свойства будут отправляться как текст получающим устройством где они недоставало отображается в левом нижнем углу изображения.

`Caption`, `SubCaption`, `TrailingCaption` И `TrailingSubcaption` свойства Дополнительно, какое изображение и отображается в разделе под изображением. Все эти свойства для параметра `null` создаст пузырек сообщение без области названия:

[![](advanced-message-app-extensions-images/interactive07.png "Пузырек сообщение без области названия")](advanced-message-app-extensions-images/interactive07.png#lightbox)

Последний следует заметить, что приложения для обмена сообщениями будет рисовать значок расширения сообщений приложения в левом верхнем углу пузырька сообщения.

## <a name="sending-a-message"></a>Отправка сообщения

Один раз `MSMessage` был состоит, можно использовать для отправки в него следующий код:

```csharp
public void SendMessage (MSMessage message)
{
    // Insert the message into the conversation
    ActiveConversation.InsertMessage (message, (error) => {
        // Did the message send successfully?
        if (error == null) {
            // Handle successful send
        } else {
            // Report Error
            Console.WriteLine ("Error: {0}", error);
        }
    };

}
```

`ActiveConversation` Свойство `MSMessagesAppViewController` будет содержать текущий сеанс, расширение сообщения приложения был запущен в.

Вызовите `InsertMessage` из `MSConversation` сообщения в диалоге и обработки ошибок, которые могут возникнуть. Если сообщение успешно включен, сообщение пузырек будет отображаться в поле ввода.

Кроме того расширение может отправлять различные типы данных для диалога, такие как:

- **Текст** - `ActiveConversation.InsertText ("Message", (error) => {...});`
- **Вложения** - `ActiveConversation.InsertAttachment (new NSUrl ("path"), "filename", (error) => {...});`
- **Наклейки**  -  `ActiveConversation.InsertSticker (sticker, (obj) => {...});` где `sticker` является `MSSticker`.

Когда новое содержимое для поля ввода, пользователь не сможет отправить сообщение, нажав синюю **отправки** кнопку (так же, как любое обычное сообщение). Нет способа для расширения приложения сообщение автоматически отправлять содержимое, этот процесс полностью находится под контролем пользователя.

## <a name="handling-the-compact-and-expanded-modes"></a>Режимы Compact и развернутым обработки

Расширение приложения сообщения могут отображаться в одном из двух различных режимов просмотра:

[![](advanced-message-app-extensions-images/interactive08.png "Расширение приложения сообщения, отображаются в двух различных режимов просмотра: Compact & развернуто")](advanced-message-app-extensions-images/interactive08.png#lightbox)

- **Compact** -этот режим используется по умолчанию где расширение сообщение приложения занимает последние 25% представления сообщения. На компактный режим приложение имеет доступ к клавиатуре, горизонтальная прокрутка или распознавателей жестов проведите по экрану. Приложение может получить доступ к полю входных данных и вызовы `InsertMessage` сразу же будет отображаться для пользователя существует.
- **Расширен** -расширение приложения сообщение заполняет все представление сообщения. Он не имеет доступа к полю входных данных, но имеет доступа к клавиатуры, горизонтальная прокрутка и проведите по экрану распознавателей жестов.

Расширение приложения сообщения могут переключаться между этими режимами программным способом или вручную пользователем в любое время и должен быть мгновенной быстро реагировать на любые изменяет режим в представлении.

Взгляните на следующий пример обработки переключение между двух различных режимов просмотра. Для каждого состояния потребуются два разных контроллеров представлений. `StickerBrowserViewController` Дескрипторы **Compact** представление и `AddStickerViewController` обрабатывающий **разреженный** представления:

```csharp
using System;

using Messages;
using Foundation;
using UIKit;

namespace MessagesExtension {
    public partial class MessagesViewController : MSMessagesAppViewController, IIceCreamsViewControllerDelegate, IBuildIceCreamViewControllerDelegate {
        public MessagesViewController (IntPtr handle) : base (handle)
        {
        }

        public override void WillBecomeActive (MSConversation conversation)
        {
            base.WillBecomeActive (conversation);

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, PresentationStyle);
        }

        public override void WillTransition (MSMessagesAppPresentationStyle presentationStyle)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected an active converstation");

            // Present the view controller appropriate for the conversation and presentation style.
            PresentViewController (conversation, presentationStyle);
        }

        void PresentViewController (MSConversation conversation, MSMessagesAppPresentationStyle presentationStyle)
        {
            // Determine the controller to present.
            UIViewController controller;

            if (presentationStyle == MSMessagesAppPresentationStyle.Compact) {
                // Show a list of previously created ice creams.
                controller = InstantiateIceCreamsController ();
            } else {
                var iceCream = new IceCream (conversation.SelectedMessage);
                controller = iceCream.IsComplete ? InstantiateCompletedIceCreamController (iceCream) : InstantiateBuildIceCreamController (iceCream);
            }

            foreach (var child in ChildViewControllers) {
                child.WillMoveToParentViewController (null);
                child.View.RemoveFromSuperview ();
                child.RemoveFromParentViewController ();
            }

            AddChildViewController (controller);
            controller.View.Frame = View.Bounds;
            controller.View.TranslatesAutoresizingMaskIntoConstraints = false;
            View.AddSubview (controller.View);

            controller.View.LeftAnchor.ConstraintEqualTo (View.LeftAnchor).Active = true;
            controller.View.RightAnchor.ConstraintEqualTo (View.RightAnchor).Active = true;
            controller.View.TopAnchor.ConstraintEqualTo (View.TopAnchor).Active = true;
            controller.View.BottomAnchor.ConstraintEqualTo (View.BottomAnchor).Active = true;

            controller.DidMoveToParentViewController (this);
        }

        UIViewController InstantiateIceCreamsController ()
        {
            // Instantiate a `IceCreamsViewController` and present it.
            var controller = Storyboard.InstantiateViewController (IceCreamsViewController.StoryboardIdentifier) as IceCreamsViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an IceCreamsViewController from the storyboard");

            controller.Builder = this;
            return controller;
        }

        UIViewController InstantiateBuildIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (BuildIceCreamViewController.StoryboardIdentifier) as BuildIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an BuildIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            controller.Builder = this;

            return controller;
        }

        public UIViewController InstantiateCompletedIceCreamController (IceCream iceCream)
        {
            // Instantiate a `BuildIceCreamViewController` and present it.
            var controller = Storyboard.InstantiateViewController (CompletedIceCreamViewController.StoryboardIdentifier) as CompletedIceCreamViewController;
            if (controller == null)
                throw new Exception ("Unable to instantiate an CompletedIceCreamViewController from the storyboard");

            controller.IceCream = iceCream;
            return controller;
        }

        public void DidSelectAdd (IceCreamsViewController controller)
        {
            Request (MSMessagesAppPresentationStyle.Expanded);
        }

        public void Build (BuildIceCreamViewController controller, IceCreamPart iceCreamPart)
        {
            var conversation = ActiveConversation;
            if (conversation == null)
                throw new Exception ("Expected a conversation");

            var iceCream = controller.IceCream;
            if (iceCream == null)
                throw new Exception ("Expected the controller to be displaying an ice cream");

            string messageCaption = string.Empty;
            var b = iceCreamPart as Base;
            var s = iceCreamPart as Scoops;
            var t = iceCreamPart as Topping;

            if (b != null) {
                iceCream.Base = b;
                messageCaption = "Let's build an ice cream";
            } else if (s != null) {
                iceCream.Scoops = s;
                messageCaption = "I added some scoops";
            } else if (t != null) {
                iceCream.Topping = t;
                messageCaption = "Our finished ice cream";
            } else {
                throw new Exception ("Unexpected type of ice cream part selected.");
            }

            // Create a new message with the same session as any currently selected message.
            var message = ComposeMessage (iceCream, messageCaption, conversation.SelectedMessage?.Session);

            // Add the message to the conversation.
            conversation.InsertMessage (message, null);

            // If the ice cream is complete, save it in the history.
            if (iceCream.IsComplete) {
                var history = IceCreamHistory.Load ();
                history.Append (iceCream);
                history.Save ();
            }

            Dismiss ();
        }

        MSMessage ComposeMessage (IceCream iceCream, string caption, MSSession session = null)
        {
            var components = new NSUrlComponents {
                QueryItems = iceCream.QueryItems
            };

            var layout = new MSMessageTemplateLayout {
                Image = iceCream.RenderSticker (true),
                Caption = caption
            };

            var message = new MSMessage (session ?? new MSSession()) {
                Url = components.Url,
                Layout = layout
            };

            return message;
        }
    }
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

При необходимости мог бы использовать приложение `WillTransition` метод для обработки изменения режима представления, прежде чем будет предоставлен пользователю (как показано в приведенном выше примере построитель Icecream). Дополнительные сведения см. в разделе наших [дальнейшую настройку наклейки](~/ios/platform/message-app-integration/intro-to-message-app-extensions.md) документации.

## <a name="replying-to-a-message"></a>Ответ на полученное сообщение

Существует два варианта, расширение сообщения приложения нужно будет обрабатывать при ответе на сообщение:

[![](advanced-message-app-extensions-images/interactive09.png "Расширение приложения сообщения в режимах неактивно и активный")](advanced-message-app-extensions-images/interactive09.png#lightbox)

- **Расширение — неактивный** -есть пузырьков сообщение расширение приложения для сообщения записи сообщения, когда пользователь выберет для активации расширения и по-прежнему интерактивного диалога.
- **Расширение активен** — когда пользователь выберет пузырьковой сообщение расширение приложения сообщение в записи сообщения, чтобы перейти в режим представления с помощью расширенных и продолжить интерактивный процесс с места остановки.

### <a name="the-extension-is-inactive"></a>Расширение неактивен.

Когда сообщение пузырек нажатии пользователем в расшифровку дикторского текста сообщения и расширение приложения сообщений неактивна, произойдет следующий процесс:

[![](advanced-message-app-extensions-images/interactive10.png "Обработка неактивных пузырьковой сообщения")](advanced-message-app-extensions-images/interactive10.png#lightbox)

1. Пользователь касается пузырьковой сообщения расширения.
2. При запуске расширения сообщения приложение запустит процесс.
3. `DidBecomeActive` Вызывается и передается метод `MSConversation` , представляющий диалога, который выполняется расширение приложения сообщения.
4. Так как расширение основан `UIViewController` оба `ViewWillAppear` и `ViewDidAppear` вызываются.

Когда процесс завершен, расширение сообщения приложения отображаются в режиме просмотра с помощью расширенных.

### <a name="the-extension-is-active"></a>Расширение активен.

Когда пузырек сообщение нажатии пользователем в расшифровку дикторского текста сообщения и сообщения приложения расширение будет активно, произойдет следующий процесс:

[![](advanced-message-app-extensions-images/interactive11.png "Обработка active пузырьковой сообщения")](advanced-message-app-extensions-images/interactive11.png#lightbox)

1. Пользователь касается пузырьковой сообщения расширения.
2. Так как расширение приложения для сообщения уже активен, `WillTransition` метод `MSMessagesAppViewController` вызывается для обработки, переключение с сжатия в режиме просмотра с помощью расширенных.
3. `DidSelectMessage` Метод `MSMessagesAppViewController` вызывается и передается `MSMessage` и `MSConversation` , к которому принадлежит сообщение пузырька.
4. `DidTransition` Метод `MSMessagesAppViewController` вызывается для обработки, переключение с сжатия в режиме просмотра с помощью расширенных.

Опять же когда процесс завершен, расширение сообщения приложения отображаются в режиме просмотра с помощью расширенных.

### <a name="accessing-the-selected-message"></a>Доступ к выбранное сообщение

В любом случае, когда пользователь касается пузырек сообщений, принадлежащих расширение приложения для сообщения, его нужно будет получить доступ к `MSMessage` , было выполнено `SelectedMessage` свойство `MSConversation`.

Пример:

```csharp
using System;
using Foundation;
using Messages;
using UIKit;


namespace MessageExtension
{
    public partial class MessagesViewController : MSMessagesAppViewController
    {
        ...

        #region Override Methods
        public override void DidSelectMessage (MSMessage message, MSConversation conversation)
        {
            base.DidSelectMessage (message, conversation);

            // Get selected message
            var selected = conversation.SelectedMessage;

            // Present the user interface to continue editing
            // the conversation
            ...
        }
        #endregion
    }
}
```

Выбранное сообщение должно отображаться в пользовательском Интерфейсе расширение сообщения приложения и пользователю должно быть разрешено для составления ответа.

## <a name="removing-partially-completed-messages"></a>Удаление частично завершено сообщений

В процессе передачи по различным этапам интерактивного диалога между двумя пользователя в диалоге, частично завершенной всплывающие сообщения можно запустить загромождать расшифровку дикторского текста сообщения:

[![](advanced-message-app-extensions-images/interactive12.png "Можно частично завершенной сообщение поднимается засорять расшифровку дикторского текста сообщения")](advanced-message-app-extensions-images/interactive12.png#lightbox)

Вместо этого расширения приложения сообщение следует свернуть предыдущих всплывающие сообщения в краткий комментарий в расшифровку дикторского текста сообщения:

[![](advanced-message-app-extensions-images/interactive13.png "Свертывание предыдущих всплывающие сообщения в сообщение записи")](advanced-message-app-extensions-images/interactive13.png#lightbox)

Это делается с помощью `MSSession` Чтобы свернуть все существующие действия. Поэтому `DidSelectMessage` метод `MSMessagesAppViewController` класса может быть изменено для выглядеть следующим образом:

```csharp
public override void DidSelectMessage (MSMessage message, MSConversation conversation)
{
    base.DidSelectMessage (message, conversation);

    MSSession session;
    var summary = "";

    // Get selected message
    var selected = conversation.SelectedMessage;

    // Does the selected message already have a session?
    if (selected.Session == null) {
        // No, create a new session
        session = new MSSession ();
        summary = "Let's build an ice cream";
    } else {
        // Yes, use the existing session
        session = selected.Session;
        summary = "I added some scoops";
    }

    // Create an instance of the message being composed
    var existingMessage = new MSMessage (session);
    message.SummaryText = summary;
    ...

    // Present the user interface to continue editing
    // the conversation
    ...
}
```

Если выбранное сообщение уже выход из `MSSession`, в противном случае используется новый `MSSession` создается. `SummaryText` Свойство `MSMessage` используется для добавления названия свернутого предыдущие шаги. Если `SummaryText` свойству `null`, предыдущие шаги из диалога будет полностью удален из Стенограмму диалога.

## <a name="advanced-message-api-features"></a>Дополнительные функции API сообщения

С основные функции этого нового API сообщение, подробно рассматривается выше затем изучите некоторые из более сложных функций, которые Apple создала в платформу.

Во-первых, есть несколько других методов переопределения в `MSMessagesAppViewController` класс, который обеспечивают более широкий доступ для диалога:

- `DidStartSendingMessage` -Это вызывается, когда пользователь касается кнопки «Отправить». Это не означает, что сообщение фактически был доставки сообщения получателю, точно так же, что процесс отправки был запущен.
- `DidCancelSendingMessage` -Это происходит, когда пользователь касается *X* кнопки в правом верхнем углу пузырька сообщения в диалоге записи.
- `DidReceiveMessage` — Этот метод вызывается в том случае, когда сообщение приложения расширение будет активно новое сообщение было получено из одного из участников диалога.

### <a name="group-conversations"></a>Групповые беседы

Расширение приложения сообщений может использоваться во время пользователей, участвующих в групповые беседы (с 3 или более отдельных пользователей), и это будет необходимо принимать во внимание при проектировании и реализации расширения приложения сообщения.

Взгляните на следующие взаимодействия в групповую беседу с трех пользователей:

[![](advanced-message-app-extensions-images/interactive14.png "Взаимодействие в групповую беседу с трех пользователей")](advanced-message-app-extensions-images/interactive14.png#lightbox)

1. Пользователь 1 отправляет группу интерактивному сообщению запросом пользователя 2 и 3 пользователя для выбора начинки гамбургер.
2. Пользователь 2 выбирает tomatoes.
3. 3 пользователем Соленья.
4. Пользователь 2 и 3 пользователя выбранные поступать на 1 пользователя в почти то же время. В результате выбора пользователем 2 сворачивается в строка сводки и недоступна. Этот случай может также была отражена, с выбором пользователя 2 элемента свернуты отображается и пользователь 3.

В любом случае такое поведение является нежелательным, как 1 пользователь должен иметь возможность доступа к пользователя 2 и 3 пользовательского выбора. Чтобы решить эту проблему, Apple предлагает что расширение приложения для сообщения состояния сообщения хранятся в облаке и использовать свойство URL-адрес `MSMessage` (который отправляется между пользователями) для доступа к это состояние.

Когда пользователь отправляет сообщение, маркер сеанса и оправлены в облако с текущим состоянием сообщения. При касании пузырек сообщения в диалоге записи, токен сеанса используется для получения текущего состояния сеанса из облака.

### <a name="sender-identifiers"></a>Идентификаторов отправителя

Для обсуждения, доступ к идентификатор отправителя сообщения, рассмотрим в качестве примера групповую беседу, приведенном выше:

[![](advanced-message-app-extensions-images/interactive15.png "Отправка идентификаторов групповую беседу")](advanced-message-app-extensions-images/interactive15.png#lightbox)

1. Опять же, 1 пользователь отправляет группу интерактивному сообщению запросом пользователя 2 и 3 пользователя для выбора начинки гамбургер.
2. Пользователь 3 выбирает Соленья.
3. Выбор элемента 3 пользователя приходит на пользователя 1 и 2 пользователя еще не ответил.
4. Так как Apple очень интересуют конфиденциальность пользователей, расширение сообщения приложения знает только о уникальный идентификатор (как `NSUUID`), назначенный каждый участник диалога. На локальном устройстве известен только идентификатор текущего пользователя.
5. `MSMessage` Имеет `SenderIdentifier` свойство, которое совпадает с одним пользователем в список участников известные к расширению.
6. Каждое устройство пользователя имеет собственную копию список участников, где снова, известен только идентификатор локального пользователя.
7. При отправке сообщения, его `SenderIdentifier` свойства также известно, локального пользователя.

Идентификаторов отправителя можно использовать одним из следующих способов:

- Просмотрев список участников расширения можно получить число пользователей в диалоге.
- Когда модуль получает сообщение от пользователя, он может отслеживать связи идентификатор отправителя. Если поступает другое сообщение с таким идентификатором отправителя, расширение понимает, что один пользователь.
- Они могут использоваться для идентификации конкретного пользователя в диалоге.

Идентификатор отправителя можно использовать в любом из полей из `MSMessageTemplateLayout` ставится знак доллара (`$`). Пример:

```csharp
// Pass along the sender identifier
var layout = new MSMessageTemplateLayout()
layout.Caption = string.format("${0} wants pickles.",Conversation.LocalParticipantIdentifier.UuidString);
```

Когда приложения "сообщения" отображает сообщение пузырек с этим типом форматирования, она заменит `$uuid...` с имя контакта из участников диалога, отправившего сообщение.

Идентификатор отправителя является уникальным на каждом устройстве, поэтому просмотрев выше Обратите внимание на диаграмме устройства 1 пользователя и устройства пользователь 3 имеет другой уникальный идентификатор отправителя для каждого участника диалога.

Идентификаторов отправителя действуют на установку расширения приложения сообщения. Поэтому если пользователь удаляет и повторно устанавливает расширение приложения сообщение новых идентификаторов отправителя будет создаваться для установки нового.

Чтобы получить доступ к идентификаторов отправителя, расширение можно использовать следующий код:

```csharp
public override void DidStartSendingMessage (MSMessage message, MSConversation conversation)
{
    base.DidStartSendingMessage (message, conversation);

    // Get the sender's participant ID
    var senderID = message.SenderParticipantIdentifier;

    // Get the local participant ID
    var localID = conversation.LocalParticipantIdentifier;

    // Get the remote participant IDs
    var remoteIDs = conversation.RemoteParticipantIdentifiers;
}
```

## <a name="supported-platforms"></a>Поддерживаемые платформы

Интерактивные сообщения, создаваемые расширение приложения сообщения будут предоставляться на следующих платформах Apple:

- watchOS 3
- macOS Sierra
- iOS 10

Из трех платформ только в iOS 10 позволит пользователям создавать интерактивные сообщение. В macOS Sierra, если пользователь щелкает интерактивные сообщения пузырька, URL-адрес присоединены к `MSMessage` будут открываться в Safari и представление сообщения должны приводиться в нем.

В watchOS приложения для обмена сообщениями можно переходной интерактивному сообщению на подключенном устройстве iOS где пользователя можно сформировать ответ.

Новый интерфейс API сообщений поддерживает для резервной проверки подлинности при получении интерактивные сообщения в более старых платформ Apple:

- watchOS 2 +
- OS X 10.11 +
- iOS 9 +

Они будут предоставляться в виде резервной как два отдельных сообщения:

- Один будет образ, предоставленный шаблон макета.
- Другой будет URL-адрес, как указано в `MSMessage`.

## <a name="summary"></a>Сводка

В этой статье вы ознакомились с дополнительные методы для работы с расширений сообщений приложения в решении Xamarin.iOS, которое интегрируется с **сообщений** приложения и присутствуют новые функциональные возможности для пользователя.


## <a name="related-links"></a>Связанные ссылки

- [Построитель мороженого (пример)](https://developer.xamarin.com/samples/monotouch/ios10/IceCreamBuilder/)
- [Справочник по сообщений](https://developer.apple.com/reference/messages)
- [Руководство по программированию расширения приложения](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)
