---
title: Загрузка видеоресурсов приложения
description: В этой статье объясняется, как загрузить видео, хранятся в виде ресурсов приложения в приложение проигрывателя видео, с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171330"
---
# <a name="loading-application-resource-videos"></a>Загрузка видеоресурсов приложения

Пользовательские модули подготовки отчетов для `VideoPlayer` представления способны воспроизведения видеофайлов, которые внедрены в качестве ресурсов приложения проекты для отдельных платформ. Однако текущая версия `VideoPlayer` нет доступа к ресурсам, внедренных в библиотеку .NET Standard.

Чтобы загрузить эти ресурсы, создайте экземпляр `ResourceVideoSource` , задав `Path` свойство имени файла (или в папку и имя файла) ресурса. Кроме того, можно вызвать статический `VideoSource.FromResource` метод для ссылки на ресурс. Затем задайте `ResourceVideoSource` объект `Source` свойство `VideoPlayer`.

## <a name="storing-the-video-files"></a>Хранение файлов видео

Хранение видеофайла в проект платформы отличается для каждой платформы.

### <a name="ios-video-resources"></a>видеоресурсы iOS

В проект iOS, можно хранить видео в **ресурсы** папку или вложенную папку **ресурсы** папки. Видеофайл должен иметь `Build Action` из `BundleResource`. Задайте `Path` свойство `ResourceVideoSource` к имени файла, например, **MyFile.mp4** для файла в **ресурсы** папки, или **MyFolder/MyFile.mp4**, где **MyFolder** вложена **ресурсы**.

В **VideoPlayerDemos** решения, **VideoPlayerDemos.iOS** проект содержит вложенную папку **ресурсы** с именем **видео** содержащий файл с именем **iOSApiVideo.mp4**. Это короткое видео, которое показывает, как использовать веб-сайт Xamarin для поиска документации по iOS `AVPlayerViewController` класса.

### <a name="android-video-resources"></a>Ресурсы Android видео

В проекте Android видео должны храниться во вложенной **ресурсы** с именем **необработанные**. **Необработанные** папка не может содержать вложенные папки. Присвойте файлу видео `Build Action` из `AndroidResource`. Задайте `Path` свойство `ResourceVideoSource` к имени файла, например, **MyFile.mp4**.

**VideoPlayerDemos.Android** проект содержит вложенную папку **ресурсы** с именем **необработанные**, который содержит файл с именем **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Видеоресурсы универсальной платформы Windows

В проекте универсальной платформы Windows можно хранить видео в любой папке в проекте. Присвойте файлу `Build Action` из `Content`. Задайте `Path` свойство `ResourceVideoSource` к папке и имя файла, например, **MyFolder/MyVideo.mp4**.

**VideoPlayerDemos.UWP** проект содержит папку с именем **видео** с файлом **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Загрузка файлов видео

Каждый из классов отрисовки платформы содержит код в его `SetSource` метод загрузки видеофайлов, хранятся как ресурсы.

### <a name="ios-resource-loading"></a>Загрузка ресурсов iOS

Версии iOS `VideoPlayerRenderer` использует `GetUrlForResource` метод `NSBundle` для загрузки ресурса. Полный путь необходимо разделить на имя файла, расширение и каталог. Код использует `Path` классов в .NET `System.IO` пространство имен для разделения пути к файлу на эти компоненты:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Загрузка ресурсов Android

Android `VideoPlayerRenderer` использует имя файла и пакетов для создания `Uri` объекта. Имя пакета, — это имя приложения, в этом случае **VideoPlayerDemos.Android**, можно получить из статического `Context.PackageName` свойство. Полученный `Uri` объект затем передается `SetVideoURI` метод `VideoView`:

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Загрузка ресурсов универсальной платформы Windows

UWP `VideoPlayerRenderer` создает `Uri` объекта для пути и заменяет его на `Source` свойство `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>Воспроизведение файла ресурсов

**Воспроизведения видео ресурсов** странице в **VideoPlayerDemos** решения использует `OnPlatform` класс для указания файла видео для каждой платформы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Если ресурс iOS хранится в **ресурсы** папки, и если ресурс универсальной платформы Windows хранится в корневой папке проекта, можно использовать такое же имя файла для каждой платформы. Если это так, то это имя можно задать непосредственно в `Source` свойство `VideoPlayer`.

Вот этой странице под управлением:

[![Воспроизвести видео ресурсов](loading-resources-images/playvideoresource-small.png "воспроизведения видео ресурсов")](loading-resources-images/playvideoresource-large.png#lightbox "воспроизведения видео ресурсов")

Теперь вы знаете как [загрузка видео из Web URI](web-videos.md) и воспроизведение внедренные ресурсы. Кроме того, вы можете [загрузка видео из устройства видеотека](accessing-library.md).


## <a name="related-links"></a>Связанные ссылки

- [Видеодемонстрации Player (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
