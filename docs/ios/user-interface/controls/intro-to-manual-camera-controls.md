---
title: Ручные элементы управления камерой в Xamarin.iOS
description: В этом документе описывается, как платформа AVFoundation iOS можно с помощью Xamarin.iOS для включения ручные элементы управления камерой. Ручные элементы управления камерой позволяет пользователю фокус элементов управления, Баланс белого и параметры экспозиции.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 84c4b699ba2c046eeb70963f3df71ca9a4760f3b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104184"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Ручные элементы управления камерой в Xamarin.iOS

Элементы управления камерой вручную, предоставляемые `AVFoundation Framework` в iOS 8, разрешить мобильного приложения на полный контроль над камеры на устройстве iOS. Этот детальный уровень элемента управления можно использовать для создания профессиональных камеры на уровне приложений и предоставить исполнителя композиции, их параметров камеры, при этом по-прежнему изображение или видео.

Эти элементы управления также можно использовать при разработке приложений, научных исследований или промышленный результаты менее предназначены для правильности или красота изображения куда предназначены для более выделение некоторые компонента или элемента изображения к выполнению.

## <a name="avfoundation-capture-objects"></a>Объекты отслеживания измененных AVFoundation

Ли принять видео или изображений с помощью камеры на устройстве iOS, процесс, используемый для создания этих образов не изменилась. Это верно для приложений, использующих элементы управления камерой автоматической по умолчанию, или те, которые используют преимущества новых элементов управления камеры вручную:

 [![](intro-to-manual-camera-controls-images/image1.png "Общие сведения об объектах захвата AVFoundation")](intro-to-manual-camera-controls-images/image1.png#lightbox)

Входные данные берутся из `AVCaptureDeviceInput` в `AVCaptureSession` посредством `AVCaptureConnection`. Результат выводится либо как по-прежнему изображение или видео потока. Весь процесс контролируется `AVCaptureDevice`.

## <a name="manual-controls-provided"></a>Вручную элементов управления

С помощью новых интерфейсов API в iOS 8, приложение можно контролировать следующие функции камеры:

-  **Вручную фокус** —, позволяя пользователю напрямую получить контроль над фокус, приложение может обеспечить больший контроль над изображение взято.
-  **Ручной выдержки** – предоставляя вручную контролировать доступность, приложение может предоставлять пользователям большую свободу и разрешить их для достижения стилизованные вид.
-  **Вручную баланс белого** — используемый баланс белого цвета изображения — часто, чтобы сделать его реалистичные. Различные источники света имеют разные цвета температуры и настройки камеры для записи образа является корректируются с учетом этих различий. Опять же позволяя пользователю контроль над баланс белого, пользователей может вносить изменения с не может быть сделано автоматически.


iOS 8 предоставляет расширения и улучшения существующих iOS API-интерфейсы для предоставления этого точного управления изображение процесса записи.

## <a name="bracketed-capture"></a>Записи в квадратных скобках

Заключенный в скобки сбора данных на основе параметров из элементов управления вручную камеры, представленные выше, что позволяет приложению можно записать на момент времени, в ряд различных способов.

Проще говоря, заключенных в скобки захвата является по-прежнему изображения, полученные с параметрами из рисунка, рисунок в пакетном режиме.

## <a name="requirements"></a>Требования

Ниже перечислены необходимые условия для выполнения действия, описанные в этой статье:

-  **Xcode 7 + и iOS 8 или более поздней версии** — Apple Xcode 7 и iOS 8 или более новых интерфейсов API необходимо установить и настроить на компьютере разработчика.
-  **Visual Studio для Mac** — последнюю версию Visual Studio для Mac должны быть установлены и настроены на устройстве пользователя.
-  **iOS 8 устройства** — устройства iOS под управлением последней версии iOS 8. Возможности камеры можно тестировать в симуляторе iOS.


## <a name="general-av-capture-setup"></a>Общие AV записи установки

При записи видео на устройстве iOS, используется Общая Настройка код, который всегда является обязательным. В этом разделе рассматриваются минимальные настройки, необходимые для записи видео с камеры на устройстве iOS и отображения видео в реальном времени в `UIImageView`.

### <a name="output-sample-buffer-delegate"></a>Выходные данные примера буфера делегата

В первую очередь необходимое будет делегат для отслеживания образец выходного буфера и отобразить изображение из буфера `UIImageView` в пользовательском Интерфейсе приложения.

Следующие процедуры будут отслеживаться пример буфера и обновить пользовательский Интерфейс:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

С этой подпрограммы в месте `AppDelegate` можно изменить, чтобы открыть сеанс захвата антивирусной программы для записи видео динамического канала.

### <a name="creating-an-av-capture-session"></a>Создание сеанса записи звуковых и Видеофайлов

Сеанс захвата AV используется для управления запись потокового видео с камеры на устройстве iOS и необходим для получения видео в приложение iOS. Так как пример `ManualCameraControl` пример приложения использует сеанса записи в разных местах, он будет настроен в `AppDelegate` и доступны для всего приложения.

Выполните следующую команду, чтобы вносить изменения в приложения `AppDelegate` и добавить необходимый код:


1. Дважды щелкните `AppDelegate.cs` файл в обозревателе решений, чтобы открыть его для редактирования.
1. Добавьте следующие операторы using в верхней части файла:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Добавьте следующие частные переменные и вычисляемые свойства к `AppDelegate` класса:
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. Переопределить метод завершения и измените его на:
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. Сохраните изменения в файле.


Этот код в месте элементы управления камерой вручную можно легко реализовать для экспериментов и тестирования.

## <a name="manual-focus"></a>Вручную фокус

Позволяя пользователю напрямую воспользоваться элементами управления фокус, приложение может обеспечивать более художественно контроль над изображение взято.

Например, профессиональным фотографом можно смягчить фокус изображения для достижения [эффект Боке](http://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Эффект Боке")](intro-to-manual-camera-controls-images/image2.png#lightbox)

Кроме того, создать [фокус на включение внесенных изменений эффект](http://www.mediacollege.com/video/camera/focus/pull.html), такие как:

[![](intro-to-manual-camera-controls-images/image3.png "Эффект фокус на включение внесенных изменений")](intro-to-manual-camera-controls-images/image3.png#lightbox)

Для специалистов по анализу или модуль записи медицинские применения приложение может потребоваться программно перемещать группы связанных элементов для экспериментов. В любом случае новый интерфейс API позволяет берется конечный пользователь или приложение, чтобы воспользоваться контроль над фокус на момент изображения.

### <a name="how-focus-works"></a>Как работает фокус

Прежде чем обсуждать аспекты управления фокус в приложении IOS 8. Давайте кратко рассмотрим, как фокус работает на устройстве iOS:

[![](intro-to-manual-camera-controls-images/image4.png "Как работает фокус на устройстве iOS")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Свет вводит объектив камеры на устройстве iOS и занимается изображения датчика. Расстояние от группы связанных элементов от датчика определяет, где центральный узел (область, где отображаются лучшими изображения), в связи с датчиком. Чем дальше линз — от датчика, показаться лучшими расстояние объектов и чем ближе рядом объектов показаться лучшими.

На устройстве iOS линз перемещается ближе к или от датчика магниты и пружины. Таким образом точное расположение линз невозможна, поскольку он зависит от устройства к устройству и может зависеть от параметров, например ориентации устройства или возраст устройства, а затем spring.

### <a name="important-focus-terms"></a>Особое внимание нужно уделить условия

При работе с фокусом, существует несколько терминов, которые разработчик должны быть знакомы с:

-  **Глубина поля** — расстояние между объектами ближайшего и крайний с фокусом. 
-  **Макрос** -это практически конце спектра фокус и близкому, по которому можно сосредоточиться призму.
-  **Бесконечность** – это дальнем конце спектра фокус и наибольшее расстояние, по которому можно сосредоточиться призму.
-  **Расстояние Hyperfocal** — это точка в спектре фокус которого выбирается самый дальний объект в рамке только на дальнем конце фокус. Другими словами это фокальной позиции, которая обеспечивает максимальную глубину поля. 
-  **Колонки позиции** — это область контроля всех указанных выше другие условия. Это является расстояние от группы связанных элементов с датчиков и тем самым контроллером фокус.


Эти условия и знания в виду новые элементы управления фокус вручную можно реализовать в приложении iOS 8 успешно.

### <a name="existing-focus-controls"></a>Существующие элементы управления фокус

iOS 7 и более ранних версий, предоставляемых существующих элементов управления фокус с помощью `FocusMode`свойство как:

-   `AVCaptureFocusModeLocked` — Фокус блокируется в точке одного фокус.
-   `AVCaptureFocusModeAutoFocus` — Камеры выполняет очистку линз через все фокальной точки, пока не обнаруживает sharp фокус и останется там.
-   `AVCaptureFocusModeContinuousAutoFocus` — Камеры refocuses всякий раз при обнаружении условие ожидания в фокусе.


Существующие элементы управления также предоставляются настраиваемым Достопримечательности через`FocusPointOfInterest` свойство, таким образом, пользователь может получить фокус на определенной области. Приложение также можно отслеживать перемещение группы связанных элементов, отслеживая `IsAdjustingFocus` свойство.

Кроме того, ограничение диапазона предоставлен `AutoFocusRangeRestriction` свойство как:

-   `AVCaptureAutoFocusRangeRestrictionNear` — Ограничивает autofocus рядом глубины. Это удобно в ситуациях, такие как сканирование QR-код или штрих-код.
-   `AVCaptureAutoFocusRangeRestrictionFar` — Ограничивает autofocus отдаленных глубины. Это удобно в ситуациях, где находятся объекты, которые заведомо не имеет значения в поле зрения (например, рамку окна).


И, наконец `SmoothAutoFocus` свойство, которое замедляет алгоритм автоматически фокус и его действия с меньшим шагом приращения для устранения перемещений артефактов при записи видео.

### <a name="new-focus-controls-in-ios-8"></a>Новые элементы управления фокус в iOS 8

Помимо функций, уже поддерживаемых по iOS 7 и более поздних версий следующие функции доступны для управления фокус в iOS 8:

-  Элемент управления вручную позиции линз при блокировке фокус.
-  Наблюдение за ключ значение позиции линз в любом режиме фокусировки.


Для реализации выше функций, `AVCaptureDevice` класса был изменен для включения только для чтения `LensPosition` свойство, используемое для получения текущего положения объектив камеры.

Чтобы воспользоваться ручное управление позиции линз, устройств видеозахвата должны быть в режиме фокусировки заблокирован. Пример

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked` Метод устройства записи используется для настройки положения объектив камеры. Необязательный обратный вызов подпрограммы позволяет получать уведомления о изменения вступят в силу. Пример

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Как показано в приведенном выше коде устройств видеозахвата должна быть заблокирована для конфигурации, прежде чем сделать изменения позиции линз. Допустимые значения положения группы связанных элементов — от 0,0 до 1,0.

### <a name="manual-focus-example"></a>Пример вручную фокус

Общая Настройка захвата AV кодом в месте `UIViewController` могут быть добавлены к раскадровке приложения и настраивается следующим образом:

[![](intro-to-manual-camera-controls-images/image5.png "UIViewController могут быть добавлены к приложениям раскадровки и настроено, как показано здесь")](intro-to-manual-camera-controls-images/image5.png#lightbox)

Представление содержит следующие основные элементы:

-  Объект `UIImageView` , будет отображаться канал видео.
-  Объект `UISegmentedControl` , изменится режим фокусировки с автоматического на заблокирована.
-  Объект `UISlider` , будет отображения и обновления в настоящее время группы связанных элементов.


Выполните следующие действия для передачи показатель контроллера представления для элемента управления фокус вручную.


1. Добавьте следующие операторы using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Добавьте следующие частные переменные:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. Добавьте следующие вычисляемые свойства:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Переопределить `ViewDidLoad` метод и добавьте следующий код:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Переопределить `ViewDidAppear` метод и добавьте следующий код в начало записи, после загрузки представления:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. С камеры в режиме Auto ползунок переместит автоматически, как камера настраивает фокус:

    [![](intro-to-manual-camera-controls-images/image6.png "Ползунок будут перемещаться автоматически по мере камеры настраивает фокус в этот пример приложения")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Щелкните сегмент Locked и перетащите ползунок позиции, можно вручную настроить положение группы связанных элементов:

    [![](intro-to-manual-camera-controls-images/image7.png "Вручную Настройка положения группы связанных элементов")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Остановите приложение.


Код, представленный выше показано, как отслеживать положение группы связанных элементов, когда камеры выполняется в автоматическом режиме или используйте ползунок для управления позицией группы связанных элементов, когда он находится в режиме блокировки.

## <a name="manual-exposure"></a>Ручной выдержки

Уязвимости ссылается на яркости изображения относительно яркость источника и определяется по свет достигает датчика, как долго, а также уровень усиления датчика (сопоставление ISO). Предоставляя вручную контролировать доступность, приложения обеспечивают большую свободу для конечного пользователя и разрешить их для достижения стилизованные взгляд.

Использование элементов управления раскрытия вручную, пользователь может делать изображение из что ярко-темную или родственника:

[![](intro-to-manual-camera-controls-images/image8.png "Рисунок, демонстрирующий нарушения безопасности что ярко-родственника и темно-пример")](intro-to-manual-camera-controls-images/image8.png#lightbox)

Опять же это можно сделать автоматически с помощью программный контроль для научных приложений или вручную элементы управления, предоставляемые в пользовательском интерфейсе приложения. В любом случае новый iOS 8 раскрытия интерфейсы API обеспечивают точный контроль параметры экспозиции камеры.

### <a name="how-exposure-works"></a>Как работает раскрытия

Прежде чем обсуждать аспекты контроля воздействия в приложении IOS 8. Давайте кратко рассмотрим, как работает раскрытия:

[![](intro-to-manual-camera-controls-images/image9.png "Как работает раскрытия")](intro-to-manual-camera-controls-images/image9.png#lightbox)

Ниже приведены три основные элементы, которые образуют и контролировать воздействие.

-  **Скорость затвора** — это продолжительность времени, затвора открыт, чтобы позволить свет на датчик камеры. Короче время затвора открыт, меньше света, let и четче изображение является (четкостью движения). Чем длиннее открыт затвора, – это предоставить дополнительные света в, и чем больше движения размытия, возникающее.
-  **Сопоставление ISO** — это термин позаимствован из фильма фотографии и относится к конфиденциальности химические вещества в фильме известными. Значения Low ISO в фильме имеют меньше интервала и возможности более точного цветопередачи; Низкие значения ISO на цифровые датчики имеют снижается уровень шума датчика, но меньше яркости. Чем выше значение ISO, яркость изображения, но с дополнительные шума датчика. «ISO» на цифровой датчик — это мера [электронных прибыль](http://en.wikipedia.org/wiki/Gain), не физическая характеристика. 
-  **Колонки Aperture** — это размер открывающего группы связанных элементов. На всех устройствах iOS диафрагмы объектива фиксирована, поэтому только два значения, которые могут использоваться для корректировки раскрытия выдержка и ISO.


### <a name="how-continuous-auto-exposure-works"></a>Непрерывных Works раскрытия Auto

Прежде чем перейти к как работает Ручная выдержка, он является хорошим полезно понять механизм непрерывных раскрытия автоматически работает на устройстве iOS.

[![](intro-to-manual-camera-controls-images/image10.png "Как работает раскрытия непрерывной автоматически на устройстве iOS")](intro-to-manual-camera-controls-images/image10.png#lightbox)

Сначала представляет собой блок раскрытия автоматически, его задания расчета идеальный раскрытия и постоянно вводятся статистики, отслеживания использования. Он использует эти сведения для которого требуется вычислить оптимальное сочетание ISO и выдержка, чтобы получить хорошо освещена сцены. Этот цикл называется AE цикла.

### <a name="how-locked-exposure-works"></a>Как заблокированные раскрытия Works

Теперь давайте рассмотрим как заблокированные раскрытия работает на устройствах iOS.

[![](intro-to-manual-camera-controls-images/image11.png "Как заблокированные раскрытия работает на устройствах iOS")](intro-to-manual-camera-controls-images/image11.png#lightbox)

Опять же у вас есть блок автоматического раскрытия, который пытается вычислить оптимальное iOS и значения длительности. Однако в этом режиме блок AE отключен от статистики, отслеживания использования ядра.

### <a name="existing-exposure-controls"></a>Существующие элементы управления раскрытия

iOS 7 и более поздних версий, укажите следующие существующие элементы управления раскрытия через `ExposureMode` свойство:

-   `AVCaptureExposureModeLocked` — Примеры сцены один раз и использует эти значения во всей сцены.
-   `AVCaptureExposureModeContinuousAutoExposure` — Примеры сцены непрерывно, чтобы убедиться, что он хорошо освещена.


`ExposurePointOfInterest` Может использоваться для коснитесь, чтобы предоставлять сцены, выбрав целевой объект для предоставления в, а также отслеживать приложение `AdjustingExposure` свойство, чтобы увидеть, когда устанавливается раскрытия.

### <a name="new-exposure-controls-in-ios-8"></a>Новые элементы управления раскрытия в iOS 8

Помимо функций, уже поддерживаемых по iOS 7 и более поздних версий следующие функции теперь доступны для управления раскрытия в iOS 8:

-  Полностью вручную пользовательских раскрытия.
-  Get, Set и ключ значение изучаете IOS и выдержка (длительность).


Для реализации возможностей выше, новый `AVCaptureExposureModeCustom` режим был добавлен. Когда камеры в пользовательского режима, следующий код может использоваться для корректировки длительности раскрытия и ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

В режимах Auto и заблокирована приложение можно настроить сдвиг подпрограмма автоматическую доступность, используя следующий код:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Параметр минимального и максимального диапазонов зависят от устройства, на котором приложение выполняется, поэтому они должны никогда не быть жестко запрограммированный. Вместо этого используйте следующие свойства для получения диапазонов минимальное и максимальное значение:

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


Как показано в приведенном выше коде устройств видеозахвата должна быть заблокирована для конфигурации, прежде чем сделать влияет на доступность.

### <a name="manual-exposure-example"></a>Пример ручной выдержки

Общая Настройка захвата AV кодом в месте `UIViewController` могут быть добавлены к раскадровке приложения и настраивается следующим образом:

[![](intro-to-manual-camera-controls-images/image12.png "UIViewController могут быть добавлены к приложениям раскадровки и настроено, как показано здесь")](intro-to-manual-camera-controls-images/image12.png#lightbox)

Представление содержит следующие основные элементы:

-  Объект `UIImageView` , будет отображаться канал видео.
-  Объект `UISegmentedControl` , изменится режим фокусировки с автоматического на заблокирована.
-  Четыре `UISlider` элементов управления, которые будут отображать и обновлять смещение, длительность, ISO и смещение.


Выполните следующие действия для передачи показатель контроллера представления для экспозиции вручную.


1. Добавьте следующие операторы using:
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Добавьте следующие частные переменные:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. Добавьте следующие вычисляемые свойства:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Переопределить `ViewDidLoad` метод и добавьте следующий код:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. Переопределить `ViewDidAppear` метод и добавьте следующий код в начало записи, после загрузки представления:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. С камеры в режиме Auto ползунки переместит автоматически, как камера корректирует раскрытия:

    [![](intro-to-manual-camera-controls-images/image13.png "Ползунки будут перемещаться автоматически по мере камеры корректирует раскрытия")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Щелкните сегмент Locked и перетащите ползунок смещение для выбора сдвиг автоматическую доступность вручную:

    [![](intro-to-manual-camera-controls-images/image14.png "Ручная настройка сдвиг автоматическую доступность")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Щелкните сегмент Custom и перетащить ползунки длительность и ISO, вручную контролировать воздействие:

    [![](intro-to-manual-camera-controls-images/image15.png "Перемещайте ползунки длительность и ISO для вручную раскрытия элемента управления")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Остановите приложение.


Код, представленный выше показано, как отслеживать параметры экспозиции при камеры в автоматическом режиме и как использовать ползунки для управляющих доступностью, когда он находится в режимах заблокирована "или" другой.

## <a name="manual-white-balance"></a>Ручной баланс белого

Баланс белого элементы управления позволяют пользователям откорректировать баланс colosr в изображение, чтобы сделать их более реалистичные. Различные источники света имеют разные цвета температуры и настройки камеры для записи образа должны быть изменены, чтобы компенсировать эти различия. Опять же, позволяя пользователю контроль над баланс белого они можно корректировать professional, автоматическое подпрограммы не способные использовать для достижения художественного эффектов.

[![](intro-to-manual-camera-controls-images/image16.png "Пример изображения, показывающий корректировок техническом руководстве")](intro-to-manual-camera-controls-images/image16.png#lightbox)

Например перехода на летнее имеет blueish приведения к типу, тогда как tungsten ламп индикаторы теплый, желтый оранжевый оттенок. (Ввести в заблуждение, «холодные» цвета имеют выше температуры цвет, чем «горячего» резервирования. Цвет температуры используются физических мер, не существует восприятия.)

Человеческий ум очень удобен для компенсации для различия в цветовой температуры, но это то, что нельзя сделать веб-камеры. С помощью которого оценка цвет в какой противоположной части спектра, чтобы компенсировать различия цвет работает камеры.

Новый iOS 8 раскрытия API позволяет приложению для контроля процесса и обеспечивает детализированный контроль над параметрами баланс белого камеры.

### <a name="how-white-balance-works"></a>Как белый Works баланс

Прежде чем обсуждать аспекты управления белый баланс в приложение IOS 8. Давайте кратко рассмотрим, как белый works сальдо:

В исследовании в восприятии цвет [CIE 1931 RGB цвета и XYZ CIE 1931 цвет места](http://en.wikipedia.org/wiki/CIE_1931_color_space) первый математически определенные цветовые пространства. Они были созданы комиссии International на освещения (CIE) 1931.

[![](intro-to-manual-camera-controls-images/image17.png "Цветовое пространство RGB CIE 1931 и CIE 1931 XYZ цветовое пространство")](intro-to-manual-camera-controls-images/image17.png#lightbox)

Выше диаграмма показывает все цвета отображается человеческим глазом, выберите темно-синий ярко-зеленый цвет для ярко-красным. Любой точке на диаграмме может отображаться со значением X и Y, как показано на приведенной выше диаграмме.

Как видно на диаграмме значения X и Y, которые могут размещаться в графе, которые бы за пределами диапазона человеческих концепции и в результате эти цвета не удается воспроизвести камеры.

Вызывается меньшего размера кривой на диаграмме выше [Очага Planckian](http://en.wikipedia.org/wiki/Planckian_locus), который выражает цветовой температуры (в градусах Кельвина), с помощью более высоких показателей на стороне синий (еще важнее) и номера нижней на стороне red (холоднее). Это полезно для ситуаций, типичных освещения.

В смешанных освещении белый корректировок нужно будет отклоняться от Planckian позволяет визуально вносить необходимые изменения. В таких ситуациях необходимые корректировки быть сдвинуты либо зеленый или красный пурпурный части CIE масштабирования.

Устройства iOS компенсировать оттенки увеличивая противоположной прибыль цвет. Например если сцена имеет слишком много синий, затем Усиление красного получают более высокий приоритет для компенсации. Прибыль, эти значения являются калибровки для конкретных устройств, поэтому они зависят от устройства.

### <a name="existing-white-balance-controls"></a>Существующие элементы управления баланс белого

iOS 7 и более поздней версии предоставляются следующие существующие элементы управления баланс белого через `WhiteBalanceMode` свойство:

-   `AVCapture WhiteBalance ModeLocked` — Примеры сцены один раз и с помощью этих значений в сцене.
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` — Примеры сцены непрерывно, чтобы убедиться, хорошо сбалансированы.


И приложения можно отслеживать `AdjustingWhiteBalance` свойство, чтобы увидеть, когда устанавливается раскрытия.

### <a name="new-white-balance-controls-in-ios-8"></a>Новые элементы управления Технический баланс в iOS 8

Помимо функций, уже поддерживаемых по iOS 7 и более поздних версий следующие функции доступны для управления баланс белого в iOS 8:

-  Получает полностью ручное управление устройства RGB.
-  Get, Set и наблюдать за ключ-значение RGB устройство получает.
-  Поддержка баланс белого, с помощью карты серого цвета.
-  Процедуры преобразования в и из устройства независимых цветовых пространств.


Для реализации выше функций, `AVCaptureWhiteBalanceGain` структура была добавлена в следующие члены:

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


Максимальный баланс белого прибыль в настоящее время является четырем (4) и может быть готовы из `MaxWhiteBalanceGain` свойство. Поэтому допустимый диапазон — от одного (1) `MaxWhiteBalanceGain` (4) в настоящее время.

`DeviceWhiteBalanceGains` Свойство может использоваться для наблюдения за текущими значениями. Используйте `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` Чтобы откорректировать баланс получает при камеры в режиме заблокированные баланс белого.

#### <a name="conversion-routines"></a>Процедуры преобразования

Процедуры преобразования были добавлены в iOS 8, чтобы помочь с преобразованием и от устройства независимых цветовых пространств. Для реализации процедуры преобразования, `AVCaptureWhiteBalanceChromaticityValues` структура была добавлена в следующие члены:

-   `X` — представляет собой значение от 0 до 1.
-   `Y` — представляет собой значение от 0 до 1.


`AVCaptureWhiteBalanceTemperatureAndTintValues` Структура была также добавлена со следующими членами:

-   `Temperature` -— Это число с плавающей запятой в градусах Кельвина.
-   `Tint` -— смещение от зеленого или пурпурного от 0 до 150 положительными значениями к зеленый направление и отрицательное сторону пурпурный цвет.


Используйте `CaptureDevice.GetTemperatureAndTintValues`и `CaptureDevice.GetDeviceWhiteBalanceGains`методы преобразования между температуры и оттенок, цветности и RGB получить цветовых пространств.

> [!NOTE]
> Процедуры преобразования, более точны, чем ближе значение для преобразования — Planckian позволяет визуально.




#### <a name="gray-card-support"></a>Поддержка серый карт

Apple использует термин World серого для ссылки на Поддержка карт серого цвета, встроенные в iOS 8. Он позволяет сосредоточиться на использования физических карт серого цвета, охватывает по меньшей мере 50% центра кадра, который используется, чтобы откорректировать баланс белый. Карты серого предназначена для достижения белый, появившемся нейтрального.

Это можно реализовать в приложении, запрашивая пользователя для размещения физических серый карт камеры, мониторинг `GrayWorldDeviceWhiteBalanceGains` свойство и ждать, пока значения сопоставления.

Приложение затем привело бы к блокировке баланс белого выигрыш для `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` метод, используя значения из `GrayWorldDeviceWhiteBalanceGains` свойство, чтобы применить изменения.

Устройство записи должен быть заблокирован для конфигурации, прежде чем сделать изменения в баланс белого.

### <a name="manual-white-balance-example"></a>Пример ручной баланс белого

Общая Настройка захвата AV кодом в месте `UIViewController` могут быть добавлены к раскадровке приложения и настраивается следующим образом:

[![](intro-to-manual-camera-controls-images/image18.png "UIViewController могут быть добавлены к приложениям раскадровки и настроено, как показано здесь")](intro-to-manual-camera-controls-images/image18.png#lightbox)

Представление содержит следующие основные элементы:

-  Объект `UIImageView` , будет отображаться канал видео.
-  Объект `UISegmentedControl` , изменится режим фокусировки с автоматического на заблокирована.
-  Два `UISlider` элементов управления, которые будут отображать и обновлять температуры и оттенок.
-  Объект `UIButton` используется для выборки пробел серого цвета карты (серый World), задайте данная с помощью этих значений.


Выполните следующие действия для передачи вверх контроллера представления для элемента управления баланс техническом руководстве.


1. Добавьте следующие операторы using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. Добавьте следующие частные переменные:

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. Добавьте следующие вычисляемые свойства:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. Добавьте следующий частный метод, чтобы задать новый Технический сбалансировать температуры и оттенок:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. Переопределить `ViewDidLoad` метод и добавьте следующий код:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. Переопределить `ViewDidAppear` метод и добавьте следующий код в начало записи, после загрузки представления:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. Сохраните изменения в код и запустить приложение.
1. С камеры в режиме Auto ползунки переместит автоматически, как камера регулирует баланс белого:

    [![](intro-to-manual-camera-controls-images/image19.png "Ползунки автоматически перемещаются как камера регулирует баланс белого")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Щелкните сегмент Locked и перетащить ползунки Temp и оттенок, соответствующим образом изменить баланс белого вручную:

    [![](intro-to-manual-camera-controls-images/image20.png "Перемещайте ползунки Temp и оттенок к соответствующим образом изменить баланс белого вручную")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. С сегментом Locked все еще выбран разместите физических серый карты на передней камеры и коснитесь кнопки серого цвета карты для корректировки баланс белого к миру серого цвета:

    [![](intro-to-manual-camera-controls-images/image21.png "Коснитесь кнопки серого цвета карты для корректировки баланс белого к миру серого")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Остановите приложение.

Код, представленный выше показано, как отслеживать параметры баланс белого при камеры в автоматическом режиме или с помощью ползунков управления баланс белого, когда он находится в режиме блокировки.

## <a name="bracketed-capture"></a>Записи в квадратных скобках

Заключенный в скобки сбора данных на основе параметров из элементов управления вручную камеры, представленные выше, что позволяет приложению можно записать на момент времени, в ряд различных способов.

Проще говоря, заключенных в скобки захвата является по-прежнему изображения, полученные с параметрами из рисунка, рисунок в пакетном режиме.

[![](intro-to-manual-camera-controls-images/image22.png "Как работает заключенный в скобки захвата")](intro-to-manual-camera-controls-images/image22.png#lightbox)

С помощью скобок записи данных в iOS 8, приложение можно предустановку ряд вручную элементы управления камерой, выдавать одну команду и текущей сцене возвращаемых ряд образов для каждого из предустановок, вручную.

### <a name="bracketed-capture-basics"></a>Основные сведения о записи в квадратных скобках

Опять же заключенных в скобки захвата является по-прежнему изображения, полученные с различными параметрами из рисунка рисунок в пакетном режиме. Ниже приведены типы из заключенных в скобки записи доступны.

-  **Auto скобка раскрытия** : где все образы доступны на различных величину смещения.
-  **Вручную скобка раскрытия** — где все образы имеют разнообразных выдержка (длительность) и ISO сумма.
-  **Простой скобка ускорение** — ряд по-прежнему изображения, полученные в быстрой последовательности.


### <a name="new-bracketed-capture-controls-in-ios-8"></a>Новые заключенный в скобки записи элементы управления в iOS 8

Все команды, заключенных в скобки записи, реализуются в `AVCaptureStillImageOutput` класса. Используйте `CaptureStillImageBracket`метод для получения набора изображений с заданным массивом параметров.

Два новых класса были реализованы для обработки параметров.

-   `AVCaptureAutoExposureBracketedStillImageSettings` - У него есть одно свойство, `ExposureTargetBias`которое используется для установки смещения для автоматической экспозиции. 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` - Он имеет два свойства `ExposureDuration` и `ISO` используется для установки скорости затвора и ISO для ручной экспозиционной скобки. 


### <a name="bracketed-capture-controls-dos-and-donts"></a>Записи в квадратных скобках определяет рекомендации и запреты

#### <a name="dos"></a>Что делать

Ниже приведен список требований, которые должны выполняться при с помощью скобок записи элементов управления в iOS 8:

-  Подготовка приложения для ситуации худшего случая записи путем вызова `PrepareToCaptureStillImageBracket` метод.
-  Предположим, что пример буферов будут поступать из один и тот же общий пул.
-  Чтобы освободить память, выделенную предыдущим вызовом подготовки, вызовите `PrepareToCaptureStillImageBracket` еще раз и отправить его в массив один объект.


#### <a name="donts"></a>По работе с кластерами

Ниже приведен список требований, которые не следует выполнять при с помощью скобок записи элементов управления в iOS 8:

-  Не следует смешивать заключенный в скобки записать параметры типов в один символ.
-  Не запрашивать более чем `MaxBracketedCaptureStillImageCount` образов в один символ.


### <a name="bracketed-capture-details"></a>Сведения о записи в квадратных скобках

Следующие сведения следует принимать во внимание при работе с помощью записи заключенный в скобки в iOS 8:

-  Параметры в квадратных скобках временно переопределить `AVCaptureDevice` параметры.
-  Флэш-памяти и по-прежнему параметры стабилизацию изображения игнорируются.
-  Все изображения должны использовать один и тот же формат выходных данных (jpeg, png, и т.д.)
-  Просмотр видео может удалить кадров.
-  В квадратных скобках захвата поддерживается на всех устройствах, совместим с iOS 8.


С учетом этих сведений давайте рассмотрим пример использования заключенных в скобки захвата в iOS 8.

### <a name="bracket-capture-example"></a>Пример записи скобка

Общая Настройка захвата AV кодом в месте `UIViewController` могут быть добавлены к раскадровке приложения и настраивается следующим образом:

[![](intro-to-manual-camera-controls-images/image23.png "UIViewController могут быть добавлены к приложениям раскадровки и настроено, как показано здесь")](intro-to-manual-camera-controls-images/image23.png#lightbox)

Представление содержит следующие основные элементы:

-  Объект `UIImageView` , будет отображаться канал видео.
-  Три `UIImageViews` , будут отображены результаты записи.
-  Объект `UIScrollView` для размещения канал видео и результат представления.
-  Объект `UIButton` позволяет перевести в заключенных в скобки записи с некоторые параметры предустановки.


Выполните следующие действия для передачи вверх контроллера представления для отслеживания заключенный в скобки.


1. Добавьте следующие операторы using:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. Добавьте следующие частные переменные:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. Добавьте следующие вычисляемые свойства:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. Добавьте следующий частный метод для создания представлений рисунков требуемые выходные данные:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. Переопределить `ViewDidLoad` метод и добавьте следующий код:
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. Переопределить `ViewDidAppear` метод и добавьте следующий код:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. Сохраните изменения в код и запустить приложение.
1. Кадра сцены и коснитесь кнопки записи скобок:

    [![](intro-to-manual-camera-controls-images/image24.png "Кадра сцены и коснитесь кнопки записи скобка")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Проведите справа налево, чтобы увидеть три изображения, полученные по заключенный в скобки сбора данных:

    [![](intro-to-manual-camera-controls-images/image25.png "Проведите справа налево, чтобы увидеть три изображения, полученные по заключенный в скобки сбора данных")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Остановите приложение.


Код, представленный выше показано, как настроить и выполнить автоматическое раскрытия заключенный в скобки захвата в iOS 8.

## <a name="summary"></a>Сводка

В этой статье мы рассматриваются общие сведения о новых элементов управления вручную камеры, предоставленной iOS 8 и рассматриваются основы работы предназначении и принципах их работы. Мы предоставили примеры фокус вручную, Ручная выдержка и Баланс белого вручную. Наконец мы присвоили пример, принимая, заключенных в скобки записи с помощью уже говорилось ранее элементы управления камерой вручную

## <a name="related-links"></a>Связанные ссылки

- [ManualCameraControls (образец)](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [Введение в iOS 8](~/ios/platform/introduction-to-ios8.md)
