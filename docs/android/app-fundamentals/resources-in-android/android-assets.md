---
title: Использование ресурсов Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114864"
---
# <a name="using-android-assets"></a>Использование ресурсов Android

_Активы_ позволяют включать произвольные файлы, такие как текст, xml, шрифты, музыки и видео в приложение. При попытке включить эти файлы как «resources» Android будет обрабатывать их в своей системе ресурсов, и вы не сможете получить необработанные данные. Если вы хотите получить доступ к данным без изменений, ресурсы — один из способов сделать это.

Ресурсы, которые добавлены в проект будет отображаться так же, как файловая система, которая может считывать из приложения с помощью [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
В этой простой демонстрации, мы собираемся добавить ресурс файла текста для проектов, прочитайте его с помощью `AssetManager`и отображает его на текстового представления.


## <a name="add-asset-to-project"></a>Добавить ресурс к проекту

Средства go в `Assets` папку проекта. Добавьте новый текстовый файл в эту папку с именем `read_asset.txt`. Поместите в него какой-либо текст, как «Я поступил из ресурса!».

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio должен иметь набор **действие при построении** этот файл **AndroidAsset**:

![При выборе режима построения AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Visual Studio для Mac должен иметь набор **действие при построении** этот файл **AndroidAsset**:

[![При выборе режима построения AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Выбрав правильный **BuildAction** гарантирует, что файл упаковываются в пакет APK во время компиляции.


## <a name="reading-assets"></a>Средства чтения

Активы считываются с помощью [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Экземпляр `AssetManager` доступен, обратившись к [активы](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) свойство `Android.Content.Context`, такие как действие.
В следующем коде мы откроем наших **read_asset.txt** активов, чтение и отображение его с помощью текстового представления.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```


## <a name="running-the-application"></a>Запуск приложения

Запустите приложение и вы должны увидеть следующее:

![Снимок экрана с примером](android-assets-images/screenshot.png)


## <a name="related-links"></a>Связанные ссылки

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Контекст](https://developer.xamarin.com/api/type/Android.Content.Context/)
