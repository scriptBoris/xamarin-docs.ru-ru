---
title: Основы Android ресурсов
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: f6be1001e5d3455a94e677f1bb5dc52ca574b873
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764896"
---
# <a name="android-resource-basics"></a>Основы Android ресурсов

Практически все приложения Android будет иметь какой-то ресурсы в них; как минимум они часто имеют макеты интерфейса пользователя в виде XML-файлов. При создании приложения Xamarin.Android ресурсы по умолчанию настраиваются с помощью шаблона проекта Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Файлы ресурсов](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Файлы ресурсов](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Пять файлов, составляющих ресурсов по умолчанию были созданы в папке Resources:

-  **Icon.PNG** &ndash; значок по умолчанию для приложения

-  **Main.AXML** &ndash; файла макет интерфейса пользователя по умолчанию для приложения. Обратите внимание, что при Android использует **.xml** использует расширение файла Xamarin.Android **.axml** расширение имени файла.

-  **Strings.XML** &ndash; таблицу строк для локализации приложения

-  **AboutResources.txt** &ndash; это не является обязательным и спокойно удалить. Он просто содержит общий обзор папки ресурсов и в нем файлов.

-  **Resource.Designer.cs** &ndash; этот файл автоматически создается и обслуживается Xamarin.Android и содержит уникальные идентификаторы назначенных каждому ресурсу. Это очень похожих и в целях идентичен файлу R.java оказывает приложения на языке Java. Он автоматически создается средствами Xamarin.Android и будет заново время от времени.


## <a name="creating-and-accessing-resources"></a>Создание и обращение к ресурсам

Создание ресурсов заключается в добавлении файлов в каталог для данного типа ресурсов. На приведенном ниже снимке экрана показаны строковые ресурсы для немецких языковых стандартов были добавлены в проект. Когда **Strings.xml** был добавлен в файл **действие при построении** автоматически присваивается **AndroidResource** средствами Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Действие для Strings.xml присвоено AndroidResource сборки](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Действие для Strings.xml присвоено AndroidResource сборки](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Это позволяет Xamarin.Android средства для правильной компиляции и внедрять ресурсы в файл APK. Если для какой-либо причине **действие при построении** равно **Android ресурсов**, затем файлы будут исключены из APK и любая попытка загрузки или доступа к ресурсам приведет к ошибке во время выполнения и произойдет сбой приложения.

Кроме того важно отметить, что хотя Android поддерживает только нижний регистр имен файлов для ресурсов элементов, Xamarin.Android является более терпим; он будет поддерживать верхнего и нижнего регистра имен файлов. Соглашение для имена изображений — использование строчные символы подчеркивания в качестве разделителей (например, **Мои\_изображения\_name.png**). Обратите внимание, что имена ресурсов нельзя обработать, если в качестве разделителей используются тире и пробелы.

После добавления ресурсов в проект существует два способа их использования в приложении &ndash; программно (в коде) или из XML-файлов.


## <a name="referencing-resources-programmatically"></a>Ссылки на ресурсы программным способом

Доступ к этим файлам, им назначается уникальный идентификатор ресурса. Этот идентификатор ресурса является целым, определенные в специальный класс с именем `Resource`, которая находится в файле **Resource.designer.cs**, и выглядит примерно так:

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

Каждый идентификатор ресурса содержится во вложенном классе, который соответствует типу ресурса. Например, если файл **Icon.png** был добавлен в проект обновлен Xamarin.Android `Resource` класс, создание вложенный класс с именем `Drawable` с константой внутри с именем `Icon`.
Это позволяет файл **Icon.png** ссылаться в коде в виде `Resource.Drawable.Icon`. `Resource` Класса не следует изменять вручную, как все изменения, внесенные в него будут перезаписаны при Xamarin.Android.

При ссылке на ресурсы программно (в коде), они доступны через класс иерархии ресурсов, который использует следующий синтаксис:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **Имя пакета** &ndash; требуется пакет, который предоставляет ресурс и только в том случае, если используются ресурсы из других пакетов.

-  **ResourceType** &ndash; это вложенный тип ресурса в пределах класса ресурсов, описанных выше.

-  **Имя ресурса** &ndash; это имя файла ресурсов (без расширения) или значение атрибута android: name для ресурсов, которые находятся в XML-элемента.


## <a name="referencing-resources-from-xml"></a>Ссылки на ресурсы из XML

Доступа к ресурсам в XML-файл, выполнив специальный синтаксис:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **Имя пакета** &ndash; требуется пакет, который предоставляет ресурс и только в том случае, если используются ресурсы из других пакетов.

-  **ResourceType** &ndash; это вложенный тип ресурса в пределах класса ресурсов.

-  **Имя ресурса** &ndash; это имя файла ресурсов (*без* файлы с расширением) или значение `android:name` атрибут для ресурсов, находящихся в XML-элемента.

Пример содержимого файла разметки, **Main.axml**, таковы:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

В этом примере имеется [ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview) , требующего drawable ресурса с именем **флаг**. `ImageView` Имеет его `src` атрибута **@drawable/flag**. При запуске действия Android будет выглядеть внутри каталога **ресурсов или Drawable** файл с именем **flag.png** (расширение файла может быть другой формат изображения, например **flag.jpg**) загрузить этот файл и откройте его в `ImageView`.
При запуске этого приложения, он будет выглядеть примерно как на следующем рисунке:

![Локализованные ImageView](android-resource-basics-images/03-localized-screenshot.png)

