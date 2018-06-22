---
title: Привязка. JAR-ФАЙЛ
description: Это пошаговое руководство содержит пошаговые инструкции для создания библиотеки привязок Java Xamarin.Android из Android. JAR-файл.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/11/2018
ms.openlocfilehash: 2d9f2198dbb88e7614944ac73729a4e6eca42647
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798383"
---
# <a name="binding-a-jar"></a>Привязка. JAR-ФАЙЛ

_Это пошаговое руководство содержит пошаговые инструкции для создания библиотеки привязок Java Xamarin.Android из Android. JAR-файл._

## <a name="overview"></a>Обзор

Android сообщества предлагает множество библиотек Java, которые можно использовать в приложении. Эти библиотеки Java часто упакованы в. Формат JAR (архив Java), но вы можете упаковать. JAR в *библиотеки привязок Java* , чтобы его функциональность доступна в Xamarin.Android приложения. Библиотека привязок Java предназначена для API в. JAR-файл доступным для кода C#, через код, автоматически созданный оболочки.

Средства Xamarin можно создавать библиотеку привязок из одного или нескольких входных данных. JAR-файлы. Библиотека привязок (. DLL-сборки) содержит следующие элементы: 

-   Содержимое исходного. JAR-файлы.

-   Которые упаковывают Java соответствующих типов в типы управляемых вызываемой оболочки (MCW), которые входят в C#. JAR-файлы.

Созданный код MCW использует JNI (Java Native Interface) для перенаправления вызовов API к базовому объекту. JAR-файл. Можно создавать библиотеки привязок для любых. JAR-файл, первоначально была предназначена для использования с Android (Обратите внимание, что инструменты Xamarin сейчас не поддерживает привязку библиотеки не Android Java). Вы можете также для построения библиотеки привязок без включения содержимого. JAR-файл, чтобы библиотеки DLL имеет зависимость от. JAR во время выполнения.

В этом руководстве мы шаг по основам создания библиотеки привязок одного и того же. JAR-файл. Мы опишем с примером, где все пойдет правой &ndash; где без настройки или отладка привязок является обязательным. 
[Создание привязки с использованием метаданных](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) приведен пример более сложных сценариев, когда процесс привязки не является полностью автоматически и некоторые объема ручного вмешательства не требуется. Обзор Java библиотеки привязки, в целом (с основной код примера), в разделе [привязки библиотеки Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Пошаговое руководство

В этом пошаговом руководстве мы создадим библиотеку привязок для [Picasso](http://square.github.io/picasso/), популярных Android. JAR-ФАЙЛ, предоставляющий изображение, загрузка и кэширование функциональные возможности. Мы будем использовать следующие шаги для привязки **picasso 2.x.x.jar** для создания новой сборки .NET, можно использовать в проекте Xamarin.Android: 

1. Создайте новый проект библиотеки привязок Java.

2. Добавить. JAR-файл в проект.

3. Задать действие соответствующие сборки для. JAR-файл.

4. Выбор целевой платформы,. Поддерживает JAR-ФАЙЛ.

5. Построение библиотеки привязок.

После того как мы создали библиотеки привязок, мы выработаете небольшого приложения Android, демонстрирующий нашей возможность вызова API-интерфейсов в библиотеке привязок. В этом примере мы хотим получить доступ к методам **picasso 2.x.x.jar**:

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}

```csharp
After we generate a Bindings Library for **picasso-2.x.x.jar**,
we can call these methods from C#. For example:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Создание библиотеки привязок

Перед началом следующие действия, загрузите [picasso 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Во-первых создайте новый проект библиотеки привязок. В Visual Studio для Mac или Visual Studio создайте новое решение и выберите *библиотеки Android привязки* шаблона. (В этом пошаговом руководстве снимки экрана с помощью Visual Studio, но Visual Studio для Mac очень похожа.) Присвойте решению имя **JarBinding**: 

[![Создание проекта библиотеки JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

Шаблон включает **JAR-файлов** папки, которые добавляются к. JAR(s) на проект библиотеки привязок. Щелкните правой кнопкой мыши **JAR-файлов** папку и выберите **Добавить > существующий элемент**: 

[![Добавление существующего элемента](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Перейдите к **picasso 2.x.x.jar** ранее загрузить файл, выберите его и нажмите кнопку **добавить**: 

[![Выберите jar-файл и нажмите кнопку Добавить](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Убедитесь, что **picasso 2.x.x.jar** файл был успешно добавлен в проект: 

[![Добавить проект JAR](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

При создании проекта библиотеки Java привязок, необходимо указать ли. JAR должен быть внедрены в библиотеке привязки или в отдельные пакеты. Чтобы сделать это, необходимо указать одно из следующих *построения действий*: 

-   **EmbeddedJar** &ndash; . JAR-ФАЙЛ будет внедрен в библиотеке привязок.

-   **InputJar** &ndash; . JAR-ФАЙЛ будет храниться отдельно от библиотеки привязок.

Как правило, используется **EmbeddedJar** действие построения, чтобы. JAR автоматически упакованы в библиотеке привязок. Это самый простой вариант &ndash; байт-код Java в. JAR-ФАЙЛ преобразуется в Dex байт-код и внедряется (вместе с вызываемой оболочки управляемого) в вашей APK. Если вы хотите сохранить. JAR отдельно от библиотеки привязок, можно использовать **InputJar** параметр; тем не менее, необходимо убедиться, что. JAR-файл доступен на устройства, которое запускает приложение. 

Задайте действие построения **EmbeddedJar**: 

[![Выберите действие построения EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Затем откройте свойства для настройки проекта *требуемой версии .NET Framework*. Если. JAR-ФАЙЛ использует интерфейсы API Android, задать целевую платформу на уровне API. Ожидает JAR-ФАЙЛ. Как правило, разработчик. JAR-файл будет указывать, какие API уровня (или уровней),. JAR совместим с. (Дополнительные сведения о параметре целевой платформы и Android API уровней в целом см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).)

Задание целевого объекта API уровня для привязки библиотеки (в этом примере мы используем уровень API 19): 

[![Целевой API уровень API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Наконец создайте библиотеку привязок. Несмотря на то, что некоторые предупреждения могут отображаться, в проект библиотеки привязок следует построить успешно и результат. Библиотеки DLL в следующем расположении: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>С помощью библиотеки привязок

Чтобы использовать это. Библиотеки DLL в приложении Xamarin.Android, выполните следующее:

1.  Добавьте ссылку на библиотеку привязок.

2.  Выполнять вызовы. JAR через управляемый вызываемых оболочек. 

В следующих шагах мы создадим минимальной приложение, которое использует библиотеку привязок для загрузки и отображения изображения в `ImageView`; «тяжелой работы» выполняется кодом, который находится в. JAR-файл. 

Во-первых создайте новое Xamarin.Android приложение, использующее библиотеку привязок. Щелкните правой кнопкой мыши решение и выберите **Добавление нового проекта**; для нового проекта имя **BindingTest**. Мы создаем это приложение в том же решении, что библиотека привязок для упрощения этого пошагового руководства; Однако приложение, использующее библиотеку привязок вместо этого находятся в другом решении: 

[![Добавление нового проекта BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Щелкните правой кнопкой мыши **ссылки** узел **BindingTest** проект и выберите **добавить ссылку...** :

[![Добавить ссылку справа](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Проверьте **JarBinding** проект, созданный ранее и нажмите кнопку **ОК**:

[![Выберите проект JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Откройте **ссылки** узел **BindingTest** проекта и убедитесь, что **JarBinding** присутствует ссылка: 

[![JarBinding появится в списке ссылок](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Изменить **BindingTest** макета (**Main.axml**), чтобы он имел один `ImageView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

Добавьте следующие `using` инструкции **MainActivity.cs** &ndash; это позволяет легко обращаться к методам, основанного на Java `Picasso` класс, который находится в библиотеке привязки:

```csharp
using Com.Squareup.Picasso;
```

Изменить `OnCreate` метод использования `Picasso` класс, чтобы загрузить изображение из URL-адреса и отобразит в `ImageView`: 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

Компиляция и выполнение **BindingTest** проекта. Приложение будет запущено и после небольшой задержки (в зависимости от состояния сети), его необходимо загрузить и выводит изображение, аналогичные следующим образом:

[![Снимок экрана BindingTest запущена](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Поздравляем! Был успешно присоединен библиотеки Java. JAR и использовать его в приложении Xamarin.Android.
 
 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы создали библиотеку привязок для сторонних разработчиков. JAR-файл, добавленный библиотеки привязок минимальной тестовое приложение и запустить приложение, чтобы убедиться, вызова кода Java, хранящегося в наш код C#. JAR-файл. 



## <a name="related-links"></a>Связанные ссылки

- [Библиотека привязки создается Java (видео)](https://university.xamarin.com/classes#10090)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
