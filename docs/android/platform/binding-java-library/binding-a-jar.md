---
title: Привязка. JAR-ФАЙЛ
description: В этом пошаговом руководстве содержатся пошаговые инструкции по созданию библиотеку привязок Java Xamarin.Android из Android. JAR-файл.
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120012"
---
# <a name="binding-a-jar"></a>Привязка. JAR-ФАЙЛ

_В этом пошаговом руководстве содержатся пошаговые инструкции по созданию библиотеку привязок Java Xamarin.Android из Android. JAR-файл._

## <a name="overview"></a>Обзор

Android сообщества предлагает множество библиотек Java, которые можно использовать в приложении. Эти библиотеки Java часто упаковываются в. Формат JAR-ФАЙЛ (архив Java), но вы можете упаковать. JAR-ФАЙЛ в *библиотека Java Bindings* , чтобы его функциональность доступна для приложений Xamarin.Android. Библиотека привязок Java предназначена для интерфейсов API в. JAR-файл для C# кода с помощью автоматически созданного кода оболочки.

Средства Xamarin можно создать библиотеку привязок один или несколько входных данных. JAR-файлы. Библиотека привязок (. Сборка библиотеки DLL) содержит следующие элементы: 

-   Содержимое исходного объекта. JAR-файлы.

-   Управляемые вызываемой оболочки (MCW), которые являются C# типы, которые упаковывают типов в соответствующих Java. JAR-файлы.

Созданный код MCW использует JNI (Java Native Interface) для перенаправления вызовов API к базовому объекту. JAR-файл. Библиотеки привязки можно создать для любого. JAR-файл, отличного от целевого для использования с Android (Обратите внимание, что средства Xamarin не поддерживает привязку библиотеки не Android Java). Вы также можете выбрать для создания библиотека привязок, не включая содержимое. JAR-файл, чтобы библиотеки DLL имеет зависимость от. JAR-файла во время выполнения.

В этом руководстве пройдем через основ создания библиотека привязок для одного. JAR-файл. Показано на примере, где все пойдет правой &ndash; где отладки привязок или настройка не является обязательным. 
[Создание метаданных с помощью привязки](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) предлагает пример более сложным сценарием, где процесс привязки не полностью в автоматическом и некоторые объема ручного вмешательства является обязательным. Общие сведения о Java библиотеки в целом привязки (с примера базовый код), см. в разделе [привязка библиотеки Java](~/android/platform/binding-java-library/index.md). 

 
## <a name="walkthrough"></a>Пошаговое руководство

В следующем примере мы создадим библиотеку привязок для [Пикассо](http://square.github.io/picasso/), популярных Android. JAR-ФАЙЛ, который содержит образ загрузки и возможности кэширования. Мы используем следующие действия для привязки **Пикассо 2.x.x.jar** для создания новой сборки .NET, можно использовать в проекте Xamarin.Android: 

1. Создайте новый проект библиотеки привязок Java.

2. Добавить. JAR-файл в проект.

3. Задайте действие, соответствующее построение. JAR-файл.

4. Выбор целевой платформы,. Поддерживает JAR-ФАЙЛ.

5. Построить библиотеку привязок.

После того как мы создали библиотека привязок, мы создам небольшое приложение Android, демонстрирующий наши возможности вызова API-интерфейсы в библиотеке привязки. В этом примере мы хотим получить доступ к методам из **Пикассо 2.x.x.jar**:

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
```

Мы создали библиотеку привязок для внесли **Пикассо 2.x.x.jar**, мы можем вызвать эти методы из C#. Пример:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>Создание библиотеки привязки

Перед началом следующие действия, загрузите [Пикассо 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar).

Во-первых создайте новый проект библиотеки привязки. В Visual Studio для Mac или Visual Studio создайте новое решение и выберите *Android библиотека привязок* шаблона. (Снимки экрана в этом пошаговом руководстве с помощью Visual Studio, но Visual Studio для Mac очень похож.) Назовите решение **JarBinding**: 

[![Создайте проект библиотеки JarBinding](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

В шаблоне есть **Jars** папку, где добавить ваш. JAR(s) на проект библиотеки привязки. Щелкните правой кнопкой мыши **Jars** папку и выберите **Добавить > существующий элемент**: 

[![Добавление существующего элемента](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

Перейдите к **Пикассо 2.x.x.jar** файл, загруженный ранее, выберите его и нажмите кнопку **добавить**: 

[![Выберите JAR-файл и щелкните "Добавить"](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

Убедитесь, что **Пикассо 2.x.x.jar** файл был успешно добавлен в проект: 

[![Добавить проект JAR-файл](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

При создании проекта библиотеку привязок Java, необходимо указать ли. JAR-ФАЙЛ находится в том, чтобы быть внедрены в библиотеке привязки или представлено отдельным пакетом. Чтобы сделать это, необходимо указать одно из следующих *действия при сборке*: 

-   **EmbeddedJar** &ndash; . JAR-ФАЙЛ будет встроен в библиотеку привязок.

-   **InputJar** &ndash; . JAR-ФАЙЛ будет храниться отдельно от библиотека привязок.

Как правило, используется **EmbeddedJar** действие при сборке, чтобы. JAR-ФАЙЛ автоматически упакован в библиотеку привязок. Это самый простой вариант &ndash; байт-кода Java в. JAR-ФАЙЛ преобразуется в Dex байт-кода и внедренный в пакет APK (вместе с управляемых вызываемых оболочек). Если вы хотите сохранить. JAR-файла отдельно от библиотека привязок, можно использовать **InputJar** параметр; тем не менее, необходимо убедиться, что. JAR-файл можно найти в устройства, которое запускает приложение. 

Задайте действие сборки **EmbeddedJar**: 

[![Выберите действие сборки EmbeddedJar](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

Далее, откройте свойства для настройки проекта *требуемой версии .NET Framework*. Если. JAR-ФАЙЛ использует интерфейсы API Android, задать целевую платформу на уровне API. Ожидает JAR-ФАЙЛ. Как правило, разработчик. JAR-файл сообщит, какой уровень API (или уровней),. JAR-ФАЙЛ совместим с. (Дополнительные сведения о настройках требуемой версии .NET Framework и уровнях Android API в целом, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).)

Задание целевой API уровня для привязки библиотеки (в этом примере мы используем API уровня 19): 

[![Целевой API уровень API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


Наконец создайте библиотеку привязок. Несмотря на то, что некоторые предупреждения могут отображаться, проект библиотеки привязки следует построить успешно и выдает результат. Библиотеки DLL по следующему адресу: **JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>С помощью библиотеки привязки

Чтобы их использовать. Библиотеки DLL в приложение Xamarin.Android, сделайте следующее:

1.  Добавьте ссылку на библиотеку привязок.

2.  Вызвать службу. JAR-ФАЙЛ через управляемый вызываемых оболочек. 

В следующих шагах мы создадим минимальное приложение, использующее библиотеку привязок для скачивания и отображения изображения в `ImageView`; «тяжелая работа» выполняется кодом, который находится в. JAR-файл. 

Во-первых создайте новое приложение Xamarin.Android, которое использует библиотеку привязок. Щелкните правой кнопкой мыши решение и выберите **Добавление нового проекта**; назовите новый проект **BindingTest**. Мы создаем это приложение в том же решении, что библиотека привязок для упрощения в этом пошаговом руководстве; Однако приложение, использующее библиотеку привязок, вместо этого находятся в другом решении: 

[![Добавление нового проекта BindingTest](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

Щелкните правой кнопкой мыши **ссылки** узел **BindingTest** проекта и выберите **добавить ссылку...** :

[![Прямо добавить ссылку](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

Проверьте **JarBinding** проект, созданный ранее и нажмите кнопку **ОК**:

[![Выберите проект JarBinding](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

Откройте **ссылки** узел **BindingTest** проекта и убедитесь, что **JarBinding** присутствует ссылка: 

[![JarBinding появится в списке ссылок](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

Изменить **BindingTest** макета (**Main.axml**), чтобы он имеет единственный `ImageView`:

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

Добавьте следующий `using` инструкцию, чтобы **MainActivity.cs** &ndash; это позволяет легко обращаться к методам, основанного на Java `Picasso` класс, который находится в библиотеке привязки:

```csharp
using Com.Squareup.Picasso;
```

Изменить `OnCreate` метод использования `Picasso` класс для загрузки изображения из URL-адрес и отобразить ее в `ImageView`: 

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

Скомпилируйте и запустите **BindingTest** проекта. Приложение будет запущено, и после короткой задержки (в зависимости от состояния сети), его необходимо загрузить и отобразить изображение как на следующем снимке экрана:

[![Снимок экрана из BindingTest запущена](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

Поздравляем! Был успешно присоединен библиотеки Java. JAR-ФАЙЛ и использовать его в приложение Xamarin.Android.
 
 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы создали библиотеку привязок для независимых производителей. JAR-файл, добавлены в библиотеку привязок минимальной тестовое приложение со, а затем запустили приложение, чтобы убедиться, что наши C# код может вызвать код Java, которая находится в. JAR-файл. 



## <a name="related-links"></a>Связанные ссылки

- [Создание библиотеки привязок Java (видео)](https://university.xamarin.com/classes#10090)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
