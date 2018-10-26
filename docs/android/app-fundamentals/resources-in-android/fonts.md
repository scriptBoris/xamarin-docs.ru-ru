---
title: Шрифты
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/0/2018
ms.openlocfilehash: fb0e1c42efc6bb51dcd899c7e6017007a82d5c7f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105939"
---
# <a name="fonts"></a>Шрифты

## <a name="overview"></a>Обзор

Начиная с API уровня 26, пакет SDK для Android позволяет обработки как ресурсы, так же, как макетов шрифтов или прорисовываемых ресурсов. [Android NuGet 26 библиотеки поддержки](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) будет бэкпортировать новый шрифт интерфейса API для этих приложений, предназначенных для API уровня 14 или более поздней версии.

После предназначенных API 26 или установки v26 библиотеки поддержки Android существует два способа использования шрифтов в приложении Android:

1. **Упаковка шрифтов как ресурс Android** &ndash; таким образом, шрифт всегда доступными для приложения, что приведет к увеличению размера пакета APK. 
2. **Загрузить их** &ndash; Android также поддерживает загрузку шрифт из _поставщика шрифта_. Поставщик шрифта проверяет, если шрифт уже на устройстве. При необходимости шрифт загружены и кэшированы на устройстве. Этот шрифт могут совместно использоваться несколькими приложениями.

Похожие шрифты (или шрифт, который может иметь несколько различных стилей) могут быть сгруппированы в _семейств шрифтов_. Это позволяет разработчикам задавать несколько атрибутов шрифта, например его вес и Android будет автоматически выбирать соответствующий шрифт из семейства шрифтов.

Библиотеки Android поддерживают v26 будет бэкпортировать поддержка шрифтов для уровень API 26. При разработке для более старых уровней API, бывает необходимо объявить `app` пространство имен XML и присваивать различные атрибуты шрифта с помощью `android:` пространства имен и `app:` пространства имен. Если только `android:` используется пространство имен, а затем шрифты не будет отображаемых устройств под управлением уровень API 25 или менее. Например, этот фрагмент кода XML объявляет новый [ _семейство шрифтов_ ](#font_families) ресурс, который будет работать в API уровня 14 и более поздних версий:

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

До тех пор, пока шрифтов предоставляются в приложение Android должным образом, они могут применяться к мини-приложение пользовательского интерфейса, установив [ `fontFamily` атрибут](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Например следующий фрагмент кода демонстрирует способ отображения шрифта в текстового представления:

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

В этом руководстве сначала рассматривается использование шрифтов как ресурс Android и затем перейдем к описывается загрузка шрифтов во время выполнения.

## <a name="fonts-as-a-resource"></a>Шрифты в качестве ресурса

Упаковку шрифта в Android APK гарантирует, что следует всегда доступными для приложения. Файл шрифта (либо. TTF или. Файл OTF) добавляется в приложение Xamarin.Android так же, как и любого другого ресурса, путем копирования файлов в подкаталоге в **ресурсы** папку проекта Xamarin.Android. Ресурсы шрифты хранятся в **шрифта** вложенный каталог **ресурсы** папку проекта. 

> [!NOTE]
> Шрифты должны иметь **действие при построении** из **AndroidResource** или они не будет упакован в конечного пакета APK. Действие построения автоматически задается в интегрированной среде разработки.

При наличии многих аналогичные файлы шрифтов (например, тот же шрифт с разными весами или стили) можно объединить их в семейство шрифтов.

<a name="font_families" />

### <a name="font-families"></a>Семейство шрифтов

Семейство шрифтов — это набор шрифты, которые имеют разный вес и стили. Например может быть файлы шрифта для шрифтов полужирным шрифтом или курсивом. Семейство шрифтов определяется `font` элементов в XML-файл, который хранится в **ресурсы/шрифта** каталога. Каждое семейство шрифта должен иметь собственный XML-файл.

Чтобы создать семейство шрифтов, сначала добавьте все шрифты **ресурсы/шрифта** папки. Затем создайте новый XML-файл в папке шрифта семейства шрифтов. Имя XML-файла не имеет соответствия или отношения к шрифтам, на которую ссылается; файл ресурсов может быть любое имя файла ресурсов юридические Android. Этот XML-файл будет иметь корень `font-family` элемент, который содержит один или несколько `font` элементов. Каждый `font` элемент объявляет атрибуты шрифта.

Следующий XML-код является примером семейство шрифтов для _источников Sans Pro_ шрифт, который определяет множество весовых значений другой шрифт. Он сохраняется в виде файла в **ресурсы/шрифта** папку с именем **sourcesanspro.xml**:

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

* **Обычный** &ndash; стандартного шрифта
* **курсивом** &ndash; курсив

`fontWeight` Атрибут соответствует CSS `font-weight` атрибут и ссылается на толщину шрифта. Это значение в диапазоне от 100-900. Ниже перечислены общие значения Вес шрифта и его имя.

* **Тонкие** &ndash; 100
* **Очень света** &ndash; 200
* **Свет** &ndash; 300
* **Обычный** &ndash; 400
* **Средний** &ndash; 500
* **Полужирным шрифтом Semi** &ndash; 600
* **Полужирным шрифтом** &ndash; 700
* **Жирный** &ndash; 800
* **Черный** &ndash; 900

После определения семейства шрифтов, его можно использовать декларативно, задав `fontFamily`, `textStyle`, и `fontWeight` атрибуты в файле макета.  Например следующий фрагмент кода XML задает 600 Вес шрифта (обычный) и стиле курсивный текст:

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

### <a name="programmatically-assigning-fonts"></a>Программного связывания шрифтов

Шрифты можно задать программно с помощью [ `Resources.GetFont` ](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) метод для извлечения [ `Typeface` ](https://developer.android.com/reference/android/graphics/Typeface.html) объекта. Многие представления имеют `TypeFace` свойство, которое может использоваться для назначения шрифт мини-приложения. В этом фрагменте кода показано, как программно задать шрифт для текстового представления:

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

`GetFont` Метод будет автоматически загружаться первой шрифта семейства шрифтов.  Чтобы загрузить шрифт, который соответствует определенному стилю, используйте `Typeface.Create` метод. Этот метод будет пытаться загрузить шрифт, который соответствует указанному стилю. Например, этот фрагмент кода будет пытаться загрузить полужирным `Typeface` объект из семейства шрифтов, который определен в **ресурсы/шрифты**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Загрузка шрифтов

Вместо упаковка шрифтов в качестве ресурса приложения Android можно скачать шрифты из удаленного источника. Это будет влиять на желательно уменьшения размера пакета APK. 

Шрифты загружаются с помощью _поставщика шрифта_. Это специализированный поставщик содержимого, который управляет загрузкой и кэшированием шрифтов для всех приложений на устройстве. Android 8.0 включает поставщика шрифтов для загрузки шрифты из [репозиторий Google шрифта](http://fonts.google.com). Этот поставщик шрифта по умолчанию — дистрибутивы API уровня 14 с v26 библиотеки поддержки Android.

Когда приложение выполняет запрос для шрифта, поставщик шрифта сначала проверит, см. в разделе, если шрифт уже на устройстве. В противном случае он попытается загрузить шрифт. Если шрифт не может быть Скачанный, затем Android будет использовать системный шрифт по умолчанию. После скачивания шрифт, она доступна для всех приложений на устройстве, не только к приложению, который выполняет начальный запрос.

При запросе для загрузки шрифта, приложение не запрашивает поставщика шрифта напрямую. Вместо этого приложения будут использовать экземпляр [ `FontsContract` ](https://developer.android.com/reference/android/provider/FontsContract.html) API (или [ `FontsContractCompat` ](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) при использовании 26 библиотеки поддержки).  

Android 8.0 поддерживает загрузку шрифты двумя разными способами:

1. **Объявите шрифтов как ресурс** &ndash; приложение может объявлять загружаемые шрифты в приложения Android с помощью XML-файлы ресурсов. Эти файлы будут содержать все метаданные, необходимо асинхронно загружаете шрифты, когда приложение запускается и кэшировать их на устройстве Android.
2. **Программно** &ndash; API-интерфейсов в Android API уровня 26 позволяют приложению загрузить их программным образом, во время работы приложения. Приложения будет создан `FontRequest` для данного шрифта и передать этот объект для `FontsContract` класса. `FontsContract` Принимает `FontRequest` и извлекает шрифт из _поставщика шрифта_. Android загружается синхронно шрифта. Пример создания `FontRequest` будет показано далее в этом руководстве.

Независимо от того, какой подход используется можно загрузить файлы ресурсов, необходимо добавить в приложение Xamarin.Android перед шрифты. Во-первых, шрифты должны объявляться в XML-файл в **ресурсы/шрифта** каталог как часть семейства шрифтов. Этот фрагмент кода приведен пример того, как загрузить шрифты из [Google шрифты открытая исходная коллекиця](https://fonts.google.com) с помощью поставщика шрифта по умолчанию, который поставляется с Android 8.0 (или библиотека поддержки v26):

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

`font-family` Элемент содержит следующие атрибуты, объявляющий сведения, что для Android требуется их загрузке:

1. **fontProviderAuthority** &ndash; центра поставщика шрифта, который будет использоваться для запроса.
2. **fontPackage** &ndash; пакета для поставщика шрифта, используемый для запроса. Используется для проверки подлинности поставщика.
3. **fontQuery** &ndash; это строка, которая поможет найти требуемом шрифте поставщик шрифта. Сведения о запросе шрифта относятся только к поставщику шрифта. [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) В класс [загружаемые шрифты](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) пример приложения приведены некоторые сведения о формата запроса для шрифтов, из исходной коллекции откройте шрифты Google.
4. **fontProviderCerts** &ndash; массив ресурсов, в списке наборов хеш-кодов для сертификатов, поставщик должен быть подписан.

После определения шрифтов может потребоваться для предоставления сведений о _сертификаты шрифта_ связанных с загрузкой.

### <a name="font-certificates"></a>Сертификаты шрифта

Если на устройстве не предустановлена поставщика шрифта или если приложение использует `Xamarin.Android.Support.Compat` библиотека Android требует сертификаты безопасности поставщика шрифта. Эти сертификаты будут перечислены в файле ресурсов массив, который хранится в **ресурсы и значения, которые** каталога. 

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

С этими файлами ресурсов в месте приложение может выполнять загрузку шрифтов.

### <a name="declaring-downloadable-fonts-as-resources"></a>Объявление шрифтов как ресурсов

Перечислив загружаемые шрифты в **AndroidManifest.XML**, Android асинхронно загружает шрифты при первом запуске приложения. Шрифт на сами, перечислены в файле ресурсов массива, следующего вида: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Чтобы загрузить эти шрифты, они должны быть объявлены в **AndroidManifest.XML** , добавив `meta-data` как дочерний `application` элемент. Например, если загружаемые шрифты объявляются в файле ресурсов в **Resources/values/downloadable_fonts.xml**, а затем этот фрагмент пришлось бы добавить в манифест: 

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Загрузку шрифт с интерфейсами API шрифта

Можно программным образом загрузить шрифта путем создания экземпляра [ `FontRequest` ](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) объекта и передачи для `FontContractCompat.RequestFont` метод. `FontContractCompat.RequestFont` Метод сначала проверьте, существует ли шрифт на устройстве и затем при необходимости будет асинхронно запроса поставщика шрифта и попытаться загрузить шрифт для приложения. Если `FontRequest` не удается загрузить шрифт, то Android будет использовать системный шрифт по умолчанию. 

Объект `FontRequest` объект содержит сведения, которые будут использоваться поставщиком шрифта для обнаружения и загрузки шрифта. Объект `FontRequest` необходимы четыре элемента информации:

1. **Центр поставщика шрифта** &ndash; центра поставщика шрифта, который будет использоваться для запроса.
2. **Пакет шрифтов** &ndash; пакета для поставщика шрифта, используемый для запроса. Используется для проверки подлинности поставщика.
3. **Запрос шрифта** &ndash; это строка, которая поможет найти требуемом шрифте поставщик шрифта. Сведения о запросе шрифта относятся только к поставщику шрифта. Сведения о строке относятся к поставщику шрифта. [ `QueryBuilder` ](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) В класс [загружаемые шрифты](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) пример приложения приведены некоторые сведения о формата запроса для шрифтов, из исходной коллекции откройте шрифты Google.
4. **Сертификаты поставщика шрифта** &ndash; массив ресурсов, в списке наборов данных из хэша для сертификата, поставщик должен быть подписан. 

В этом фрагменте приведен пример создания нового `FontRequest` объекта:

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

В предыдущем фрагменте `FontToDownload` запрос, который поможет шрифт из коллекции с открытым исходным кодом шрифты Google. 

Перед передачей `FontRequest` для `FontContractCompat.RequestFont` метод, имеется два объекта, которые должны быть созданы:

* **`FontsContractCompat.FontRequestCallback`** &ndash; Это абстрактный класс, который необходимо расширить. Это обратный вызов, который будет вызываться при `RequestFont` завершения работы. Приложение Xamarin.Android должно подкласс `FontsContractCompat.FontRequestCallback` и переопределить `OnTypefaceRequestFailed` и `OnTypefaceRetrieved`, предоставляя действия, которые должны выполняться при загрузке проходит или не проходит соответственно.
* **`Handler`** &ndash; Это `Handler` которого будет использоваться `RequestFont` для загрузки шрифта в потоке, при необходимости. Шрифты должны **не** загружаться в потоке пользовательского интерфейса.

В этом фрагменте приведен пример C# класс, который асинхронно загрузит шрифта с открытым исходным кодом шрифты Google коллекции. Он реализует `FontRequestCallback` интерфейс и вызывает C# событий при `FontRequest` завершения. 

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

Для использования этого вспомогательного объекта новый `FontDownloadHelper` создается и назначается обработчик событий:  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) => 
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Сводка

В этом руководстве рассматриваются новые интерфейсы API в Android 8.0 для поддержки шрифтов и шрифты как ресурсы. Это обсуждалось внедрение шрифтов, существующих в пакете APK и использовать их в макете. Он также описано, как Android 8.0 поддерживает загрузку шрифты, от поставщика шрифта, программно или путем объявления шрифта метаданных в файлах ресурсов.

## <a name="related-links"></a>Связанные ссылки

- [Семейство шрифтов](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Шрифт](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Поддержка Android NuGet библиотеки 26](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Использование шрифтов в Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Спецификация Вес шрифта CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Коллекции с открытым исходным кодом шрифты Google](https://fonts.google.com/)
- [Источник Sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
