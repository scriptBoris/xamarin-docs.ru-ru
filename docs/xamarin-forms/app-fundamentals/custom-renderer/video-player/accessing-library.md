---
title: Доступ к видеотеке устройства
description: В этой статье объясняется, как получить доступ к видеотеке устройства в приложении видеопроигрывателя с помощью Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 619469e4c4fd3901491c20d6215ec0a25c49f69d
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171187"
---
# <a name="accessing-the-devices-video-library"></a>Доступ к видеотеке устройства

Большинство современных мобильных устройств и настольных компьютеров имеют возможность записи видео, с помощью камеры устройства. Видео, которые создает пользователь затем сохраняются в виде файлов на устройстве. Эти файлы извлекаются из библиотеки образов и выполняемые `VideoPlayer` класса так же, как и любые другие видео.

## <a name="the-photo-picker-dependency-service"></a>Службы хранения фотографий выбора зависимостей

Каждой из платформ включает средство, позволяющее пользователю выбрать фото или видео из библиотеки изображений устройства. Первым шагом в воспроизведение видео из библиотеки изображений устройства Создание зависимости службы, которая вызывает средство выбора изображения на каждой платформе. Службы зависимостей, описанных ниже очень похожа на один определенный в [ **комплектации фотографии из библиотеки рисунков** ](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md) статьи, за исключением того, что видео средство выбора возвращает имя файла, а не `Stream`объекта.

Проект библиотеки .NET Standard определяет интерфейс с именем `IVideoPicker` для зависимостей службы:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Каждой из платформ содержит класс с именем `VideoPicker` , реализующий этот интерфейс.

### <a name="the-ios-video-picker"></a>Средство выбора видео iOS

IOS `VideoPicker` использует iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) для доступа к библиотеке изображений, указав, что он должен быть ограничен видео (называются «movies») в iOS `MediaType` свойство. Обратите внимание, что `VideoPicker` явно реализует `IVideoPicker` интерфейс. Также Обратите внимание, что `Dependency` атрибут, определяющий этот класс как услуга зависимостей. Ниже приведены два требования, которые позволяют Xamarin.Forms найти службу зависимости в проект платформы:

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Android Выбор видео

Android реализация `IVideoPicker` требуется метод обратного вызова, который является частью действий приложения. По этой причине `MainActivity` класс определяет два свойства, поля и метод обратного вызова:

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

`OnCreate` Метод в `MainActivity` сохраняет свой собственный экземпляр в статический `Current` свойство. Это позволяет реализация `IVideoPicker` для получения `MainActivity` экземпляра для запуска **видео, выберите** выбора:

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Дополнения к `MainActivity` объект являются единственным кодом в [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) решение, где обычный код приложения необходимо изменить для поддержки `FormsVideoLibrary` классы.

### <a name="the-uwp-video-picker"></a>Средство выбора видео универсальной платформы Windows

Реализация UWP `IVideoPicker` интерфейс использует UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/). Он начинает поиск файла с библиотекой рисунков и ограничивает типы файлов MP4 и WMV (Windows Media Video):

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Вызов службы зависимостей

**Воспроизведения видео библиотеки** странице [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) программа демонстрирует, как использовать службу зависимостей видео выбора. Файл XAML содержит `VideoPlayer` экземпляра и `Button` с меткой **Показать видеотека**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

Файл кода содержит `Clicked` обработчик для `Button`. Вызов службы зависимостей требуется вызов `DependencyService.Get` для получения реализации `IVideoPicker` интерфейс в проекте платформы. `GetVideoFileAsync` На этом экземпляре затем вызывается метод:

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

`Clicked` Обработчик использует имя этого файла для создания `FileVideoSource` объект и присвойте ему значение `Source` свойство `VideoPlayer`.

Каждый из `VideoPlayerRenderer` классов содержит код в его `SetSource` метод для объектов типа `FileVideoSource`. Это показано ниже:

### <a name="handling-ios-files"></a>Обработка операций ввода-вывода файлов

Версии iOS `VideoPlayerRenderer` процессы `FileVideoSource` объектов с помощью статического `Asset.FromUrl` метод с именем файла. Это создание `AVAsset` объект, представляющий файл в библиотеке изображений устройства:

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Обработка файлов Android

При обработке объектов типа `FileVideoSource`, Android реализация `VideoPlayerRenderer` использует `SetVideoPath` метод `VideoView` для указания файла в библиотеке изображений устройства:

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Обработка файлов универсальной платформы Windows

При обработке объектов типа `FileVideoSource`, реализация UWP `SetSource` метод должен создать `StorageFile` , откройте этот файл для чтения и передайте в объект потока, чтобы `SetSource` метод `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

Для каждой платформы видео начинает воспроизведение почти сразу же после видео источник устанавливается в том случае, поскольку данный файл на устройстве и может не загружаться.



## <a name="related-links"></a>Связанные ссылки

- [Видеодемонстрации Player (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [Выбрав фотографии из библиотеки рисунков](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
