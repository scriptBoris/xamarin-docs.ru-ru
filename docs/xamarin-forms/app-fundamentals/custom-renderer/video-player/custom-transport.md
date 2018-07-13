---
title: Элементы управления настраиваемых транспортировкой видео
description: В этой статье объясняется, как реализовать пользовательский транспорт элементов управления в приложение проигрывателя видео, с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 84870de28ffd30b2d29fb5d8fbea815e1fd0d9c4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996442"
---
# <a name="custom-video-transport-controls"></a>Элементы управления настраиваемых транспортировкой видео

Элементы управления транспортировкой видео проигрывателя включают кнопок, которые выполняют функции **воспроизведение**, **Пауза**, и **остановить**. Эти кнопки обычно определяются с помощью знакомых значков, а не текст и **воспроизведение** и **Пауза** функций обычно объединяются в одну кнопку.

По умолчанию `VideoPlayer` отображает транспорта элементов управления, поддерживаемых каждой платформы. При задании `AreTransportControlsEnabled` свойства `false`, эти элементы управления, подавляются. Вы можете управлять `VideoPlayer` программным способом или указать собственные элементы управления транспортировкой.

## <a name="the-play-pause-and-stop-methods"></a>Методы воспроизведения, приостановки и остановки

`VideoPlayer` Класс определяет три метода с именем `Play`, `Pause`, и `Stop` , реализуемые генерирует события:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Обработчики событий для этих событий задаются `VideoPlayerRenderer` класса каждой из платформ, как показано ниже:

### <a name="ios-transport-implementations"></a>реализации транспорта iOS

Версии iOS `VideoPlayerRenderer` использует `OnElementChanged` метод, чтобы задать обработчики для этих трех событий при `NewElement` свойство не `null` и отсоединяет обработчики событий при `OldElement` не `null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

Обработчики событий реализуются путем вызова методов на `AVPlayer` объекта. Существует не `Stop` метод `AVPlayer`, поэтому он имитируется Приостановка видео и перемещает позицию в начало.

### <a name="android-transport-implementations"></a>Реализации транспорта с Android

Android реализация аналогична реализации операций ввода-вывода. Обработчики для трех функций устанавливаются при `NewElement` не `null` и при отсоединении `OldElement` не `null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

Три функции вызывают методы, определенные `VideoView`.

### <a name="uwp-transport-implementations"></a>Реализации транспорта универсальной платформы Windows

UWP функции три транспорта реализуется очень похожа на iOS и Android реализации:

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
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>Состояние видеопроигрывателя

Реализация **воспроизведение**, **Пауза**, и **остановить** функций недостаточно для поддержки элементов управления транспорта. Часто **воспроизведение** и **Пауза** команды реализуются с помощью одной кнопки, которая его внешний вид, указывает на видео в настоящее время воспроизведения или паузы. Кроме того кнопки не следует включать даже если видео еще не загружены.

Эти требования подразумевают, что видеопроигрыватель необходимо сделать доступными текущее состояние, указывающее, при его воспроизведения или приостановлено, или если он еще не готов для воспроизведения видео. (Трех платформ также поддерживают свойства, которые определяют, если видео могут быть приостановлены, также можно переместить в новую позицию, но эти свойства применяются для потоковой передачи видео, а не видеофайлы, поэтому они не поддерживаются в `VideoPlayer` описано здесь.)

**VideoPlayerDemos** проект включает в себя `VideoStatus` перечисления с тремя элементами:

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

`VideoPlayer` Класса определяет только для реального привязываемых свойство, именуемое `Status` типа `VideoStatus`. Это свойство определено как доступное только для чтения, так как он должен быть задан только модуль подготовки отчетов платформы:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

Как правило, только для чтения свойство, используемое бы закрытый `set` доступа для `Status` свойство, чтобы его можно задать в классе. Для `View` производных продуктов, поддерживаемых модулей подготовки отчетов, однако свойство должно иметь значение от вне класса, но только модулем подготовки платформы.

По этой причине определяется другое свойство с именем `IVideoPlayerController.Status`. Это явная реализация интерфейса и стало возможным, `IVideoPlayerController` интерфейс, который `VideoPlayer` реализует класс:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Это аналогично тому, как [ `WebView` ](xref:Xamarin.Forms.WebView) управления использует [ `IWebViewController` ](xref:Xamarin.Forms.IWebViewController) интерфейс для реализации `CanGoBack` и `CanGoForward` свойства. (См. в статье исходный код [ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) и его модулей подготовки отчетов, Дополнительные сведения.)

Это делает возможным для класса внешним по отношению к `VideoPlayer` присвоить `Status` свойство, ссылаясь на `IVideoPlayerController` интерфейс. (Скоро можно будет код.) Свойство может быть задано от других классов, а также, но это вряд ли будут задаваться случайно. Самое главное `Status` невозможно задать свойство через привязку данных.

Для модулей подготовки отчетов в этого `Status` свойство обновлено, `VideoPlayer` класс определяет `UpdateStatus` событие, которое активируется каждую десятую долю секунды:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>Параметры состояния iOS

IOS `VideoPlayerRenderer` задает обработчик для `UpdateStatus` событий (и отсоединяет этот обработчик при базовой `VideoPlayer` отсутствует элемент) и использует обработчик, чтобы задать `Status` свойства:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Два свойства класса `AVPlayer` должен осуществляться: [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/) свойство типа `AVPlayerStatus` и [ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/) свойство типа `AVPlayerTimeControlStatus`. Обратите внимание, что `Element` свойство (который является `VideoPlayer`) должен быть приведен к `IVideoPlayerController` присвоить `Status` свойство.

### <a name="the-android-status-setting"></a>Параметр состояния Android

[ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) Свойство Android `VideoView` является логическое значение, указывающее, только если видео воспроизведения или паузы. Для определения `VideoView` не могут ни play ни приостанавливает видео еще `Prepared` событие `VideoView` должны быть обработаны. Эти два обработчика задаются в `OnElementChanged` метод и отсоединенные во время `Dispose` переопределить:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`UpdateStatus` Обработчик использует `isPrepared` поля (в `Prepared` обработчик) и `IsPlaying` задаваемое свойство `Status` свойство:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>Параметр состояния универсальной платформы Windows

UWP `VideoPlayerRenderer` использует `UpdateStatus` событий, но он не нужны для параметра `Status` свойство. `MediaElement` Определяет [ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged) событие, которое позволяет модуль подготовки отчетов для получения уведомлений при [ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState) свойство изменилось. Свойство отсоединяется в `Dispose` переопределить:

```csharp
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
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`CurrentState` Свойство имеет тип [ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)и легко схем в `VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Воспроизведение, Пауза и остановка кнопок

С использованием символов Юникода для символьных **воспроизведения**, **Пауза**, и **остановить** образов является проблематичным. [Различных технических](https://unicode-table.com/en/blocks/miscellaneous-technical/) раздел стандарта Юникод определяет три специальных символов, казалось бы, подходящую для этой цели. Эти особые значения приведены ниже.

- 0x23F5 (черный средних вправо треугольник) или &#x23F5; для **воспроизведения**
- 0x23F8 (двойная вертикальная черта) или &#x23F8; для **приостановки**
- 0x23F9 (черный квадрат) или &#x23F9; для **остановить**

Независимо от того как эти символы отображаются в браузере (и разные браузеры обрабатывают их по-разному), не будут отображаться постоянно на платформах, поддерживаемых Xamarin.Forms. Для устройств iOS и UWP **Пауза** и **остановить** символы имеют графический интерфейс, с синим фоном 3D и белый передний план. Это не так в Android, где символ является просто синий. Тем не менее codepoint 0x23F5 для **воспроизведение** нет что одинаково на UWP и она даже не поддерживается в iOS и Android.

По этой причине 0x23F5 кодовая точка не может использоваться для **воспроизведение**. Является хорошей заменой:

- 0x25B6 (черный треугольник вправо) или &#x25B6; для **воспроизведения**

Это поддерживается по всем трем платформам, за исключением того, что это обычный черный треугольник, который не похож объемный внешний вид **Пауза** и **остановить**. Один из вариантов, выполните 0x25B6 кодовых точек с использованием кода варианта.

- 0x25B6 следуют 0xFE0F (variant 16) или &#x25B6; &#xFE0F; для **воспроизведения**

Это используемые в разметке, показано ниже. В iOS, он предоставляет **воспроизведение** символов 3D внешний вид **Пауза** и **остановить** кнопки, но вариант не работает на Android и UWP.

**Пользовательский транспорт** странице наборы **AreTransportControlsEnabled** свойства **false** и включает в себя `ActivityIndicator` отображаемое время загрузки видео и два кнопки. `DataTrigger` объекты используются для включения и отключения `ActivityIndicator` и кнопок и для переключения между первой кнопки **воспроизведение** и **Пауза**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Подробно в этой статье описаны триггеры данных [триггеры данных](~/xamarin-forms/app-fundamentals/triggers.md#data).

Файл кода содержит обработчики для кнопки `Clicked` события:

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Так как `AutoPlay` присваивается `false` в **CustomTransport.xaml** файл, вам потребуется нажать **воспроизведение** кнопку она становится доступной для начала видео. Кнопки определены таким образом, чтобы символы Юникода, описанных выше сопровождаются эквиваленты текста. Кнопки имеют согласованного внешнего вида на каждой платформе, при воспроизведении видео:

[![Воспроизведение пользовательский транспорт](custom-transport-images/customtransportplaying-small.png "воспроизведение пользовательский транспорт")](custom-transport-images/customtransportplaying-large.png#lightbox "воспроизведение пользовательский транспорт")

Но на Android и UWP **воспроизведение** кнопка имеет существенные отличия при приостановке видео:

[![Пользовательский транспорт приостановлена](custom-transport-images/customtransportpaused-small.png "приостановлена пользовательский транспорт")](custom-transport-images/customtransportpaused-large.png#lightbox "пользовательский транспорт приостановлена")

В рабочем приложении вам будет удобнее использовать свои собственные образы растрового изображения для кнопок для достижения визуальное единообразие.


## <a name="related-links"></a>Связанные ссылки

- [Видеодемонстрации Player (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
