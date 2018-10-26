---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: f43147d98599d36aa136e4ddc2975205e0e69e26
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122846"
---
# <a name="textureview"></a>TextureView

`TextureView` Класс является представлением, который использует Двухмерная отрисовка с аппаратным ускорением для включения видео или OpenGL поток содержимого для отображения. Например, на следующем рисунке показан `TextureView` отображения динамического канала из камеры устройства:

[![Снимок экрана с примером динамического изображения из камеры устройства](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

В отличие от `SurfaceView` TextureView класс, который может также использоваться для отображения OpenGL или видео содержимого, не отображается в отдельном окне.
Таким образом `TextureView` может поддерживать преобразований представления, как любое другое представление. Например, смена `TextureView` может быть выполнено путем простого указания его `Rotation` свойство, его прозрачность, установив его `Alpha` свойства и т. д.

Поэтому при использовании `TextureView` теперь можно выполнять следующие действия отображения потока в реальном времени от камеры и преобразует их, как показано в следующем коде:

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;
       
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;
           
        SetContentView (_textureView);
    }
       
    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }
           
        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

Приведенный выше код создает `TextureView` экземпляра в действии `OnCreate` метод и задает действие как `TextureView`в `SurfaceTextureListener`. Чтобы быть `SurfaceTextureListener`, реализует действие `TextureView.ISurfaceTextureListener` интерфейс. Система будет вызывать `OnSurfaceTextAvailable` метод при `SurfaceTexture` будет готов к использованию. В этом случае мы берем `SurfaceTexture` , передается в и присвойте ему значение камеры Предварительная версия текстуры. Затем мы свободны в обычный режим работы на основе представлений, например задать `Rotation` и `Alpha`, как показано в приведенном выше примере. Ниже приведен результирующий приложение, работающее на устройстве:

[![Пример приложения, работающие на устройствах, отображение изображения](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Чтобы использовать `TextureView`, аппаратное ускорение должна быть включена, она будет по умолчанию, начиная с API уровня 14. Кроме того, поскольку в этом примере используется камеры, как `android.permission.CAMERA` разрешение и `android.hardware.camera` функция должна быть задана в **AndroidManifest.xml**.



## <a name="related-links"></a>Связанные ссылки

- [TextureViewDemo (пример)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
