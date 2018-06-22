---
title: Локализация приложения и строковые ресурсы
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: cfb127500f919b61788087465700dfed213d5eb2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765858"
---
# <a name="application-localization-and-string-resources"></a>Локализация приложения и строковые ресурсы

Локализация приложения — это предоставление альтернативных ресурсов, предназначенную для определенного региона или языкового стандарта. Например может предоставить локализованные строки для разных стран или можно изменить цвета или раскладки определенного языков и региональных параметров. Android будет загрузить и использовать ресурсы, соответствующее языковому стандарту устройства во время выполнения без изменения исходного кода.

Например на следующем рисунке показано одно приложение запускается в трех регионах другое устройство, но текст, отображаемый на каждой кнопке зависит от языкового стандарта, которой присваивается каждому устройству:

[![Примеры три разных языковых стандартов](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

В этом примере содержимое файла разметки, **Main.axml** выглядит примерно так:

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

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Строки ресурсов для трех языках](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Строки ресурсов для трех языках](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Локализация приложений Android

Чтение [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md) советы и рекомендации по локализации мобильных приложений.

[Локализация приложений Android](~/android/app-fundamentals/localization.md) руководство содержит подробные примеры на перевод строки и локализация образов с помощью Xamarin.Android.



## <a name="related-links"></a>Связанные ссылки

- [Локализация приложений Android](~/android/app-fundamentals/localization.md)
- [Общие сведения о локализации](~/cross-platform/app-fundamentals/localization.md)
