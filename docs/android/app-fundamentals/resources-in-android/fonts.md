---
title: Шрифты
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/13/2018
ms.openlocfilehash: 086576ea7d806bb0768fbe4563df7fca99244ccb
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044825"
---
# <a name="fonts"></a>Шрифты

## <a name="overview"></a>Обзор

Начиная с API уровня 26, шрифты, которые следует рассматривать как ресурсы, так же, как макеты позволяет пакета SDK для Android или drawables. [Android NuGet 26 библиотеки поддержки](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) будет backport нового шрифта API-Интерфейсы для этих приложений, которые целевой уровень API 14 или более поздней версии.

После API 26 для различных версий или установка v26 библиотеку поддержки Android можно использовать шрифты в приложения двумя способами:

1. **Пакет шрифт как ресурс Android** &ndash; благодаря шрифта всегда доступны для приложения, что приведет к увеличению размера APK. 
2. **Загрузить их** &ndash; Android поддерживает загрузки шрифта из _поставщика шрифта_. Поставщик шрифта проверяет, если шрифт на устройстве. При необходимости шрифт загружены и кэшированы на устройстве. Он может использоваться между несколькими приложениями.

Похожие шрифты (или, возможно, несколько различных стилей шрифта) могут быть сгруппированы в _семейств шрифтов_. Это позволяет разработчикам указывать определенные атрибуты шрифта, например его вес и Android автоматически выбирает соответствующий шрифт из семейства шрифтов.

V26 библиотеку поддержки Android будут backport поддержка шрифтов уровень API 26. Если старые уровни API, бывает необходимо объявить `app` пространство имен XML и присвойте различных атрибутов шрифта с помощью `android:` пространства имен и `app:` пространства имен. Если только `android:` используется пространство имен, а затем шрифты не будет отображаемых устройств, под управлением API уровня 25 или. Например, этот фрагмент XML объявляет новый [ _семейство шрифтов_ ](#font_families) ресурса, который будет работать на уровне API 14 и более поздних версий:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular" 
            android:fontStyle="normal" 
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular" 
            app:fontStyle="normal" 
            app:fontWeight="400" />

</font-family>
```

При условии, что шрифтов предоставляются для приложения должным образом, они могут быть применены к графического пользовательского интерфейса, задав [ `fontFamily` атрибут](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Например следующий фрагмент кода демонстрирует отображения шрифта в текстового представления:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

В этом руководстве рассматривается использование шрифтов Android ресурс и затем перейти к описывают, как загрузить шрифты во время выполнения.

## <a name="fonts-as-a-resource"></a>Шрифты в виде ресурса

Упаковка шрифта в Android apk-ФАЙЛ гарантирует, что он будет доступны для приложения. Файл шрифта (либо. TTF или. Файл OTF) добавляется к приложению Xamarin.Android так же, как и любой другой ресурс путем копирования файлов на подкаталог в **ресурсов** папку проекта Xamarin.Android. Шрифты ресурсы хранятся в **шрифта** из подкаталога **ресурсов** папку проекта. 

> [!NOTE]
> Шрифты должны иметь **действие при построении** из **AndroidResource** или они не будет упакован в окончательный APK. Действие построения автоматически задается в интегрированной среде разработки.

При наличии многих аналогичных файлов шрифта (например, тот же шрифт с разные веса нагрузки или стили) можно объединить их в семейство шрифтов.

<a name="font_families" />

### <a name="font-families"></a>Семейство шрифтов

Семейство шрифтов — это набор шрифтов, которые имеют разные веса нагрузки и стили. Например может возникнуть файлы шрифта для шрифтов полужирным шрифтом или курсивом. Семейство шрифтов определяется `font` элементов в XML-файл, который сохраняется в **ресурсы или шрифта** каталога. Каждое семейство шрифтов должен иметь собственный XML-файл.

Для создания семейства шрифтов, сначала добавить все шрифты в **ресурсы или шрифта** папки. Затем создайте новый XML-файл в папке шрифта для семейства шрифтов. Имя XML-файла не имеет сходство или отношения к шрифтам, на которую выполняется ссылка; файл ресурсов может быть любое имя файла ресурсов юридических Android. Этот XML-файл будет иметь корневой `font-family` элемент, который содержит один или несколько `font` элементов. Каждый `font` элемент объявляет атрибуты шрифта.

Следующий XML-код является примером семейство шрифтов для _источников Sans Pro_ шрифт, который определяет множество весовых значений другой шрифт. Он сохраняется в виде файла в **ресурсы или шрифта** папку с именем **sourcesanspro.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular" 
          android:fontStyle="normal" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular" 
          app:fontStyle="normal" 
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold" 
          android:fontStyle="normal" 
          android:fontWeight="800" 
          app:font="@font/sourcesanspro_bold" 
          app:fontStyle="normal" 
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic" 
          android:fontStyle="italic" 
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic" 
          app:fontStyle="italic" 
          app:fontWeight="400" />
</font-family>
```

`fontStyle` Атрибут имеет два возможных значения:

* **Обычный** &ndash; обычный шрифт
* **Курсив** &ndash; курсив

`fontWeight` Атрибут соответствует CSS `font-weight` атрибута и ссылается на толщину шрифта. Это значение в диапазоне от 100 900. Ниже описаны общие значения Вес шрифта и его имя.

* **Тонкие** &ndash; 100
* **Лишние светлой** &ndash; 200
* **Индикатор** &ndash; 300
* **Обычный** &ndash; 400
* **Средний** &ndash; 500
* **Полусоединения полужирным** &ndash; 600
* **Bold** &ndash; 700
* **Жирный** &ndash; 800
* **Черный** &ndash; 900

После определения семейство шрифтов, он может использоваться декларативно, задав `fontFamily`, `textStyle`, и `fontWeight` атрибутов в файл макета.  Например в следующем фрагменте XML устанавливает (обычные) 400 Вес шрифта и стиля текста курсивом.

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Назначение шрифты программным способом

Шрифты можно задать программно с помощью [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) метод для извлечения [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) объекта. Многие представления имеют `TypeFace` свойство, которое может использоваться для назначения шрифт мини-приложения. Следующий фрагмент кода показывает, как программно задать шрифт для текстового представления:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont` Метод автоматически загрузит первый шрифт в семейство шрифтов.  Чтобы загрузить шрифт, который соответствует определенному стилю, используйте `Typeface.Create` метод. Этот метод будет пытаться загрузить шрифт, который соответствует указанного стиля. Например, этот фрагмент будет пытаться загрузить полужирным `Typeface` объекта из семейства шрифтов, который определен в **ресурсы и шрифты**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Загрузка шрифтов

Вместо шрифтов упаковки в качестве ресурса приложения Android можно загрузить шрифты из удаленного источника. Это отразится желательно уменьшения размера APK. 

Шрифты загружаются с помощью _поставщика шрифта_. Это специализированный поставщик содержимого, управляющий загрузкой и кэшированием шрифтов для всех приложений на устройстве. Android 8.0 включает поставщик шрифта для загрузки всех шрифтов из [Google шрифта репозитория](http://fonts.google.com). Этот поставщик шрифта по умолчанию — backported уровень API 14 v26 библиотеку поддержки Android.

Когда приложение выполняет запрос для шрифта, поставщик шрифта сначала проверит шрифт ли уже на устройстве. В противном случае он предпримет попытку загрузить шрифт. Если шрифт не может быть загруженного, затем Android будет использовать системный шрифт по умолчанию. После загрузки шрифт будет доступен для всех приложений на устройстве, а не только приложения, который создал запрос на начальной.

При выполнении запроса для загрузки шрифта, приложение не запрашивает поставщика шрифта напрямую. Вместо этого приложения будут использовать экземпляр [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (или [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) при использовании 26 библиотеки поддержки).  

Android 8.0 поддерживает загрузку шрифты двумя способами:

1. **Объявите загружаемые шрифты как ресурс** &ndash; приложение может объявить загружаемые шрифты для Android через файлы ресурсов XML. Эти файлы будут содержать все метаданные, необходимо асинхронно загрузки шрифтов, когда приложение запускается и кэшировать их на устройстве Android.
2. **Программно** &ndash; API-интерфейсы Android API уровня 26 позволяют приложению загрузить их программно, пока работает приложение. Приложения будет создан `FontRequest` для данного шрифта и передать этот объект для `FontsContract` класса. `FontsContract` Принимает `FontRequest` и получает начертание шрифта с _поставщика шрифта_. Android синхронно загрузит шрифта. Пример создания `FontRequest` будет показано далее в этом руководстве.

Независимо от того, какой подход используется можно загрузить файлы ресурсов, которые необходимо добавить в приложение Xamarin.Android перед шрифты. Во-первых, шрифты должны объявляться в XML-файл в **ресурсы или шрифта** каталог как часть семейство шрифтов. Этот фрагмент приведен пример того, как загрузить шрифты из [Google шрифты откройте исходную коллекцию](https://fonts.google.com) с использованием поставщика шрифта по умолчанию, который поставляется с Android 8.0 (или библиотека поддержки v26):

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts" 
             android:fontProviderPackage="com.google.android.gms" 
             android:fontProviderQuery="VT323" 
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts" 
             app:fontProviderPackage="com.google.android.gms" 
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

`font-family` Элемент содержит следующие атрибуты, объявляющий сведения, что Android требуется их загрузке:

1. **fontProviderAuthority** &ndash; центра поставщика шрифта, который будет использоваться для запроса.
2. **fontPackage** &ndash; пакета для поставщика шрифт, используемый для запроса. Используется для проверки подлинности поставщика.
3. **fontQuery** &ndash; это строка, которая поможет найти требуемый шрифт поставщик шрифта. Сведения о запросе шрифта характерные для поставщика шрифта. [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) Класса в [загружаемые шрифты](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) пример приложения приведены некоторые сведения о формата запроса шрифты Google шрифты откройте исходной коллекции.
4. **fontProviderCerts** &ndash; массиву ресурсов со списком наборов хэша для сертификата, поставщик должен быть подписан.

После определения шрифты может оказаться необходимым предоставить сведения о _сертификаты шрифта_ на загрузку.

### <a name="font-certificates"></a>Сертификаты шрифта

Если поставщик шрифта не предустановлено на устройстве или если приложение использует `Xamarin.Android.Support.Compat` библиотека Android требуются сертификаты безопасности поставщика шрифта. Эти сертификаты будут перечислены в файле ресурсов массив, который сохраняется в **ресурсы или значений** каталога. 

Например, следующий код XML с именем **Resources/values/fonts_cert.xml** и сохраняет сертификаты для поставщика шрифта Google: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

С этими файлами ресурсов в месте приложение может выполнять загрузку шрифты.

### <a name="declaring-downloadable-fonts-as-resources"></a>Объявление загружаемые шрифты как ресурсы

Путем перечисления загружаемые шрифты в **AndroidManifest.XML**, Android асинхронно загружает шрифты при первом запуске приложения. Шрифт в сами, перечислены в файле ресурсов массива, следующим образом: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Чтобы загрузить эти шрифты, они должны быть объявлены в **AndroidManifest.XML** путем добавления `meta-data` как дочерний `application` элемента. Например, если загружаемые шрифты, объявляются в файле ресурсов на **Resources/values/downloadable_fonts.xml**, а затем этот фрагмент необходимо будет добавлен в манифест: 

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Загрузка шрифта с интерфейсами API шрифта

Можно программным образом загрузить шрифта путем создания экземпляра [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) объекта и передачи для `FontContractCompat.RequestFont` метод. `FontContractCompat.RequestFont` Метод сначала проверьте, существует ли шрифт на устройстве и затем при необходимости будет асинхронно запроса поставщика шрифта и попробуйте загрузить шрифт для приложения. Если `FontRequest` не удается загрузить шрифт, то Android будет использовать системный шрифт по умолчанию. 

Объект `FontRequest` объект содержит сведения, которые будут использоваться поставщиком шрифта для обнаружения и загрузки шрифта. Объект `FontRequest` требует сведения в четырех разделах:

1. **Центр поставщика шрифта** &ndash; центра поставщика шрифта, который будет использоваться для запроса.
2. **Пакет шрифта** &ndash; пакета для поставщика шрифт, используемый для запроса. Используется для проверки подлинности поставщика.
3. **Запрос шрифта** &ndash; это строка, которая поможет найти требуемый шрифт поставщик шрифта. Сведения о запросе шрифта характерные для поставщика шрифта. Сведения о строке характерные для поставщика шрифта. [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) Класса в [загружаемые шрифты](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) пример приложения приведены некоторые сведения о формата запроса шрифты Google шрифты откройте исходной коллекции.
4. **Сертификаты поставщика шрифта** &ndash; массиву ресурсов со списком наборов хэша для сертификата, поставщик должен быть подписан. 

Этот фрагмент является примером создания нового `FontRequest` объекта:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

В приведенном выше фрагменте `FontToDownload` запрос, который поможет шрифт Google шрифты откройте исходной коллекции. 

Перед передачей `FontRequest` для `FontContractCompat.RequestFont` метода, имеется два объекта, которые должны быть созданы:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Это абстрактный класс, который должен быть расширен. Это обратный вызов, который будет вызываться при `RequestFont` завершения работы. Подкласс необходимо приложение Xamarin.Android `FontsContractCompat.FontRequestCallback` и Переопределите `OnTypefaceRequestFailed` и `OnTypefaceRetrieved`, предоставляя действия, выполняемые при загрузке проходит или не проходит соответственно.
* **`Handler`** &ndash; Это `Handler` который будет использоваться `RequestFont` для загрузки шрифта в потоке. Шрифты должны **не** загружаться в потоке пользовательского интерфейса.

Ниже приведен пример класса C#, который асинхронно загрузит шрифта с открытым исходным кодом шрифты Google коллекции. Он реализует `FontRequestCallback` интерфейсов и вызывает событие C# при `FontRequest` завершения. 

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };


    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Для использования этого вспомогательного объекта новый `FontDownloadHelper` создания и назначения обработчика событий:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) => 
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Сводка

В этом руководстве рассматриваются новые интерфейсы API в Android 8.0, для поддержки загрузки и шрифты как ресурсы. Он рассматривается как внедрение шрифтов, существующих в apk-ФАЙЛ и использовать их в макете. Также рассматривается, как Android 8.0 поддерживает загрузку шрифты от поставщика шрифта, программным путем или путем объявления шрифта метаданных в файлах ресурсов.

## <a name="related-links"></a>Связанные ссылки

- [Семейство FontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Начертание шрифта](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Поддержка Android библиотеки 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Использование шрифтов в Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Спецификация Вес шрифта CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Коллекция открытой шрифты Google](https://fonts.google.com/)
- [Источник Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
