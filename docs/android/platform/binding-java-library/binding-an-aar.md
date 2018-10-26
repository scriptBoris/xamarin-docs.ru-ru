---
title: Привязка. AAR
description: В этом пошаговом руководстве содержатся пошаговые инструкции по созданию библиотеку привязок Java Xamarin.Android из Android. AAR-файла.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 7f71ccf4ff61c176e73be6d3855136697a5c2130
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104002"
---
# <a name="binding-an-aar"></a>Привязка. AAR

_В этом пошаговом руководстве содержатся пошаговые инструкции по созданию библиотеку привязок Java Xamarin.Android из Android. AAR-файла._


## <a name="overview"></a>Обзор

*Архив Android (. AAR)* файл имеет формат файла для библиотеки Android.
. AAR-файл. ZIP-архив, который содержит следующее:

-   Скомпилированный код Java
-   Идентификаторы ресурсов
-   Ресурсы
-   Метаданные (например, действия объявления, разрешения)

В этом руководстве пройдем через основ создания библиотека привязок для одного. AAR-файла. Общие сведения о Java библиотеки в целом привязки (с примера базовый код), см. в разделе [привязка библиотеки Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Привязка проекта может содержать только один. AAR-файла. Если. AAR зависимостей от других. AAR, а затем эти зависимости необходимо содержащихся в своих собственных проектах привязки и затем ссылаться. См. в разделе [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Пошаговое руководство

Мы создадим библиотеку привязок, для примера файла архив Android, который был создан в Android Studio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Это. Содержит AAR `TextCounter` класс со статическими методами, которые подсчитывают количество гласные и согласные буквы в строке. Кроме того **textanalyzer.aar** содержит ресурс изображения для отображения результатов инвентаризации.

Мы будем использовать следующие действия, чтобы создать библиотеку привязок из. AAR-файла.

1. Создайте новый проект библиотеки привязок Java.

2. Добавьте один. AAR-файл в проект. Привязка проекта может содержать только один. AAR.

3. Задайте действие, соответствующее построение. AAR-файла.

4. Выбор целевой платформы,. Поддерживает AAR.

5. Построить библиотеку привязок.

После того как мы создали библиотека привязок, мы выработаете небольшой приложение Android, которое запрашивает у пользователя строку текста, вызовы. AAR методы для анализа текста, получает образ из. AAR и отображает результаты вместе с изображением.

Пример приложения получит доступ к `TextCounter` класс **textanalyzer.aar**:

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

Кроме того, этот пример приложения будет извлекать и отображать ресурс с изображением, содержащийся в **textanalyzer.aar**:

[![Изображение monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Этот ресурс изображения находится в **res/drawable/monkey.png** в **textanalyzer.aar**.



### <a name="creating-the-bindings-library"></a>Создание библиотеки привязки

Перед началом следующие действия, загрузите пример [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android архивного файла:

1.  Создайте новый проект библиотеки привязок, начиная с Android библиотека привязок шаблона. Можно использовать Visual Studio для Mac или Visual Studio (ниже снимках экрана, Visual Studio, но Visual Studio для Mac во многом схожа). Назовите решение **AarBinding**:

    [![Создание проекта AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2.  В шаблоне есть **Jars** папку, где добавить ваш. AAR(s) на проект библиотеки привязки. Щелкните правой кнопкой мыши **Jars** папку и выберите **Добавить > существующий элемент**:

    [![Добавление существующего элемента](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Перейдите к **textanalyzer.aar** файл, загруженный ранее, выберите его и нажмите кнопку **добавить**:

    [![Добавить textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Убедитесь, что **textanalyzer.aar** файл был успешно добавлен в проект:

    [![Файл textanalyzer.aar был добавлен](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Задайте действие сборки для **textanalyzer.aar** для `LibraryProjectZip`. В Visual Studio для Mac, щелкните правой кнопкой мыши **textanalyzer.aar** задать действие сборки. Действие при построении в Visual Studio можно задать в **свойства** область):

    [![Указав действия построения textanalyzer.aar LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Откройте проект свойства для настройки *требуемой версии .NET Framework*. Если. AAR использует интерфейсы API Android, задать целевую платформу на уровне API. Ожидает AAR. (Дополнительные сведения о настройках требуемой версии .NET Framework и уровнях Android API в целом, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).)

    Задайте целевому уровню API для библиотеки привязки. В этом примере мы можете использовать последнюю версию платформы уровень API (уровень API 23), так как наш **textanalyzer** не имеет зависимость на Android API-интерфейсы:

    [![Задать целевой уровень API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Построить библиотеку привязок. Библиотека привязок проекта следует успешной сборки и выдает результат. Библиотеки DLL по следующему адресу: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>С помощью библиотеки привязки

Чтобы их использовать. Библиотеки DLL в приложение Xamarin.Android, необходимо сначала добавить ссылку на библиотеку привязок. Выполните следующие действия:

1.  В том же решении, что библиотека привязок для упрощения в этом пошаговом руководстве мы создаем это приложение. (Приложение, использующее библиотеку привязок может также находиться в другом решении.) Создание нового приложения Xamarin.Android: щелкните правой кнопкой мыши решение и выберите **Добавление нового проекта**. Назовите новый проект **BindingTest**:

    [![Создание нового проекта BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2.  Щелкните правой кнопкой мыши **ссылки** узел **BindingTest** проекта и выберите **добавить ссылку...** :

    [![Нажмите кнопку Добавить ссылку](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Выберите **AarBinding** проект, созданный ранее и нажмите кнопку **ОК**:

    [![Проверить проект привязки AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Откройте **ссылки** узел **BindingTest** проекта, чтобы убедиться, что **AarBinding** присутствует ссылка:

    [![AarBinding находится в списке ссылок](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Если вы хотите просмотреть содержимое проекта библиотеки привязки, можно дважды щелкнуть ссылку, чтобы открыть его в **обозреватель объектов**. Вы увидите содержимое сопоставленной `Com.Xamarin.Textcounter` пространства имен (, полученного из Java `com.xamarin.textanalyzezr` пакета) и вы сможете просмотреть членов `TextCounter` класса:

[![Просмотр обозревателя объектов](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

Приведенном выше снимке экрана представлены два `TextAnalyzer` методы, которые будут вызывать примера приложения: `NumConsonants` (оболочку для базовой Java `numConsonants` метод), и `NumVowels` (оболочку для базовой Java `numVowels` метод).



### <a name="accessing-aar-types"></a>Доступ к. Типы AAR

Когда вы добавите ссылку на приложение, которое указывает на библиотеку привязки, доступны типы Java в. Доступ к AAR, как вы C# типы (выражаем благодарность C# оболочки). C#код приложения может вызвать `TextAnalyzer` методы, как показано в этом примере:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

В приведенном выше примере мы звоним по номеру статические методы `TextCounter` класса. Тем не менее можно также создавать экземпляры классов и вызывать методы экземпляра. Например если ваш. AAR заключает в оболочку класс с именем `Employee` , имеет метод экземпляра `buildFullName`, можно создать экземпляр `MyClass` и использовать его как показано здесь:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

Следующие действия для добавления кода в приложение таким образом, чтобы он запрашивает у пользователя текст, использует `TextCounter` для анализа текста, а затем отображает результаты.

Замените **BindingTest** макета (**Main.axml**) следующим кодом XML. Этот макет содержит `EditText` для ввода текста и две кнопки для инициирования счетчиков гласные и согласной:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

Замените содержимое файла **MainActivity.cs** следующим кодом. Как показано в этом примере, в оболочку вызова обработчиков событий кнопки `TextCounter` методы, которые находятся в. AAR и использование всплывающие уведомления для отображения результатов. Обратите внимание, что `using` оператор для пространства имен привязки библиотеки (в этом случае `Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

Скомпилируйте и запустите **BindingTest** проекта. Приложение запустится и представления в левой части экрана ( `EditText` инициализируется с определенным текстом, но вы можете коснуться его, чтобы изменить его). При нажатии элемента **ГЛАСНЫЕ число**, всплывающее уведомление показывает число гласные, как показано в правой части:

[![Снимки экрана с управлением BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Коснитесь **число СОГЛАСНЫХ** кнопки. Кроме того можно изменить эту строку текста и используйте эти кнопки, еще раз, чтобы проверить различные гласные и подсчитывает согласных.



### <a name="accessing-aar-resources"></a>Доступ к. AAR ресурсы

Xamarin, средств слияний **R** данные. AAR в ваше приложение **ресурсов** класса. Таким образом чтобы открыть. AAR ресурсы из макета (и с выделенным кодом) в так же, как доступ к ресурсам, которые находятся в **ресурсы** путь проекта.

Чтобы получить доступ к ресурс изображения, используйте **Resource.Drawable** имя для образа упакованы внутри. AAR. Например, вы можете ссылаться на **image.png** в. AAR-файла с помощью `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

Также можно использовать макеты ресурсов, которые находятся в. AAR. Чтобы сделать это, используйте **Resource.Layout** имя макета из пакета. AAR. Пример:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer.aar** пример содержит файл изображения, которая размещается в **res/drawable/monkey.png**. Давайте доступа к этому ресурсу образ и использовать его в нашем примере приложения:

Изменить **BindingTest** макета (**Main.axml**) и добавьте `ImageView` в конец `LinearLayout` контейнера. Это `ImageView` отображает изображение, расположенный **@drawable/monkey**; этот образ будет загружен из раздела ресурсов **textanalyzer.aar**:

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

Скомпилируйте и запустите **BindingTest** проекта. Приложение запустится и представления в левой части экрана &ndash; при нажатии элемента **СОГЛАСНЫХ число**, результаты отображаются в том случае, как показано в правой части:

[![Отображение числа согласных BindingTest](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


Поздравляем! Был успешно присоединен библиотеки Java. AAR!



## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы создали библиотеку привязок для. Файл AAR, добавлена библиотека привязок к минимальным тестовое приложение со и запустили приложение, чтобы убедиться, что наши C# код может вызвать код Java, которая находится в. AAR-файла.
Кроме того, мы расширили приложения для доступа и отображения ресурс изображения, который находится в. AAR-файла.


## <a name="related-links"></a>Связанные ссылки

- [Создание библиотеки привязок Java (видео)](https://university.xamarin.com/classes#10090)
- [Привязка JAR-файла](~/android/platform/binding-java-library/binding-a-jar.md)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (пример)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Ошибки 44573 один проект не удается привязать несколько aar-файлы](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
