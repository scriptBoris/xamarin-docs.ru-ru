---
title: Выбрав фотографии из библиотеки рисунков
description: В этой статье объясняется, как использовать класс Xamarin.Forms DependencyService для применения фотографии из библиотеки рисунков телефона.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 00308a6c7883d4ac6ce41592d4a0e18f9fb28d52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113317"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Выбрав фотографии из библиотеки рисунков

В этой статье описывается создание приложения, который позволяет пользователю выбрать снимок из библиотеки рисунков телефона. Поскольку Xamarin.Forms не включает эту функцию, она необходима для использования [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) для доступа к собственным API-интерфейсам на каждой платформе.  В этой статье будут рассмотрены следующие действия по использованию `DependencyService` для выполнения этой задачи:

- **[Создание интерфейса](#Creating_the_Interface)**  &ndash; понять, как интерфейс создается в общем коде.
- **[iOS реализация](#iOS_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для iOS.
- **[Android реализации](#Android_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для Android.
- **[Универсальная реализация платформы Windows](#UWP_Implementation)**  &ndash; Узнайте, как реализовать интерфейс в машинном коде для универсальной платформы Windows (UWP).
- **[Реализация в общем коде](#Implementing_in_Shared_Code)**  &ndash; сведения об использовании `DependencyService` выполнить вызов собственной реализации из общего кода.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Создание интерфейса

Во-первых необходимо создаете интерфейс в общем коде, который выражает нужной функции. В случае с приложением photo комплектации необходим только один метод. Это определяется в [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) интерфейса в библиотеке .NET Standard в примере кода:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync` Метод определен как асинхронный метод должен возвращать быстро, но он не может вернуть `Stream` объект для выбранной фотографии, пока пользователь просматривать библиотеки рисунков и выбора одного.

Этот интерфейс реализуется на всех платформах, с помощью кода для конкретных платформ.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Реализация iOS

Реализация iOS `IPicturePicker` интерфейс использует [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) как описано в разделе [ **выберите фотографии из коллекции** ](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) рецепт и [пример кода](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

Реализация операций ввода-вывода содержится в [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) класс в проекте iOS примеров кода. Чтобы сделать этот класс видимым для `DependencyService` manager, необходимо определить класс с [`assembly`] атрибут типа `Dependency`, и класс должен быть открытым и явным образом реализовывать `IPicturePicker` интерфейса:

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

`GetImageStreamAsync` Метод создает `UIImagePickerController` и инициализирует его, чтобы выбрать образы из библиотеке фотографий. Требуются два обработчика событий: один для когда пользователь выбирает фотографии, а другой — для когда пользователь отменяет отображение библиотеке фотографий. `PresentModalViewController` Библиотеки фотографий пользователя.

На этом этапе `GetImageStreamAsync` метод должен возвращать `Task<Stream>` объект, вызывающий его код. Эта задача выполняется только в том случае, когда пользователь закончил взаимодействия с библиотекой фотографий и будет вызван один из обработчиков событий. Для таких случаях [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) класс важно. Этот класс предоставляет `Task` объекта соответствующего универсального типа для возврата из `GetImageStreamAsync` метод и класс можно позже подан сигнал, когда задача завершена.

`FinishedPickingMedia` Обработчик событий вызывается при выборе изображения. Тем не менее, предоставляет обработчик `UIImage` объекта и `Task` должен возвращать .NET `Stream` объекта. Это делается в два этапа: `UIImage` сначала преобразуется объект JPEG-файл в памяти, хранящиеся в `NSData` объекта, а затем `NSData` объект преобразуется к типу .NET `Stream` объекта. Вызов `SetResult` метод `TaskCompletionSource` объект выполняет задачу, предоставляя `Stream` объекта:

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

Приложения iOS требуется разрешение пользователя для доступа к библиотеке фотографий телефона. Добавьте следующий код в `dict` раздел файла Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Реализация Android

Android реализация использует метод, описанный в [ **выберите образ** ](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) рецепт и [пример кода](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Тем не менее, — метод, который вызывается, когда пользователь выбрал образ из библиотеки рисунков `OnActivityResult` переопределить в классе, который является производным от `Activity`. По этой причине нормали [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) класс в проекте Android были дополнены поля, свойства и переопределение `OnActivityResult` метод:

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

`OnActivityResult`Переопределение соответствует файлу выбранного изображения с Android `Uri` объект, но это может быть преобразован в .NET `Stream` путем вызова метода `OpenInputStream` метод `ContentResolver` объекта, который был получен из действия `ContentResolver` свойство.

Как и в реализации iOS, Android реализация использует `TaskCompletionSource` сообщить, когда задача была завершена. Это `TaskCompletionSource` объект определяется как открытое свойство в `MainActivity` класса. Это позволяет ссылаться на свойство [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) класс в проект Android. Это класс с `GetImageStreamAsync` метод:

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Этот метод получает `MainActivity` класс для нескольких целей: для `Instance` свойство, для `PickImageId` поле для `TaskCompletionSource` свойство и для вызова `StartActivityForResult`. Этот метод определяется `FormsAppCompatActivity` класс, который является базовым классом для `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Реализация универсальной платформы Windows

В отличие от iOS и Android реализаций, не требуется реализация средство выбора фотографии для универсальной платформы Windows `TaskCompletionSource` класса. [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) Класс использует [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) класса, чтобы получить доступ к библиотеке фотографий. Так как `PickSingleFileAsync` метод `FileOpenPicker` сам является асинхронным, `GetImageStreamAsync` можно просто использовать метод `await` с помощью этого метода (и других асинхронных методов) и возвращают `Stream` объекта:


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Реализация в общем коде

Теперь, когда интерфейс реализован для каждой платформы, приложение в библиотеке .NET Standard можно использовать его.

[ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) Класс создает `Button` выбрать фотографию:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

`Clicked` Обработчик использует `DependencyService` класса для вызова `GetImageStreamAsync`. Это приводит к вызову платформы проекта. Если метод возвращает `Stream` объекта, то обработчик создает `Image` элемент для этого рисунок с `TapGestureRecognizer`и заменяет `StackLayout` на странице, `Image`:

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

При касании `Image` элемент возвращает страницы в нормальное состояние.


## <a name="related-links"></a>Связанные ссылки

- [Выберите фотографии из коллекции (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Выберите образ (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (пример)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
