---
title: Привязка. AAR
description: Это пошаговое руководство содержит пошаговые инструкции для создания библиотеки привязок Java Xamarin.Android из Android. AAR-файл.
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/11/2018
ms.openlocfilehash: 54708a7cfd071f77968991c9fe4e52938697c9bb
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798711"
---
# <a name="binding-an-aar"></a>Привязка. AAR

_Это пошаговое руководство содержит пошаговые инструкции для создания библиотеки привязок Java Xamarin.Android из Android. AAR-файл._


## <a name="overview"></a>Обзор

*Android архива (. AAR)* файл имеет формат файла для библиотеки Android.
. AAR-файл. ZIP-архив, который содержит следующее:

-   Скомпилированный код Java
-   Идентификаторы ресурсов
-   Ресурсы
-   Метаданные (например, действие объявления, разрешения)

В этом руководстве мы шаг по основам создания библиотеки привязок одного и того же. AAR-файл. Обзор Java библиотеки привязки, в целом (с основной код примера), в разделе [привязки библиотеки Java](~/android/platform/binding-java-library/index.md).


> [!IMPORTANT]
> Проект привязки могут содержать только один. AAR-файл. Если. AAR зависимостей от других. AAR, а затем эти зависимости необходимо содержащихся в отдельном проекте привязки и затем ссылаться на него. В разделе [ошибки 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573).


## <a name="walkthrough"></a>Пошаговое руководство

Мы создадим библиотеку привязок для примера Android архивный файл, который был создан в Android Studio [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). Это. Содержит AAR `TextCounter` класса со статическими методами, число гласные и согласные буквы в строке. Кроме того **textanalyzer.aar** содержит ресурс изображения, чтобы отобразить результаты подсчета.

Мы будем использовать следующие шаги для создания библиотеки из привязок. Файл AAR:

1. Создайте новый проект библиотеки привязок Java.

2. Добавьте один. AAR-файл в проект. Проект привязки может содержать только один. AAR.

3. Задать действие соответствующие сборки для. AAR-файл.

4. Выбор целевой платформы,. Поддерживает AAR.

5. Построение библиотеки привязок.

После того как мы создали библиотеки привязок, мы выработаете небольшое приложение Android, которое запрашивает у пользователя для текстовой строки, вызовы. AAR методы для анализа текста, загружает изображение из. AAR и отображает результаты в вместе с образом.

Пример приложения будет обращаться к `TextCounter` класс **textanalyzer.aar**:

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

Кроме того, приложение из этого примера будет извлекать и отображать ресурс изображения, содержащийся в **textanalyzer.aar**:

[![Изображение monkey Xamarin](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

Этот ресурс изображения находится в **res/drawable/monkey.png** в **textanalyzer.aar**.



### <a name="creating-the-bindings-library"></a>Создание библиотеки привязок

Перед началом следующие действия, загрузите пример [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android архивного файла:

1.  Создайте новый проект библиотеки привязок, начиная с библиотеки Android привязок шаблонов. Можно использовать Visual Studio для Mac или Visual Studio (на снимке экрана ниже Показать Visual Studio, но очень похожа Visual Studio для Mac). Присвойте решению имя **AarBinding**:

    [![Создание проекта AarBindings](binding-an-aar-images/01-new-bindings-library-vs-sml.w157.png)](binding-an-aar-images/01-new-bindings-library-vs.w157.png#lightbox)

2.  Шаблон включает **JAR-файлов** папки, которые добавляются к. AAR(s) на проект библиотеки привязок. Щелкните правой кнопкой мыши **JAR-файлов** папку и выберите **Добавить > существующий элемент**:

    [![Добавление существующего элемента](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  Перейдите к **textanalyzer.aar** ранее загрузить файл, выберите его и нажмите кнопку **добавить**:

    [![Добавить textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  Убедитесь, что **textanalyzer.aar** файл был успешно добавлен в проект:

    [![Был добавлен файл textanalyzer.aar](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  Задайте действие построения для **textanalyzer.aar** для `LibraryProjectZip`. Щелкните правой кнопкой мыши в Visual Studio для Mac **textanalyzer.aar** для настройки действия построения. Действие при построении в Visual Studio можно задать в **свойства** области):

    [![Указав действия построения textanalyzer.aar LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  Откройте проект свойства для настройки *требуемой версии .NET Framework*. Если. AAR использует интерфейсы API Android, задать целевую платформу на уровне API. Ожидает AAR. (Дополнительные сведения о параметре целевой платформы и Android API уровней в целом см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).)

    Необходимо задайте конечный уровень API для библиотеки привязок. В этом примере мы, можно использовать последнюю версию платформы уровень API (API уровня 23), поскольку наш **textanalyzer** не с зависимостями от Android API-интерфейсы:

    [![Задание целевого уровня API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  Построение библиотеки привязок. Проект библиотеки привязок следует построить успешно и результат. Библиотеки DLL в следующем расположении: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>С помощью библиотеки привязок

Чтобы использовать это. Библиотеки DLL в приложении Xamarin.Android, сначала необходимо добавить ссылку на библиотеку привязок. Выполните следующие действия:

1.  В том же решении, как библиотека привязок для упрощения в данном пошаговом руководстве мы создаем этого приложения. (Приложения, использующего библиотеку привязки также может располагаться в другом решении.) Создайте новое приложение Xamarin.Android: щелкните правой кнопкой мыши решение и выберите **Добавление нового проекта**. Имя для нового проекта **BindingTest**:

    [![Создание нового проекта BindingTest](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2.  Щелкните правой кнопкой мыши **ссылки** узел **BindingTest** проект и выберите **добавить ссылку...** :

    [![Нажмите кнопку Добавить ссылку](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  Выберите **AarBinding** проект, созданный ранее и нажмите кнопку **ОК**:

    [![Проверить проект привязки AAR](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  Откройте **ссылки** узел **BindingTest** проекта, чтобы убедиться, что **AarBinding** присутствует ссылка:

    [![AarBinding находится в списке ссылок](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


Если вы хотите просмотреть содержимое библиотеки привязки проекта, можно дважды щелкнуть ссылку, чтобы открыть его в **обозревателя объектов**. Вы увидите содержимое сопоставленной `Com.Xamarin.Textcounter` пространства имен (, полученного из Java `com.xamarin.textanalyzezr` пакета), а также просматривать члены `TextCounter` класса:

[![Просмотр обозревателя объектов](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

Приведенном выше снимке экрана выделяет два `TextAnalyzer` методы, которые будут вызывать примера приложения: `NumConsonants` (который создает оболочку для базовой Java `numConsonants` метод), и `NumVowels` (который создает оболочку для базовой Java `numVowels` метод).



### <a name="accessing-aar-types"></a>Доступ к. Типы AAR

После добавления ссылки в приложении, который указывает библиотеку привязки можно использовать типы Java в. AAR, что и у вас будет доступ к типов C# (благодаря оболочки C#). Код приложения C#, может вызвать `TextAnalyzer` методы, как показано в следующем примере:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

В приведенном выше примере выполняется вызов статических методов `TextCounter` класса. Тем не менее можно создавать экземпляры классов и вызывать методы экземпляра. Например если ваш. AAR создает оболочку для класса с именем `Employee` , имеется метод экземпляра `buildFullName`, можно создать экземпляр `MyClass` и использовать его как показано ниже:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

Следующие действия для добавления кода в приложение, чтобы у пользователя для текста, использует `TextCounter` для анализа текста, а затем отображает результаты.

Замените **BindingTest** макета (**Main.axml**) следующий XML-код. Этот макет содержит `EditText` для ввода текста и две кнопки для инициализации счетчиков гласные и согласных:

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

Замените содержимое **MainActivity.cs** следующим кодом. Как показано в этом примере вызов обработчиков событий кнопки в оболочку `TextCounter` методы, которые находятся в. AAR и использование всплывающие уведомления для отображения результатов. Обратите внимание `using` оператор для пространства имен связанные библиотеки (в данном случае `Com.Xamarin.Textcounter`):

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

Компиляция и выполнение **BindingTest** проекта. Приложение запускается и представления в левой части экрана ( `EditText` инициализируется некоторый текст, но вы можете коснуться ее, чтобы изменить его). При нажатии элемента **число ГЛАСНЫЕ**, всплывающее уведомление показывает число гласные, как показано в правой части:

[![Снимки экрана из работающих BindingTest](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

Коснитесь **число СОГЛАСНЫХ** кнопки. Кроме того можно изменить эту строку текста и коснитесь эти кнопки еще раз, чтобы проверить различные гласные и подсчитывает согласных.



### <a name="accessing-aar-resources"></a>Доступ к. AAR ресурсы

Xamarin, средства слияния **R** данные. AAR в ваше приложение **ресурсов** класса. В результате доступны. AAR ресурсы с макета (и с выделенным кодом) в так же, как и к ресурсам, находящимся в **ресурсов** путь проекта.

Чтобы открыть ресурс изображения, используйте **Resource.Drawable** имя для образа упакованы внутри. AAR. Например, можно ссылаться на **image.png** в. Файл AAR с помощью `@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

Также можно получить доступ к макеты ресурсов, которые находятся в. AAR. Чтобы сделать это, используйте **Resource.Layout** имя макета из пакета. AAR. Пример:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer.aar** пример содержит файл образа, который находится в **res/drawable/monkey.png**. Давайте доступа к этому ресурсу изображения и использовать его в нашем примере приложении:

Изменить **BindingTest** макета (**Main.axml**) и добавьте `ImageView` в конец `LinearLayout` контейнера. Это `ImageView` отображает изображение в **@drawable/monkey**; этот образ будет загружен из раздела ресурсов **textanalyzer.aar**:

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

Компиляция и выполнение **BindingTest** проекта. Приложение запускается и представления в левой части экрана &ndash; при нажатии элемента **число СОГЛАСНЫХ**, результаты отображаются, как показано в правой части:

[![Отображение числа согласный BindingTest](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


Поздравляем! Был успешно присоединен библиотеки Java. AAR!



## <a name="summary"></a>Сводка

В этом пошаговом руководстве мы создали библиотеки для привязки. Файл AAR добавлена библиотеки привязок минимальной тестовое приложение и запускали приложение, чтобы убедиться, вызова кода Java, хранящегося в наш код C#. AAR-файл.
Кроме того, мы расширили приложения для доступа и отображения ресурс изображения, который находится в. AAR-файл.


## <a name="related-links"></a>Связанные ссылки

- [Библиотека привязки создается Java (видео)](https://university.xamarin.com/classes#10090)
- [Привязка JAR-файла](~/android/platform/binding-java-library/binding-a-jar.md)
- [Привязка библиотеки Java](~/android/platform/binding-java-library/index.md)
- [AarBinding (пример)](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Ошибка 44573 один проект невозможно привязать несколько файлов .aar](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
