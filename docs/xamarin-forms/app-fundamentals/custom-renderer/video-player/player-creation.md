---
title: Создание видеопроигрывателей платформы
description: В этой статье объясняется, как реализовать пользовательское средство отрисовки видеопроигрывателя на каждой платформе, с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0090ec798e8d7b1dfb9bd8e25f09d71ec0353b45
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171915"
---
# <a name="creating-the-platform-video-players"></a>Создание видеопроигрывателей платформы

[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) решение содержит весь код для реализации видеопроигрывателя для Xamarin.Forms. Она также включает ряд страниц, который демонстрирует использование проигрывателя видео в приложении. Все `VideoPlayer` кода и его платформу модулей подготовки отчетов находятся в папках проекта с именем `FormsVideoLibrary`, а также использовать пространство имен `FormsVideoLibrary`. Это следует сделать легко скопировать файлы в приложении и ссылки на классы.

## <a name="the-video-player"></a>Видеопроигрыватель

[ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) Класс является частью **VideoPlayerDemos** библиотеки .NET Standard, которая является общей для платформы. Он является производным от `View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

Члены этого класса (и `IVideoPlayerController` интерфейс) описаны в последующих статьях.

Каждой из платформ содержит класс с именем `VideoPlayerRenderer` , содержащий специфические для платформы код для реализации видеопроигрывателя. Основная задача этого модуля подготовки отчетов является создание видеопроигрывателя для данной платформы.

### <a name="the-ios-player-view-controller"></a>Контроллер представления проигрыватель iOS

При реализации проигрыватель видео в iOS участвуют несколько классов. Во-первых, приложение создает [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) и затем задает [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) свойство для объекта типа [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Дополнительные классы необходимы, когда игрок назначается источник видео.

Все модули подготовки отчетов, iOS, такие как [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) содержит `ExportRenderer` атрибут, определяющий `VideoPlayer` представление с помощью модуля подготовки отчетов:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

Обычно модуль подготовки отчетов, который задает элемент управления платформы является производным от [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) класса, где `View` подход — Xamarin.Forms `View` производных (в этом случае `VideoPlayer`) и `NativeView` является iOS `UIView` класс, производный от класса модуля подготовки отчетов. Для этого модуля подготовки отчетов, просто присваивается универсального аргумента `UIView`, по причинам, вскоре вы увидите.

Когда модуль подготовки отчетов основан на `UIViewController` производных (что является), а затем класс должен переопределять `ViewController` свойства и возвращаемые контроллера представления, в данном случае `AVPlayerViewController`. То есть назначение `_playerViewController` поля:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

Основной обязанностью `OnElementChanged` переопределение заключается в проверке, если `Control` свойство `null` и, если да, создать элемент управления платформы и передать его в `SetNativeControl` метод. В этом случае этот объект доступен только из `View` свойство `AVPlayerViewController`. Что `UIView` производных происходит быть закрытый класс с именем `AVPlayerView`, но так как он является закрытым, он не явно как второй аргумент универсального `ViewRenderer`.

Обычно `Control` свойство класса модуля подготовки отчетов, затем ссылается на `UIView` используется для реализации модуля подготовки отчетов, но в данном случае `Control` свойство нигде не используется.

### <a name="the-android-video-view"></a>Представление Android видео

Модуль подготовки отчетов, Android для `VideoPlayer` основан на Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) класса. Тем не менее если `VideoView` используется сама по себе для воспроизведения видео в приложения Xamarin.Forms, видео заливки отведенное области для `VideoPlayer` без сохранения правильных пропорций. Для этого-либо причине (как вы вскоре увидите), `VideoView` сделана дочерним Android `RelativeLayout`. Объект `using` директива определяет `ARelativeLayout` чтобы отличить его от Xamarin.Forms `RelativeLayout`, и это второй универсальный аргумент `ViewRenderer`:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

Начиная с версии 2.5 Xamarin.Forms, Android модулей подготовки отчетов должен содержать конструктор с `Context` аргумент.

`OnElementChanged` Переопределение создает обе `VideoView` и `RelativeLayout` и задает параметры макета для `VideoView` по центру в пределах `RelativeLayout`.


```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Обработчик для `Prepared` событие присоединена в этот метод и отключении от `Dispose` метод. Это событие срабатывает, когда `VideoView` имеет достаточно данных для начала воспроизведения видеофайла.

### <a name="the-uwp-media-element"></a>Элемент мультимедиа универсальной платформы Windows

В универсальной платформы Windows (UWP), является наиболее распространенных видеопроигрыватель [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Эта документация из `MediaElement` указывает, что [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) следует использовать вместо этого, когда она необходима только для поддержки версий Windows 10, начиная с сборка 1607.

`OnElementChanged` Необходимо создать переопределение `MediaElement`, настройте несколько обработчиков событий и передачи `MediaElement` объект `SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

Два обработчика событий, отсоединены в `Dispose` событий модуля подготовки отчетов.

## <a name="showing-the-transport-controls"></a>С элементами управления транспорта

Все видео проигрывателей, включенных в платформы поддерживают набор по умолчанию элементы управления транспортировкой, включающие кнопки для воспроизведения и приостановки и строка укажите текущую позицию в видео, а также переместить в новую позицию.

`VideoPlayer` Класс определяет свойство, именуемое `AreTransportControlsEnabled` и задает значение по умолчанию `true`:


```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Несмотря на то, что это свойство имеет оба `set` и `get` методы доступа, модуль подготовки отчетов должен обрабатывать случаи, только в том случае, если свойство имеет значение. `get` Доступа просто возвращает текущее значение свойства.

Свойства, такие как `AreTransportControlsEnabled` обрабатываются в модулях подготовки отчетов платформы двумя способами:

- — В первый раз, когда создает Xamarin.Forms `VideoPlayer` элемент. Это указано в `OnElementChanged` переопределить модуля подготовки отчетов при `NewElement` свойство не является `null`. В настоящее время можно установить модуль подготовки отчетов — это проигрыватель видео собственные платформы, от начального значения свойства, как определено в `VideoPlayer`.

- Если свойство в `VideoPlayer` позже меняется, то `OnElementPropertyChanged` вызывается метод в модуле подготовки отчетов. Благодаря этому модуль подготовки отчетов для обновления платформы видеопроигрывателя, на основе нового параметра свойства.

В следующих разделах рассматриваются как `AreTransportControlsEnabled` свойство обрабатывается на каждой платформе.

### <a name="ios-playback-controls"></a>элементы управления воспроизведением iOS

Свойство iOS `AVPlayerViewController` , управляющий отображение транспорта controls является [ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/). Вот как это свойство задается в iOS `VideoViewRenderer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

`Element` Свойство модуля подготовки отчетов ссылается на `VideoPlayer` класса.

### <a name="the-android-media-controller"></a>Контроллер мультимедиа Android

В Android, отображение элементов управления движением требует создания [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) объекта и его сопоставления с `VideoView` объекта. Механизм демонстрируются в `SetAreTransportControlsEnabled` метод:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>Свойства транспорта управления универсальной платформы Windows

UWP `MediaElement` определяет свойство, именуемое [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), таким образом, чтобы свойство задается из `VideoPlayer` свойство с тем же именем:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

Еще одно свойство необходим начать воспроизведение видео: это очень важно, `Source` свойство, которое ссылается на файл видео. Реализация `Source` свойство описан в следующей статье [воспроизведение видео через Интернет](web-videos.md).


## <a name="related-links"></a>Связанные ссылки

- [Видеодемонстрации Player (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
