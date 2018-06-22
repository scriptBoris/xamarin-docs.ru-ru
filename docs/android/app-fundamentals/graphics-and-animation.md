---
title: Графики и анимации
description: Android предоставляет очень широкие и разнообразные платформу для поддержки 2D-графики и анимации. В этом разделе представлены такие платформы и объясняет способы создания пользовательских графики и анимации в приложении Xamarin.Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 85a7cd2ac5094a4760c5465bd099ce2e3beeae79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773791"
---
# <a name="graphics-and-animation"></a>Графики и анимации

_Android предоставляет очень широкие и разнообразные платформу для поддержки 2D-графики и анимации. В этом разделе представлены такие платформы и объясняет способы создания пользовательских графики и анимации в приложении Xamarin.Android._


## <a name="overview"></a>Обзор

Несмотря на выполнение на устройствах, которые обычно имеют ограниченный power высоким рейтингом мобильных приложений часто встречаются сложные пользователя качества (интерфейса), дополненный высокое качество графики и анимации, которые предоставляют интуитивно понятный, гибкий, динамические вид. Мобильные приложения получают более и более сложные пользователи начали более ожидать от приложений.

К счастью для нас современных мобильных платформ имеют очень мощный платформы для создания сложных анимации и графики при сохранении простоты использования. Это позволяет разработчикам добавлять широкие возможности взаимодействия с минимальными усилиями.

Примерно платформы пользовательского интерфейса API в Android можно разделить на две категории: графики и анимации.

Графики делятся на различные подходы к повышению двухмерной и трехмерной графики. Трехмерная графика доступны через ряд встроенных в платформы, например OpenGL ES (мобильных конкретной версии OpenGL) и сторонних платформ, например MonoGame (межплатформенные набор инструментов совместима с XNA toolkit). Несмотря на то, что трехмерной графики не находятся в пределах данной статьи, будут рассмотрены встроенные методы 2D рисования.

Android предоставляет два различных API-Интерфейсы для создания двумерной графики. Выполняется одно высокоуровневая декларативный подход, а другой программный интерфейс API нижнего уровня.

-   **Ресурсы drawable** &ndash; эти значения используются для создания пользовательской графики программными средствами или (обычно) путем внедрения инструкции по рисованию в XML-файлах. Drawable ресурсы обычно определяются как XML-файлы, содержащие инструкции или действий для Android для отображения двумерной графики. 

-   **Холст** &ndash; это низкого уровня API, который включает в себя Рисование непосредственно на базовые растрового изображения. Он предоставляет детальный контроль над отображаемых. 

Помимо этих методов двумерной графики Android также предоставляет несколько способов создания анимации:

-   **Drawable анимации** &ndash; Android также поддерживает фрейм за фреймом анимации, известный как *Drawable анимации*. Это простой API анимации. Android последовательно загружает и отображает Drawable ресурсы в последовательности (аналогично Мультипликационный).

-   **Просматривать анимации** &ndash; *анимации представление* являются исходного анимация API-Интерфейсы в Android и доступны во всех версиях Android. Этот API ограничена в том, что он будет работать только с объектами представления и преобразования можно выполнить только в тех представлениях.
    Представление анимации обычно определяются в XML-файлов, найденных в `/Resources/anim` папки.

-   **Свойства анимации** &ndash; Android 3.0 появился новый набор API анимации, известный как *анимации свойства*. Эти новые API появился расширяемую и гибкую систему, который можно использовать для анимации свойства любого объекта, не только просматривать объекты. Такая гибкость позволяет анимации, который необходимо инкапсулировать в разные классы, которые позволят упростить совместное использование кода.


Просмотр анимации больше подходят для приложений, которые должен поддерживать старые предварительной Android 3.0 API-Интерфейсов (API уровня 11). В противном случае приложения должны использовать более новые API анимации свойства по причинам, упомянутых выше.

Все из этих платформ, причем параметры, однако там, где это возможно, предпочтений должен быть предоставлен для анимации свойства, как это более гибкий API-интерфейс для работы с. Свойства анимации позволяют логику анимации инкапсулировать в различных классах, делает код проще для управления доступом и упрощает обслуживание кода.


## <a name="accessibility"></a>Специальные возможности

Графики и анимации поможет сделать привлекательным приложений Android и интересные для использования; Тем не менее важно помнить, что некоторые взаимодействие осуществляется через screenreaders альтернативные устройства ввода, или с помощь масштаба.
Кроме того некоторые диалоги возможна без возможности аудио.

Приложения более удобным в таких ситуациях, если они были разработаны с учетом специальных возможностей: предоставление указания и помощь навигации в пользовательском интерфейсе и обеспечение того, текстовое содержимое или описания графические элементы пользовательского интерфейса.

Ссылаться на [Google специальных возможностей руководство](http://developer.android.com/guide/topics/ui/accessibility/) Дополнительные сведения о том, как использовать специальные возможности Android API-интерфейсы.



## <a name="2d-graphics"></a>2D-графики

Drawable ресурсы — это популярный способ в приложения Android. Как и с другими ресурсами Drawable ресурсы декларативный &ndash; они определены в XML-файлы. Такой подход позволяет для четкого разделения кода из ресурсов. Это позволяет упростить разработку и обслуживание, так как нет необходимости изменять код для обновления или изменения графики в приложение. Тем не менее хотя многие графические требования простой и общие полезны Drawable ресурсы, у которых отсутствует питания и управления Canvas API-интерфейса.

Другой подход с использованием [холст](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) объекта, очень похоже на другие традиционные API платформы, например System.Drawing или рисование основных операций ввода-вывода. С помощью объекта Canvas предоставляет полный контроль как двумерной графики создаются. Он подходит в ситуациях, когда Drawable ресурсов не будет работать или будет трудно работать с. Например может потребоваться Рисование пользовательских ползунок элемента управления, вид изменится на основе вычислений, связанных с значение ползунка.

Давайте рассмотрим Drawable ресурсов. Их проще и покрытия наиболее распространенных случаях пользовательского рисования.


### <a name="drawable-resources"></a>Drawable ресурсы

Drawable ресурсы определяются в XML-файла в каталоге `/Resources/drawable`. В отличие от внедрения PNG или JPEG, необязательно для предоставления версии плотность Drawable ресурсов.
Во время выполнения приложения загрузить эти ресурсы и используйте инструкции, содержащиеся в этих файлах XML для создания двумерной графики.
Android определяет несколько разных типов Drawable ресурсов:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; Drawable объект, который рисует примитива геометрической фигуры и применяет ограниченный набор графических эффектов на этой фигуре. Это очень удобно использовать для таких операций, как настройка кнопки или задание фона TextViews. Мы увидим пример демонстрирует использование `ShapeDrawable` далее в этой статье.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; Drawable ресурс, который приведет к изменению внешнего вида, исходя из состояния мини-приложений и управление. Например кнопка может изменить его внешний вид в зависимости от того, является ли она нажата или не.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; этот Drawable ресурс, который будет стека несколько drawables, один поверх другого. Пример *LayerDrawable* показано на следующем снимке экрана:

    ![Пример LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; это *LayerDrawable* , но с одним отличием. Объект *TransitionDrawable* способен анимировать один слой отображается поверх другого.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; это очень похоже на *StateListDrawable* в том, что он выводит изображение на основе определенных условий. Однако в отличие от *StateListDrawable*, *LevelListDrawable* отображает изображение, зависящее от целочисленное значение. Пример *LevelListDrawable* позволит отображать степень сигнала Wi-Fi. Как стойкость изменения сигнала Wi-Fi drawable, отображаемый изменится соответствующим образом.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; как и предполагает его имя, эти Drawables обеспечения масштабирования и отсекает функциональные возможности. *ScaleDrawable* масштабируется в другое время Drawable, *ClipDrawable* изменяет другой Drawable.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; этот Drawable применит отступы на сторонах другого Drawable ресурса. Он используется, если представление должно фон, меньше, чем фактический границы представления.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; этот файл представляет собой набор инструкций, в XML, которые должны выполняться с фактическим растровым. Некоторые действия, которые можно выполнять Android: разбиение, дизеринга и сглаживания. Одним из применений очень часто это является плитку растровое изображение в фоновом режиме макета.


#### <a name="drawable-example"></a>Пример drawable

Давайте рассмотрим краткий пример того, как создать двумерной графики с помощью `ShapeDrawable`. Объект `ShapeDrawable` можно определить один из четырех основных фигур: прямоугольник, Овал, строки и кольца. Можно также применить основными эффектами, например градиент, цвет и размер. В следующем XML-коде `ShapeDrawable` , можно найти в *AnimationsDemo* сопутствующий проект (в файле `Resources/drawable/shape_rounded_blue_rect.xml`).
Оно определяет прямоугольник с фоновым градиентом фиолетовый и скругленные углы:

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

Мы могут ссылаться на этот ресурс Drawable декларативно в макете или других Drawable, как показано в следующем коде XML:

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

Drawable ресурсы могут также применяться программными средствами. В следующем фрагменте кода показано, как программно задать фон текстового представления:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Чтобы просмотреть, как это будет выглядеть, запустите *AnimationsDemo* проекта и выберите элемент Drawable фигуры в главном меню. Мы будет выглядеть следующим образом:

![TextView пользовательского фона, drawable с градиента и прямоугольника с закругленными углами](graphics-and-animation-images/image1.png)

Дополнительные сведения о XML-элементов и синтаксисе Drawable ресурсов, обратитесь к [документации Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).


### <a name="using-the-canvas-drawing-api"></a>С помощью API рисования холста

Drawables мощные, но имеющие их ограничения. Определенные действия, которые являются очень сложными или невозможно (например: применение фильтра к рисунка, который был занят камеру на устройстве). Он будет очень трудно применение красных сокращение с помощью Drawable ресурсов.
Вместо этого API холста позволяет приложению очень детального контроля, чтобы выборочно изменить цвета в определенной части изображения.

Один класс, который обычно используется с холста является [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) класса. Этот класс содержит цвет и стиль сведения о способах рисования. Он используется для предоставления вещей, цвета и прозрачности.

Использует API холст *модели рисования* для отрисовки двумерных изображений.
Операции, применяются в последовательные слои друг над другом. Каждая операция мы рассмотрим некоторые области базового растрового изображения. Если области перекрывается ранее закрашиваемой области, новый рисования будет частично или полностью скрыть старый. Это аналогично работать много других рисования API, например System.Drawing и операций ввода-вывода в двухмерной графики.

Существует два способа получения `Canvas` объекта. Первый способ заключается в определении [растрового изображения](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) объекта, а затем создание `Canvas` объекта с ним. Например следующий фрагмент кода создает новый холст с базовой растрового изображения:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

Другой способ получить `Canvas` объекта является [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) метод обратного вызова, который предоставляется [представление](https://developer.xamarin.com/api/type/Android.Views.View/) базового класса. Android вызывает этот метод, когда он решает представления необходимо нарисовать сам и передает в `Canvas` объект представления для работы с.

Холст класс предоставляет методы для предоставления инструкции draw программными средствами. Пример:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; заполняет весь холст растровое изображение указанного рисования.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; Рисует указанный геометрические фигуры с помощью указанного рисования.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; Рисует текст на холсте указанный цвет. Текст рисуется в расположении `x,y` .



#### <a name="drawing-with-the-canvas-api"></a>Рисование с Canvas API

Рассмотрим пример API холста в действие. В следующем фрагменте кода показано, как рисовать представления:

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

Приведенный выше код сначала создает красный рисования и зеленый рисования объекта. Выполняет заливку содержимого части холста красным цветом и затем указывает, что полотно для рисования зеленый прямоугольник, который является 25% от ширины холста. Примером этого может их видеть в `AnimationsDemo` проекта, который входит в состав исходный код для этой статьи. Запуск приложения и выбора элемента рисования в главном меню, мы должны экран, аналогично приведенным ниже:

![Экран с красным рисования и зеленый рисования объектов](graphics-and-animation-images/image3.png)


## <a name="animation"></a>Анимация

Пользователи, такие как действия, связанные с переносом в своих приложениях. Анимации — отличный способ повышения удобства работы пользователей приложения и помочь выделить его. Наиболее анимации являются те, которые пользователи не Обратите внимание, поскольку они естественным. Android предоставляет следующие три API для анимации.

-   **Просмотр анимации** &ndash; исходного API-интерфейс. Эти анимации, привязаны к определенному представлению и простого преобразования можно выполнять на содержимое представления. Из-за его простоты этот API, действительны для выполнения задач, например альфа-анимации, вращения и т. д.

-   **Свойства анимации** &ndash; в Android 3.0 появились анимации свойства. Они позволяют приложению анимировать угодно. Анимация свойств можно использовать для изменения любого свойства любого объекта, даже если этот объект не отображается на экране.

-   **Анимация drawable** &ndash; это специальные Drawable ресурс, который используется для применения очень простая анимация эффект макеты.

В общем случае анимации свойства является предпочтительным систему для использования как он является более гибким и предоставляет дополнительные возможности.


### <a name="view-animations"></a>Просмотр анимации

Анимация представления ограничены представления и анимации можно выполнить только на значения, такие как запуск и конечных точек, размер, поворот и прозрачности.
Эти типы анимации, обычно называются *анимации*. Посмотрите видеоролики могут быть определены два способа &ndash; программно в коде или с помощью XML-файлов. XML-файлы являются предпочтительным способом для объявления представления анимации, как они являются более читаемым и упрощает обслуживание.

XML-файлы, анимация будет храниться в `/Resources/anim` каталог проекта Xamarin.Android. Этот файл должен иметь одно из следующих элементов как корневой элемент:

-   `alpha` &ndash; Постепенное или затухания анимации.

-   `rotate` &ndash; Анимация вращения.

-   `scale` &ndash; Изменения размера анимации.

-   `translate` &ndash; Горизонтальные или вертикальные движения.

-   `set` &ndash; Контейнер, который может содержать один или несколько других элементов анимации.

По умолчанию все анимации в XML-файл будет применяться одновременно. Чтобы происходить последовательно анимации, задать `android:startOffset` атрибутов на одном из элементов, определенных выше.

Существует возможность влияет на скорость изменения в анимации с помощью *интерполятора*. Интерполятора делает возможным для ускоренной, повторяющиеся или decelerated эффектов анимации. Платформа Android предоставляет несколько interpolators карты без дополнительной настройки, такие как (но не ограничиваясь ими):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Эти interpolators карты увеличить или уменьшить скорость изменения в анимации.

-   `BounceInterpolator` &ndash; Это изменение будет передаваться в конце.

-   `LinearInterpolator` &ndash; частота изменений является константой.


Следующий код XML показан пример файла анимации, который объединяет некоторые из этих элементов.

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

Эту анимацию выполняют все анимации одновременно. Первой шкалы анимации будут растяжения изображения по горизонтали и по вертикали уменьшить его и затем изображение будет одновременно поворачивать 45 градусов по часовой стрелке и сжатие, исчезновению из экрана.

Анимация программным образом применим к представлению дало бы ошибку анимации, а затем применяет его к представлению. Android предоставляет вспомогательный класс `Android.Views.Animations.AnimationUtils` , которые будут увеличению ресурс анимации и возвращают экземпляр `Android.Views.Animations.Animation`. Этот объект применяется к представлению, вызвав `StartAnimation` и передачи `Animation` объекта. В следующем фрагменте кода показан пример этого.

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Теперь, когда у нас есть фундаментальное понимание принципов работы представление анимации, позволяет переместить анимации свойства.


### <a name="property-animations"></a>Свойства анимации

Свойство мультипликаторы — это новый API, который впервые появился в Android 3.0.
Они предоставляют расширения API, который может использоваться для любого свойства для всех объектов анимации.

Все свойства анимации, создаются экземпляры [Animator](https://developer.xamarin.com/api/type/Android.Animation.Animator/) подкласс. Приложения не используют этот класс напрямую, вместо этого они используют один из его подклассов.

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; этот класс является наиболее важным в API всего свойства анимации. Она вычисляет значения свойств, которые должны быть изменены. `ViewAnimator` Напрямую не обновить эти значения; вместо этого он вызывает события, которые можно использовать для обновления анимированных объектов.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; этот класс является подклассом `ValueAnimator` . Он предназначен для упрощения процесса анимации объектов, принимая целевого объекта и свойства для обновления.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; этот класс отвечает за управление выполнением анимации по отношению друг к другу. Анимация может работать одновременно, последовательно или с заданной задержки между ними.


*Вычислители* — это специальные классы, используемые мультипликаторы для вычисления новых значений во время анимации. Готовые Android предоставляет следующие средства оценки:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; вычисляет значения для свойств целое число со знаком.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; вычисляет значения для свойств с плавающей точкой.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; вычисляет значения для свойств цвета.

Если свойство, которое выполняется анимация не `float`, `int` или цветной, приложения могут создавать свои собственные средства оценки путем реализации `ITypeEvaluator` интерфейса. (Реализация пользовательского средства оценки выходит за рамки этой статьи.)

#### <a name="using-the-valueanimator"></a>С помощью ValueAnimator

Эффекты анимации, состоит из двух частей: вычисление анимированных значений, а затем установив эти значения для свойств, на какой-либо объект. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) вычислит только значения, но он не будет работать с объектами напрямую. Вместо этого будет обновить объекты внутри обработчиков событий, которые будут вызываться в течение времени существования анимации. Такой подход позволяет выполнить обновление от одного анимированные значения несколько свойств.

Получение экземпляра `ValueAnimator` путем вызова одного из следующих методов фабрики:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

После готовности, `ValueAnimator` экземпляр должен иметь значение его длительности и затем его можно запускать. В следующем примере демонстрируется анимация значение от 0 до 1 по длительности 1000 миллисекунд:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Но, приведенном выше фрагменте кода не очень удобен &ndash; animator запустится, но нет цели для обновленного значения. `Animator` Класса инициирует событие обновления при решает, что требуется для информирования прослушиватели нового значения. Приложения может предоставить обработчик событий, чтобы ответить на это событие, как показано в следующем фрагменте кода:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

Теперь, когда у нас есть понимание `ValueAnimator`, позволяет получить дополнительные сведения о `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>С помощью ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) является подклассом `ViewAnimator` , объединяющее времени ядра и значение вычисления `ValueAnimator` с логику, необходимую для подключения обработчиков событий. `ValueAnimator` Требует, чтобы явно подключить обработчик событий приложения &ndash; `ObjectAnimator` позаботится об этом шаге для нас.

API-Интерфейс для `ObjectAnimator` очень похож на API для `ViewAnimator`, но необходимо указать объект и имя свойства для обновления. В примере показан пример использования `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Как видно из предыдущего фрагмента кода, `ObjectAnimator` можно сократить и упростить код, который необходим для анимации объекта.


### <a name="drawable-animations"></a>Drawable анимации

Последняя анимация API является Drawable API анимации. Drawable анимации загрузить ряд Drawable ресурсов один за другим и отобразить их последовательно, аналогично Мультипликационный отразить ИТ.

Drawable ресурсы определяются в XML-файле, который имеет `<animation-list>` элемента в качестве корневого элемента, а также ряд `<item>` элементы, которые определяют каждый кадр анимации. Этот XML-файл хранится в `/Resource/drawable` папке приложения. Следующий XML-код является примером drawable анимации:

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

Через шесть кадров будет выполняться этой анимации. `android:duration` Атрибут объявляет, как долго будет отображаться каждый кадр. В следующем фрагменте кода показан пример создания Drawable анимации и запустить ее, когда пользователь нажимает кнопку на экране.

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

На этом этапе мы рассмотрели базовых принципов анимации API-интерфейса в приложение.


## <a name="summary"></a>Сводка

В этой статье представлены много новых понятий и API-Интерфейс пользователя для добавления некоторых графических изображений для приложения. Сначала рассматриваются различные двумерной графики API-интерфейса и показано, как Android позволяет приложениям для вывода на экран с помощью объекта Canvas. Кроме того, мы узнали некоторые альтернативные методы, которые позволяют графики декларативно создается с использованием XML-файлов. Затем мы перешли обсудить старого и нового интерфейса API для создания анимации в Android.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация анимации (пример)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Анимации и графики](http://developer.android.com/guide/topics/graphics/index.html)
- [Использование анимации для воплощать мобильных приложений](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Объект Animator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Значение Animator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
