---
title: Начало работы с Android
description: В этом документе описывается, как приступить к использованию внедрение .NET с Android. В нем описывается установка внедрение .NET, Создание проекта библиотеки Android, используя созданные выходные данные в проекте Android Studio и других факторов.
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: e60892edfcf73f3e7cada8923e16bcc1be2c203e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121390"
---
# <a name="getting-started-with-android"></a>Начало работы с Android

В дополнение к требованиям, предъявляемым [Приступая к работе с Java](~/tools/dotnet-embedding/get-started/java/index.md) руководства, вам также потребуется:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/) или более поздней версии
* [Android Studio 3.x](https://developer.android.com/studio/index.html) с Java 1.8

Как обзор происходит следующее:

* Создание C# проект библиотеки Android
* Установка с помощью NuGet внедрение .NET
* Выполните внедрение .NET в сборке библиотеки Android
* Используйте созданный файл AAR в проекте Java в Android Studio

## <a name="create-an-android-library-project"></a>Создайте проект библиотеки Android

Откройте Visual Studio для Windows или Mac, создайте новый проект библиотеки классов Android, назовите его **hello из c#** и сохраните его для **~/Projects/hello-from-csharp** или **%USERPROFILE%\ Projects\hello из c#**.

Добавьте новое действие Android с именем **HelloActivity.cs**, а затем макет Android в **Resource/layout/hello.axml**.

Добавьте новый `TextView` макет и изменение текста на что-нибудь разработок.

Источник макет должен выглядеть следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

В действии, убедитесь, что при вызове `SetContentView` с новому макету:

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> Не забывайте `[Register]` атрибута. Дополнительные сведения см. в разделе [ограничения](#current-limitations-on-android).

Выполните построение проекта. Итоговой сборки будут сохранены в `bin/Debug/hello-from-csharp.dll`.

## <a name="installing-net-embedding-from-nuget"></a>Установка внедрение .NET из NuGet

Следуйте этим [инструкции](~/tools/dotnet-embedding/get-started/install/install.md) для установки и настройки внедрение .NET для вашего проекта.

Вызова команды, которые нужно настроить будет выглядеть следующим образом:

### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>Использовать созданные выходные данные в проекте Android Studio

1. Откройте Android Studio и создайте новый проект с **пустое действие**.
2. Щелкните правой кнопкой мыши ваш **приложения** модуль и выберите **New > модуль**.
3. Выберите **импорта. JAR /. Пакет AAR**.
4. Найдите с помощью обозревателя directory **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** и нажмите кнопку **Готово**.

![Импорт AAR в Android Studio](android-images/androidstudioimport.png)

Эта команда скопирует файл AAR в новый модуль с именем **hello_from_csharp**.

![Проект Android Studio](android-images/androidstudioproject.png)

Чтобы использовать новый модуль из вашей **приложения**, щелкните правой кнопкой мыши и выберите пункт **открыть параметры модуля**. На **зависимости** вкладку, добавьте новый **зависимостей модуля** и выберите **: hello_from_csharp**.

![Android Studio зависимостей](android-images/androidstudiodependencies.png)

В действии, добавьте новый `onResume` метод и используйте следующий код для запуска C# действия:

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>Сжатие сборки (*важно*)

Для внедрение .NET в проект Android Studio, необходим один дальнейших изменениях.

Откройте в нем **build.gradle** файл и добавьте следующее изменение:

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin.Android сейчас загружает сборки .NET непосредственно из пакета APK, но он требует сборки не будет сжат.

Если вы не настроили, приложение о сбоях при запуске и на консоль выводятся примерно следующее:

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>Запуск приложения

После запуска приложения:

![Hello из C# пример, в эмуляторе](android-images/hello-from-csharp-android.png)

Обратите внимание на то, что произошло здесь:

* У нас есть C# класс, `HelloActivity`, Java, подклассы
* У нас есть файлы ресурсов Android
* Мы использовали их из Java в Android Studio

Для этого примера для работы всех следующих настраиваются в конечного пакета APK:

* Xamarin.Android настраивается на запуск приложения
* Сборки .NET, включенные в **активы/сборок**
* **AndroidManifest.xml** изменений для вашей C# действия и т. д.
* Android ресурсы и ресурсы из библиотеки .NET
* [Вызываемые оболочки времени Android](~/android/platform/java-integration/android-callable-wrappers.md) для любого `Java.Lang.Object` подкласс

Если вы ищете дополнительным пошаговым руководством, ознакомьтесь с следующий видеоролик, который показывает, внедрение Чарльз Петцольд [FingerPaint Демонстрация](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/) в проекте Android Studio:

[![Embeddinator до 4000 для Android](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>С помощью Java 1.8

На момент написания этой статьи, лучше всего использовать Android Studio 3.0 ([загрузить здесь](https://developer.android.com/studio/index.html)).

Чтобы включить Java 1.8 в модуль приложения **build.gradle** файла:

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

Вы также можете рассмотреть [тестовый проект Android Studio](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) для получения дополнительных сведений. 

Если вы хотите использовать стабильную 2.3.x Android Studio, необходимо включить устаревшие Джек цепочки инструментов:

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Текущие ограничения в Android

Прямо сейчас, если вы подкласс `Java.Lang.Object`, Xamarin.Android создаст заглушку Java (Android вызываемую оболочку) вместо внедрения .NET. По этой причине необходимо выполнить те же правила для экспорта C# для Java как Xamarin.Android. Пример:

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

* `[Register]` необходимо сопоставить с требуемой именем пакета Java
* `[Export]` необходимо, чтобы сделать метод видимым для Java

Мы можем использовать `ViewSubclass` в Java следующим образом:

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

Дополнительные сведения о [интеграции Java с помощью Xamarin.Android](~/android/platform/java-integration/index.md).

## <a name="multiple-assemblies"></a>Несколько сборок

Внедрение в одну сборку прост; Однако это гораздо более вероятно, будет иметь более одного C# сборки. Много раз будет иметь зависимости от пакетов NuGet, таких как библиотеки поддержки Android или сервисы Google Play, дальнейшей усложняют ситуацию.

Это приводит к дилемму, так как внедрение .NET должен включать множество типов файлов в окончательный AAR, такие как:

* Ресурсов Android
* Ресурсы Android
* Android собственные библиотеки
* Источник java, Android

Скорее всего вы хотите включить эти файлы из библиотеки поддержки Android или сервисы Google Play в вашей AAR, но предпочитаете использовать в Android Studio официальной версии от Google.

Ниже приведен рекомендуемый подход.

* Передайте внедрение .NET любую сборку, вы являетесь владельцем (иметь источник) и хотите вызвать из Java
* Передайте внедрение .NET любую сборку, необходимо Android, собственные библиотеки и ресурсы из
* Добавление зависимостей Java, таких как Android поддерживает библиотеку или сервисы Google Play в Android Studio

Поэтому команда может быть:

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

Что-то следует исключить из NuGet, если только вы узнаете, он содержит ресурсов Android "," ресурсы "," в проект Android Studio и т. д. Можно также опустить зависимости, которые необходимо вызвать из Java и компоновщик _следует_ включать разделы библиотеки, которые необходимы.

Чтобы добавить зависимые компоненты Java в Android Studio вашего **build.gradle** файл может выглядеть так:

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>Дополнительные сведения

* [Обратные вызовы на устройстве Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Предварительное исследование Android](~/tools/dotnet-embedding/android/index.md)
* [Ограничения внедрение .NET](~/tools/dotnet-embedding/limitations.md)
* [Вносящая вклад в проект с открытым кодом](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Коды ошибок и описания](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>Связанные ссылки

- [Пример погоды (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
