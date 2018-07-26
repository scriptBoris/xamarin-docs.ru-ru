---
title: Основы ресурсов Android
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 207644f5a5d3d346214ba090dcd450e55fde2657
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241322"
---
# <a name="android-resource-basics"></a>Основы ресурсов Android

Почти все приложения Android будут какую-либо ресурсы в них; как минимум они часто имеют макеты интерфейса пользователя в виде XML-файлов. При создании приложения Xamarin.Android, по умолчанию ресурсы настроены с помощью шаблона проекта Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Файлы ресурсов](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Файлы ресурсов](android-resource-basics-images/01-resource-files-xs.png)
 
-----

Пять файлов, составляющих ресурсы по умолчанию были созданы в папке ресурсов:

-  **Icon.PNG** &ndash; значок по умолчанию для приложения

-  **Main.AXML** &ndash; файл макета пользовательского интерфейса по умолчанию для приложения. Обратите внимание, что при Android использует **.xml** Xamarin.Android использует расширение файла **.axml** расширение файла.

-  **Strings.XML** &ndash; таблицу строк для локализации приложения

-  **AboutResources.txt** &ndash; это не является обязательным и может быть безопасно удален. Он просто содержит общий обзор папку «ресурсы» и в нем файлов.

-  **Resource.Designer.cs** &ndash; этот файл автоматически создается и обслуживается Xamarin.Android и содержит уникальный идентификатор назначенные каждому ресурсу. Это очень похожих и в цели идентичен R.java файла, которое окажет Android приложения, написанного на языке Java. Он создается автоматически средствами Xamarin.Android и будет повторно время от времени.


## <a name="creating-and-accessing-resources"></a>Создание и обращение к ресурсам

Создание ресурсов является простым добавлением файлов в каталог для выбранного типа ресурса. Снимке экрана ниже показано, что строковые ресурсы для немецких языковых стандартов были добавлены в проект. Когда **Strings.xml** был добавлен в файл **действие при построении** было автоматически установлено на **AndroidResource** средствами Xamarin.Android:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Действие для Strings.xml присвоено AndroidResource сборки](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Действие для Strings.xml присвоено AndroidResource сборки](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

Благодаря этому средства Xamarin.Android для правильной компиляции и внедрять ресурсы в файл APK. Если для какой-либо причине **действие при построении** равно **ресурсов Android**, затем файлы будут исключены из пакета APK и любая попытка загрузки или доступа к ресурсам приведет к ошибке времени выполнения и произойдет аварийное завершение приложения.

Кроме того важно отметить, что а Android поддерживает только нижний регистр имен файлов для элементов ресурсов, Xamarin.Android более терпим; она будет поддерживать имена файлов верхнего и нижнего регистра. Соглашение, имена образов — использовать строчные с символами подчеркивания в качестве разделителей (например, **моей\_изображение\_name.png**). Обратите внимание на то, что имена ресурсов нельзя обработать, если в качестве разделителей используются тире и пробелы.

Когда ресурсы были добавлены в проект, существует два способа их использования в приложении &ndash; программно (в коде) или из XML-файлов.


## <a name="referencing-resources-programmatically"></a>Программное создание ссылок на ресурсы

Доступ к этим файлам, они назначены уникальный идентификатор ресурса. Этот идентификатор ресурса является целым, определенные в специальный класс с именем `Resource`, который можно найти в файле **Resource.designer.cs**, и выглядит примерно следующим образом:

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

Каждый идентификатор ресурса содержится во вложенном классе, который соответствует типу ресурсов. Например, если файл **Icon.png** был добавлен в проект Xamarin.Android обновлены `Resource` класс, создание вложенного класса с именем `Drawable` с постоянным inside с именем `Icon`.
Это позволяет файл **Icon.png** ссылаться в коде в виде `Resource.Drawable.Icon`. `Resource` Класса не следует изменять вручную, как любые изменения, внесенные в него будут перезаписаны Xamarin.Android.

При указании ссылки на ресурсы программно (в коде), они доступны через иерархии классов ресурсов, который использует следующий синтаксис:

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **Имя пакета** &ndash; требуется пакет, который предоставляет ресурс и только в том случае, если используются ресурсы из других пакетов.

-  **ResourceType** &ndash; это тип вложенного ресурса, который находится в пределах класса ресурсов, описанных выше.

-  **Имя ресурса** &ndash; это имя файла ресурсов (без расширения) или значение атрибута android: name для ресурсов, которые находятся в XML-элемента.


## <a name="referencing-resources-from-xml"></a>Создание ссылок на ресурсы из XML

Доступ к нужным ресурсам в XML-файл следующий специальный синтаксис:

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **Имя пакета** &ndash; требуется пакет, который предоставляет ресурс и только в том случае, если используются ресурсы из других пакетов.

-  **ResourceType** &ndash; это тип вложенного ресурса, который находится в пределах класса ресурсов.

-  **Имя ресурса** &ndash; это имя файла ресурса (*без* файлы с расширением) или значение `android:name` атрибут для ресурсов, которые находятся в XML-элемента.

Например содержимое файла макета, **Main.axml**, таковы:

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

В этом примере имеется [ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) требующее drawable ресурса с именем **флаг**. `ImageView` Имеет его `src` атрибут **@drawable/flag**. В начале действия Android будет выглядеть в каталоге **ресурсов/Drawable** откроется файл с именем **flag.png** (расширение файла может быть другой формат изображения, например **flag.jpg**) загрузить этот файл и отобразить ее в `ImageView`.
При запуске этого приложения оно будет выглядеть примерно как на следующем изображении:

![Локализованные ImageView](android-resource-basics-images/03-localized-screenshot.png)

