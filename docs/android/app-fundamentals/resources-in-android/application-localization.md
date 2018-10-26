---
title: Локализация приложения и строковые ресурсы
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116528"
---
# <a name="application-localization-and-string-resources"></a>Локализация приложения и строковые ресурсы

Локализация приложения — это процесс предоставления альтернативные ресурсы, предназначенную для определенного региона или языкового стандарта. Например можно предоставить локализованные строки для различных стран, или можно изменить цвета или макета в соответствии с конкретной языков и региональных параметров. Android будет загружать и использовать ресурсы, соответствующее языковому стандарту устройства во время выполнения без изменения исходного кода.

Например на следующем рисунке показано одно приложение запускается в трех регионах другое устройство, но текст, отображаемый в каждой кнопки зависит от языкового стандарта, которой присваивается каждому устройству:

[![Примеры трех различных языковых стандартов](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

В этом примере содержимое файла макета, **Main.axml** выглядит примерно следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

В приведенном выше примере строка для кнопки был загружен из ресурсов, указав идентификатор ресурса для строки:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Строки ресурсов для трех языков](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Строки ресурсов для трех языков](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Локализация приложений Android

Чтение [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md) советы и рекомендации по локализации мобильных приложений.

[Локализации приложений Android](~/android/app-fundamentals/localization.md) руководство содержит подробные примеры, о том, как перевод строки и локализации образов с помощью Xamarin.Android.



## <a name="related-links"></a>Связанные ссылки

- [Локализация приложений Android](~/android/app-fundamentals/localization.md)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
