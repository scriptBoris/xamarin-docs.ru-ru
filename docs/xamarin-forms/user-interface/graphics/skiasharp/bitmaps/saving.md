---
title: Сохранение растровых изображений SkiaSharp в файлы
description: Изучите различные форматы файлов, поддерживаемых SkiaSharp для сохранение растровых изображений в библиотеке фотографий пользователя.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: charlespetzold
ms.author: chape
ms.date: 07/10/2018
ms.openlocfilehash: 5ef18728bbf417750575bad88b3498f66fa585c4
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39276019"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Сохранение растровых изображений SkiaSharp в файлы

После приложения SkiaSharp создал или изменил Битовая карта, приложение может потребоваться сохранить растровое изображение к библиотеке фотографий пользователя:

![Сохранение растровых изображений](saving-images/SavingSample.png "сохранение растровых изображений")

Эта задача включает в себя два этапа:

- Преобразование SkiaSharp точечный рисунок в данных в виде определенного файла, например, JPEG или PNG.
- Сохранение результата в библиотеке фотографий, с помощью кода для конкретных платформ.

## <a name="file-formats-and-codecs"></a>Форматы и кодеки

Большинство современных популярных точечный рисунок файл форматы используют сжатие для уменьшения дискового пространства. Двумя основными категориями методы сжатия, называются _с потерями_ и _без потерь_. Эти термины указывают ли алгоритм сжатия приводит к потере данных.

Наиболее популярные с потерями формат была разработана неподвижных изображений и вызывается JPEG. Алгоритм сжатия JPEG анализирует образ с помощью математических утилиту _преобразования дискретных косинус_и пытается удалить данные, которые не важен для сохраняя качество изображения. Степень сжатия можно управлять с помощью параметра, обычно называемый _качества_. Более высокие значения качества привести файлы большего размера.

Напротив алгоритм сжатия без потерь анализирует изображение для повторения и шаблоны пикселей, которые могут быть закодированы в виде, уменьшается объем данных, но приводит к потере информации. Исходные данные точечного рисунка могут восстанавливаться из сжатого файла. Основной без потерь сжатый формат файла используется сегодня — Portable Network Graphics (PNG).

Как правило JPEG используется для фотографии, а PNG используются для образов, созданных вручную или точки. Любой алгоритм сжатие без потерь, уменьшает размер некоторых файлов обязательно необходимо увеличить размер других пользователей. К счастью это увеличение размера обычно выполняется только для данных, которое содержит много информации о случайных (или беспорядочный).

Сжатия алгоритмы будут настолько сложны, чтобы гарантировать, что два условия, описывающие процесс сжатия и распаковки:

- _декодирование_ &mdash; читать формат файла точечного рисунка и распаковывает их
- _кодирование_ &mdash; сжать точечный рисунок и записи в формате файла точечного рисунка

[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/) Класс содержит несколько методов с именем `Decode` , которые создают `SKBitmap` из сжатого источника. Требуется лишь указать имя файла, потока или массива байтов. Декодер можно определить формат файла и передать ее для правильного декодирования Внутренняя функция.

Кроме того [ `SKCodec` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCodec/) класс имеет два метода с именем `Create` , можно создать `SKCodec` от сжатые исходные и позволяют приложению получить более важную роль в процессе декодирования. ( `SKCodec` Класс показан в этой статье [ **анимации точечных рисунков SkiaSharp** ](animating.md#gif-animation) в связи с декодирование анимированный GIF-файл.)

Необходим при кодировании Битовая карта, Дополнительные сведения: кодировщику необходимо знать формат определенного файла, приложению необходимо использовать (JPEG или PNG или иное). При необходимости с потерями формат кодирования, также необходимо знать требуемый уровень качества. 

`SKBitmap` Класс определяет одно [ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Encode/p/SkiaSharp.SKWStream/SkiaSharp.SKEncodedImageFormat/System.Int32/) метод со следующим синтаксисом:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Этот метод описан более подробно чуть ниже. Записываемый поток для записи закодированного растрового изображения. («W» в `SKWStream` расшифровывается как «для записи».) Второй и третий аргументы задают формат файла и (для применяемую в форматах) желаемое качество, от 0 до 100.

Кроме того [ `SKImage` ](https://developer.xamarin.com/api/type/SkiaSharp.SKImage/) и [ `SKPixmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPixmap/) классы также определяют `Encode` методы, являются более гибкими и может потребоваться. Вы можете легко создавать `SKImage` объекта из `SKBitmap` объекта с помощью статического [ `SKImage.FromBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.FromBitmap/p/SkiaSharp.SKBitmap/) метод. Вы можете получить `SKPixmap` объекта из `SKBitmp` с помощью [ `PeekPixels` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.PeekPixels()/) метод.

Один из [ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.Encode()/) методы, определенные `SKImage` не имеет параметров и автоматически сохраняет в формате PNG. Этот метод без параметров очень прост в использовании.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Специфические для платформы код для сохранения файлов растрового изображения

При кодировании `SKBitmap` объекта в файл определенного формата, обычно можно оставить объект потока какого-либо рода, или массив данных. Некоторые из `Encode` методы (включая без параметров, определяемых `SKImage`) возвращают [ `SKData` ](https://developer.xamarin.com/api/type/SkiaSharp.SKData/) объект, который может быть преобразован в массив байтов с помощью [ `ToArray` ](https://developer.xamarin.com/api/member/SkiaSharp.SKData.ToArray()/) метод. Затем эти данные необходимо сохранить в файл. 

Сохранение в файл в локальном хранилище приложения достаточно прост, так как можно использовать стандартную `System.IO` классы и методы для выполнения этой задачи. Этот подход продемонстрирован в этой статье [ **анимации точечных рисунков SkiaSharp** ](animating.md#bitmap-animation) в связи с анимации ряд точечных рисунков из множества Мандельброта.

Если вы хотите файл совместно с другими приложениями, его необходимо сохранить в библиотеке фотографий пользователя. Эта задача требует кода для конкретных платформ и использование Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

**SkiaSharpFormsDemo** в проекте [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) приложение определяет `IPhotoLibrary` интерфейс, используемый с `DependencyService` класса. Это определяет синтаксис `SavePhotoAsync` метод:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Этот интерфейс также определяет `PickPhotoAsync` метод, который используется для открытия выбора файлов от платформы устройства библиотеке фотографий.

Для `SavePhotoAsync`, первый аргумент представляет собой массив байтов, содержащий точечный рисунок уже закодирован в формат, определенный файл, например, JPEG или PNG. Вполне возможно, что приложение может потребоваться изолировать все битовые карты, которые оно создает в определенную папку, которая указана в следующий параметр, за которым следует имя файла. Метод возвращает логическое значение, указывающее, успешно или нет.

Вот как `SavePhotoAsync` реализуется в трех платформ:

### <a name="the-ios-implementation"></a>Реализация iOS

Реализация iOS `SavePhotoAsync` использует [ `SaveToPhotosAlbum` ](https://developer.xamarin.com/api/member/UIKit.UIImage.SaveToPhotosAlbum/) метод `UIImage`:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

К сожалению нет способа указать имя файла или папки для образа. 

**Info.plist** файл в проекте iOS требуется ключ, указывающий, что он добавляет образов к библиотеке фотографий:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Осторожно! Ключ разрешения для просто доступа к библиотеке фотографий очень похожи, но не совпадают:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Реализация Android

Android реализация `SavePhotoAsync` сначала проверяет `folder` аргумент является `null` или является пустой строкой. Если Да, растровое изображение сохраняется в корневом каталоге библиотеке фотографий. В противном случае получается папка, и если он не существует, он будет создан:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

Вызов `MediaScannerConnection.ScanFile` не является обязательным, но если вы тестируете приложение немедленно проверяя библиотеке фотографий, оно помогает гораздо, обновив представление коллекции библиотеки.

**AndroidManifest.xml** файла требуется следующий тег разрешение:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>Реализация универсальной платформы Windows

Реализация UWP `SavePhotoAsync` является очень сходна со структурой в Android реализацию:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

**Возможности** раздел **Package.appxmanifest** файла требуется **библиотека изображений**.

## <a name="exploring-the-image-formats"></a>Изучение форматов изображения

Вот [ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Encode/p/SkiaSharp.SKWStream/SkiaSharp.SKEncodedImageFormat/System.Int32/) метод `SKImage` еще раз:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](https://developer.xamarin.com/api/type/SkiaSharp.SKEncodedImageFormat/) Это перечисление с члены, ссылающиеся на одиннадцати растровых форматов, некоторые из них довольно запутанным:

- `Astc` &mdash; Сжатие текстур адаптивной масштабируемой
- `Bmp` &mdash; Точечный рисунок Windows
- `Dng` &mdash; Adobe Digital Negative
- `Gif` &mdash; Graphics Interchange Format
- `Ico` &mdash; Изображения значков Windows
- `Jpeg` &mdash; Стиль объединения
- `Ktx` &mdash; Формат текстуры Khronos для OpenGL
- `Pkm` &mdash; Pokémon сохранить файл
- `Png` &mdash; Формат PNG
- `Wbmp` &mdash; Формат растрового изображения протокол беспроводных приложений (1 бит на пиксель)
- `Webp` &mdash; Формат Google WebP

Как вы вскоре увидите, только три из этих форматов файлов (`Jpeg`, `Png`, и `Webp`) фактически поддерживаются SkiaSharp.

Чтобы сохранить `SKBitmap` объект с именем `bitmap` к библиотеке фотографий пользователя, необходимо также является членом `SKEncodedImageFormat` перечисление с именем `imageFormat` и (для применяемую в форматах) является целым числом `quality` переменной. Можно использовать следующий код для сохранения его в файл с именем `filename` в `folder` папку:

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

`SKManagedWStream` Класс является производным от `SKWStream` (что означает «доступный для записи поток»). `Encode` Метод записывает файл закодированного растрового изображения в этом потоке. Комментарии в этом коде ссылаются на проверку, что может потребоваться выполнить. 

**Сохранить форматы файлов** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) приложение использует аналогичный код, чтобы можно было экспериментировать с сохранения точечных рисунков в различных форматах.

Файл XAML содержит `SKCanvasView` , отображает точечный рисунок, а остальная часть страницы содержит все, что приложение должно вызвать `Encode` метод `SKBitmap`. Он имеет `Picker` членами `SKEncodedImageFormat` перечисления, `Slider` качества аргумента для форматов с потерями точечного рисунка, два `Entry` представления, имя файла и имя папки и `Button` для сохранения файла.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save" 
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом загружает ресурса точечного рисунка и использует `SKCanvasView` для его отображения. Битовая карта, никогда не изменения. `SelectedIndexChanged` Обработчик для `Picker` изменяет имя файла с расширением, которое совпадает со значением члена перечисления:

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

`Clicked` Обработчик для `Button` все реальной работы. Он получает два аргумента для `Encode` из `Picker` и `Slider`, а затем использует код, показанный ранее, чтобы создать `SKManagedWStream` для `Encode` метод. Два `Entry` представления вставить имена файлов и папок для `SavePhotoAsync` метод.

Большая часть этого метода, предназначенного для обработки проблем или ошибок. Если `Encode` создает пустой массив, это означает, что определенный формат, не поддерживается. Если `SavePhotoAsync` возвращает `false`, а затем не был сохранен файл. 

Ниже приведен программу на трех платформ.

[![Сохранить форматы файлов](saving-images/SaveFileFormats.png "сохранять форматы файлов")](saving-images/SaveFileFormats-Large.png#lightbox)

Этом снимке экрана показаны только трех форматов, которые поддерживаются на этих платформах:

- JPEG
- PNG
- WebP

Для других форматов `Encode` метод ничего не записывает в поток и результирующий массив пуст.

Битовая карта, **сохранить форматы файлов** сохранение страниц представляет собой квадрат 600 пикселей. С 4 байта на пиксел это всего 1,440,000 байта в памяти. В следующей таблице показаны размер файла для различных сочетаний формат файла и качества:

|Формат|Качество|Размер|
|------|------:|---:|
| PNG | Н/Д | 492K |
| JPEG | 0 | 2,95 K |
|      | 50 | ПП.22.1 K |
|      | 100 | 206K |
| WebP | 0 | 2,71 K |
|      | 50 | 11,9 K |
|      | 100 | 101K |

Можно поэкспериментировать с различными уровнями качества и проанализировать результаты.

## <a name="saving-finger-paint-art"></a>Сохранение finger-paint art

Один обычно Битовая карта используется в графических программ, где он работает как сущности, называемой _точечного рисунка тени_. Рисование сохраняется на точечного рисунка, который отображается в программе. Битовая карта также может пригодиться для сохранения документа.

[ **Рисование пальцем в SkiaSharp** ](../paths/finger-paint.md) статье было показано, как использовать сенсорный ввод, отслеживания для реализации простых красочное программы. Программа поддерживается только один цвет и ширину только одним росчерком, но удалена весь рисунок в коллекцию `SKPath` объектов.

**Рисование пальцами с Сохранить** странице в [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) образец также сохраняет весь рисунок в коллекцию `SKPath` объектов, но также Выполняет визуализацию рисунок на точечного рисунка, который он может сэкономить к библиотеке фотографий.

Большая часть этой программы похож на исходный **Рисование пальцами** программы. Одним из улучшений является то, что файл XAML теперь создает экземпляры кнопки **Очистить** и **Сохранить**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

Файл с выделенным кодом поддерживает поле типа `SKBitmap` с именем `saveBitmap`. При создании или создать их повторно в этой битовой карты `PaintSurface` обработчик всякий раз, когда размер контактной изменения. Если точечный рисунок нужно создать заново, содержимое существующего растрового изображения копируются в новый таким образом, все сохраняется вне зависимости от того, как поверхность отображения изменяется размер:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

Рисования, выполняемые `PaintSurface` обработчик происходит в самом конце и состоит только из визуализации точечного рисунка.

В более ранних программу аналогично обработку сенсорного ввода. Программа поддерживает две коллекции `inProgressPaths` и `completedPaths`, которые содержат все, что пользователь, нарисованные с момента последнего отображения был очищен. Для каждого события касания `OnTouchEffectAction` вызовов обработчика `UpdateBitmap`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

`UpdateBitmap` Будет обновлен метод `saveBitmap` путем создания нового `SKCanvas`, очищать их и затем отрисовки всех путей в растровое изображение. Все переписыванием недействительный `canvasView` таким образом, чтобы растрового изображения могут отображаться на экране.

Ниже приведены обработчики для обеих кнопок. **Очистить** кнопку Очистить обе коллекции пути, обновления `saveBitmap` (в результате очистки растрового изображения) и делает недействительным `SKCanvasView`:

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

**Сохранить** обработчик кнопки использует упрощенный интерфейс [ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.Encode()/) метода из `SKImage`. Этот метод кодирует в формате PNG. `SKImage` Объект создается на основе `saveBitmap`и `SKData` объект содержит закодированный PNG-файл. 

`ToArray` Метод `SKData` получает массив байтов. Это связано с содержимым, передаваемым `SavePhotoAsync` метода, а также имя предопределенной папки и уникальное имя файла, построенный на основе текущей даты и времени.

Вот программа в действии:

[![Пальца Paint Сохранить](saving-images/FingerPaintSave.png "пальца Paint сохранить")](saving-images/FingerPaintSave-Large.png#lightbox)

Очень похожий прием используется в [ **программа SpinPaint** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/) образца. Это также красочное программы, за исключением того, что пользователь рисует вращающийся диска, который затем воспроизводит макеты на его четырех квадрантов. Цвет рисования изменения пальцем диск находится в состоянии:

[![Запустить Paint](saving-images/SpinPaint.png "запустить рисования")](saving-images/SpinPaint-Large.png#lightbox)

**Сохранить** кнопки `SpinPaint` класс аналогичен **Рисование пальцами** тем, что он сохраняет изображение имя предопределенной папки (**SpainPaint**) и имя файла из Дата и время.

## <a name="related-links"></a>Связанные ссылки

- [API-интерфейсы SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Программа SpinPaint (пример)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/)
