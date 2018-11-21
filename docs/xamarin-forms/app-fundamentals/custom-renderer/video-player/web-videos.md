---
title: Воспроизведение видео через Интернет
description: В этой статье объясняется, как для воспроизведения веб-видео в приложения видеопроигрывателя, с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 7f40d0d11fc932121b4ff7789969bbb1e354024c
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172214"
---
# <a name="playing-a-web-video"></a>Воспроизведение видео через Интернет

`VideoPlayer` Класс определяет `Source` свойство, используемое для указания источника файла видео, а также `AutoPlay` свойство. `AutoPlay` имеет значение по умолчанию `true`, что означает, что видео следует начать воспроизведение автоматически после `Source` было задано:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

`Source` Свойство имеет тип `VideoSource`, который создан по образцу Xamarin.Forms [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) абстрактный класс и его три производных [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource), [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), и [ `StreamImageSource` ](xref:Xamarin.Forms.StreamImageSource). Нет поток доступен для `VideoPlayer` тем не менее, так как iOS и Android не поддерживает воспроизведение видео из потока.

## <a name="video-sources"></a>Видеоисточников

Абстрактный `VideoSource` класса состоит только из три статических метода, которые создают трех классов, производных от `VideoSource`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

`UriVideoSource` Класс используется для указания загружаемый файл видео с URI. Он определяет единственное свойство типа `string`:

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

Обработка объектов типа `UriVideoSource` описан ниже.

`ResourceVideoSource` Класс используется для доступа к видеофайлов, которые хранятся в виде внедренных ресурсов в приложении платформы также задан с `string` свойство:

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

Обработка объектов типа `ResourceVideoSource` описан в статье [загрузка Видеоресурсов приложения](loading-resources.md). `VideoPlayer` Класс не содержит средств для загрузки файла видео хранится в виде ресурса в библиотеке .NET Standard.

`FileVideoSource` Класс используется для доступа к видеофайлов из видеотеке устройства. Одно свойство также является типом `string`:

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

Обработка объектов типа `FileVideoSource` описан в статье [доступа к библиотеке видео устройства](accessing-library.md).

`VideoSource` Класс включает `TypeConverter` атрибут, который ссылается на `VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

Вызывается этот преобразователь типов при `Source` свойство имеет значение строки в XAML. Вот `VideoSourceConverter` класса:

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

`ConvertFromInvariantString` Метод пытается преобразовать строку в `Uri` объекта. Если успеха и схема является `file:`, то метод возвращает `UriVideoSource`. В противном случае возвращается `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Устанавливая источник видео

Все другие логику, включающую видеоисточников реализуется в модулях подготовки отчетов отдельных платформ. В следующих разделах показано, как платформа визуализаторов воспроизводить видео при `Source` свойству `UriVideoSource` объекта.

### <a name="the-ios-video-source"></a>Источник видео iOS

Два раздела `VideoPlayerRenderer` вовлечены в устанавливая источник видео видеопроигрывателя. Когда Xamarin.Forms сначала создает объект типа `VideoPlayer`, `OnElementChanged` метод вызывается с `NewElement` , значение свойства объекта аргументов `VideoPlayer`. `OnElementChanged` Вызовы методов `SetSource`:

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
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

Ниже на, когда `Source` свойство меняется, `OnElementPropertyChanged` метод вызывается с `PropertyName` свойство «Источник», и `SetSource` вызывается снова.

Для воспроизведения видеофайла в iOS, тип объекта [ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/) для инкапсуляции видеофайл, сначала создается и используется для создания [ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/), который затем передается на `AVPlayer`объекта. Вот как `SetSource` метод обрабатывает `Source` свойства, если он имеет тип `UriVideoSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

`AutoPlay` Свойство имеет не аналоговый в классах видео операций ввода-вывода, поэтому свойство проверяется в конце `SetSource` метод для вызова `Play` метод `AVPlayer` объекта.

В некоторых случаях видео Продолжение воспроизведения после страницы с `VideoPlayer` переход назад к домашней странице. Для остановки видео, `ReplaceCurrentItemWithPlayerItem` также задается в `Dispose` переопределить:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Android источник видео

Android `VideoPlayerRenderer` необходимо указать источник видео проигрывателя при `VideoPlayer` будет сначала создана и более поздних версий при `Source` изменения свойств:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

`SetSource` Метод обрабатывает объекты типа `UriVideoSource` путем вызова `SetVideoUri` на `VideoView` с Android `Uri` объект, созданный из строки URI. `Uri` Класс здесь полное доменное чтобы отличить его от .NET `Uri` класса:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Android `VideoView` у которого нет соответствующего `AutoPlay` свойство, поэтому `Start` метод вызывается в том случае, если новое видео задана.

Есть разница между особенности iOS и Android модулей подготовки отчетов, если `Source` свойство `VideoPlayer` присваивается `null`, или, если `Uri` свойство `UriVideoSource` присваивается `null` или содержит пустую строку. Если видеопроигрыватель iOS в настоящее время воспроизведения видео, и `Source` присваивается `null` (или строка имеет `null` или пустое), `ReplaceCurrentItemWithPlayerItem` вызывается с `null` значение. Текущего видеоролика заменяется и прекращает воспроизведение.

Android не поддерживает аналогичные средства. Если `Source` свойству `null`, `SetSource` метод просто игнорирует его и текущего видеоролика продолжает воспроизводиться.

### <a name="the-uwp-video-source"></a>Источник видео универсальной платформы Windows

UWP `MediaElement` определяет `AutoPlay` свойство, которое обрабатывается в модуль подготовки отчетов, как и другие свойства:

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
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

`SetSource` Дескрипторы свойств `UriVideoSource` объекта, задав `Source` свойство `MediaElement` к типу .NET `Uri` значение, или `null` Если `Source` свойство `VideoPlayer` присваивается `null`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>Установка источника URL-адрес

С реализацией этих свойств в трех модулей подготовки отчетов существует возможность воспроизведения видео из URL-адрес источника. **Воспроизведение видео в Интернете** странице в [ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md) программы определяется в следующем XAML-файле:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter` Класс преобразует строку в `UriVideoSource`. При переходе на **воспроизведения видео Web** странице видео начинает загрузку и начнется воспроизведение достаточное количество данных были загружены, а в буфер. Видео составляет около 10 минут, длина:

[![Воспроизведение видео в Интернете](web-videos-images/playwebvideo-small.png "воспроизведение видео в Интернете")](web-videos-images/playwebvideo-large.png#lightbox "воспроизведение видео в Интернете")

На каждой из платформ элементы управления транспортировкой Исчезание если они не используются, но могут быть восстановлены для просмотра, коснувшись видео.

Видео может не запускаться автоматически, задав `AutoPlay` свойства `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Вам потребуется нажать **воспроизведение** кнопку, чтобы запустить видео.

Аналогичным образом, может отключить отображение элементов управления транспорта, задав `AreTransportControlsEnabled` свойства `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Если заданы оба свойства `false`видео не начинается воспроизведение и будет невозможно запустить ее! Необходимо вызвать `Play` из файла кода или для создания собственных элементов управления транспорта, как описано в статье [элементы управления транспортировкой видео пользовательские реализации](custom-transport.md).

**App.xaml** файл содержит ресурсы для двух дополнительных видео:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Чтобы сослаться на эти другие фильмов, вы можете заменить явного URL-адреса в **PlayWebVideo.xaml** файл с `StaticResource` расширения разметки, в противном случае `VideoSourceConverter` не требуется для создания `UriVideoSource` объекта:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Кроме того, можно задать `Source` свойство из видеофайла в `ListView`, как описано в следующей статье [привязка видеоисточников к проигрывателю](source-bindings.md).





## <a name="related-links"></a>Связанные ссылки

- [Видеодемонстрации Player (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
