---
title: С помощью средств Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 337a1ed82010658adce40e8946ed1b0361fb6094
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763001"
---
# <a name="using-android-assets"></a>С помощью средств Android

_Активы_ позволяют включить произвольные файлы как текста, xml, шрифты, музыки и видео в приложение. При попытке включить эти файлы как «ресурсы» Android будет преобразовывать их в свою систему ресурсов, и вы не сможете получить необработанные данные. Если вы хотите получить доступ к данным без изменений, ресурсы — это один из способов сделать это.

Активы, добавлен в проект будут отображаться так же, как в файловой системе, может считывать из приложения с помощью [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
В этой демонстрации простых мы собираемся добавить ресурс текстового файла в свой проект прочитать его с помощью `AssetManager`и отображает его текстового представления.


## <a name="add-asset-to-project"></a>Добавьте проект активов

Активы, перейдите раздел `Assets` папку проекта. Добавьте новый текстовый файл в эту папку с именем `read_asset.txt`. Поместите некоторый текст в нем, как «Я, откуда актива!».

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio должен иметь набор **действие при построении** этот файл **AndroidAsset**:

![При выборе режима построения AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Visual Studio для Mac должен иметь набор **действие при построении** этот файл **AndroidAsset**:

[![При выборе режима построения AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Выбор правильного **BuildAction** гарантирует, что файл будет упакована в APK во время компиляции.


## <a name="reading-assets"></a>Средства чтения

Активы считываются с использованием [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Экземпляр `AssetManager` доступен, обратившись к [активы](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) свойство `Android.Content.Context`, например, действия.
В следующем коде откройте нашей **read_asset.txt** активов, чтение содержимого и отобразить его с помощью текстового представления.

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

Запустите приложение и вы увидите следующее:

![Снимок экрана примера](android-assets-images/screenshot.png)


## <a name="related-links"></a>Связанные ссылки

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
