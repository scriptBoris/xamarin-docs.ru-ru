---
title: Графика и анимация
description: Android предоставляет очень широкие и разнообразные платформу для поддержки двумерной графики и анимации. В этом разделе представлены эти платформы и обсуждаются способы создания пользовательской графики и анимации в приложении Xamarin.Android.
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: c49b8855bccaf2eca825096746769d7f201736c5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116892"
---
# <a name="graphics-and-animation"></a>Графика и анимация

_Android предоставляет очень широкие и разнообразные платформу для поддержки двумерной графики и анимации. В этом разделе представлены эти платформы и обсуждаются способы создания пользовательской графики и анимации в приложении Xamarin.Android._


## <a name="overview"></a>Обзор

Несмотря на то, работает на устройствах, которые обычно являются ограниченные возможности, часто имеют сложные пользователя Механизма взаимодействия, наивысшим рейтингом мобильных приложений с высококачественной графикой и анимации, которые предоставляют интуитивно понятный, гибкий, динамическое поведение. Как мобильные приложения получают более и более сложные, пользователи начали более ожидать от приложения.

К счастью для нас, современные мобильные платформы имеют очень мощные системы для создания сложных анимаций и пользовательских графических объектов, при этом сохраняя простота использования. Это позволяет разработчикам добавлять широкие возможности взаимодействия с минимальными усилиями.

Примерно платформы пользовательского интерфейса API в Android можно разделить на две категории: графики и анимации.

Графики делятся на различные подходы к выполнив двухмерной и трехмерной графики. 3D-графики доступны через ряд встроенных платформ, таких как OpenGL ES (мобильных определенную версию OpenGL), а также сторонние платформы, такие как MonoGame (это кроссплатформенный набор средств совместимыми с набором средств XNA). Несмотря на то, что трехмерной графики выходят за рамки данной статьи, мы рассмотрим встроенные 2D методы рисования.

Android предоставляет два разных API для создания двухмерной графики. Одним является высокого уровня декларативный подход, а другой программный API низкого уровня:

-   **Прорисовываемые ресурсы** &ndash; они используются для создания пользовательской графики программными средствами или (как правило) путем внедрения инструкции по рисованию в XML-файлах. Прорисовываемые ресурсы обычно определяются как XML-файлы, которые содержат инструкции и действия для Android для отрисовки двумерной графики. 

-   **Холст** &ndash; это низкого уровня API, который включает в себя Рисование непосредственно в базовой растрового изображения. Он предоставляет детальный контроль над отображаемые. 

Помимо этих методов двумерная графика Android также предоставляет несколько различных способов создания анимации:

-   **Drawable анимации** &ndash; Android также поддерживает анимации фрейм за фреймом, известный как *Drawable анимации*. Это самый простой API анимации. Android последовательно загружает и отображает Прорисовываемые ресурсы в последовательности (подобно мультфильма).

-   **Просматривать анимации** &ndash; *анимации представление* анимации исходного интерфейса API в Android и доступны во всех версиях Android. Этот API ограничено тем, что он будет работать только с объектами представления и простые преобразования можно выполнить только на эти представления.
    Посмотрите видеоролики обычно определяются в XML-файлов, найденных в `/Resources/anim` папку.

-   **Анимация свойств** &ndash; Android 3.0 появился новый набор API анимации, известный как *анимации свойств*. Эти новые API появился расширяемую и гибкую систему, который может использоваться для анимации свойств любого объекта, не только просматривать объекты. Такая гибкость позволяет анимации, который необходимо инкапсулировать в различных классах, которые будет выполнять код, сложность совместного использования.


Посмотрите видеоролики больше подходят для приложений, которые должен поддерживать старые Android до 3.0 интерфейса API (API уровня 11). В противном случае приложения должны использовать более новый API анимации свойства, по причинам, как описано выше.

Все эти платформы являются приемлемыми параметры, тем не менее по возможности предпочтений следует уделять анимации свойств, так как это более гибкий API для работы с. Анимация свойств позволяют логику анимации, который необходимо инкапсулировать в различных классах, повышает сложность совместного использования кода и упрощает обслуживание кода.


## <a name="accessibility"></a>Специальные возможности

Графики и анимации позволяют сделать приложения Android привлекательным и им интересно пользоваться; Тем не менее важно помнить, что некоторые взаимодействия происходят через screenreaders, альтернативные устройства ввода, или с помощью масштабирования, служба технической.
Кроме того некоторые взаимодействия может возникнуть без возможности аудио.

Приложения — более удобным в таких ситуациях, если они были разработаны с учетом специальных возможностей: предоставление подсказки и помощь навигации в пользовательском интерфейсе и обеспечение текстовое содержимое или описания графические элементы пользовательского интерфейса.

Ссылаться на [руководство по доступности Google](http://developer.android.com/guide/topics/ui/accessibility/) Дополнительные сведения о том, как использовать специальные возможности Android API-интерфейсы.



## <a name="2d-graphics"></a>Двумерная графика

Drawable ресурсы — популярный способ в приложениях Android. Как и другие ресурсы, Прорисовываемые ресурсы являются декларативными &ndash; они определяются в XML-файлах. Такой подход позволяет для четкого разделения кода из ресурсов. Это позволяет упростить разработку и обслуживание, так как нет необходимости изменять код для обновления или изменения на снимках экрана в приложение Android. Тем не менее хотя Прорисовываемые ресурсы полезны для многих простых и распространенных графических требований, они не хватает питания и управления Canvas API-интерфейса.

Метод, с помощью [холст](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/) объекта, очень похожа на других традиционных платформ API, например System.Drawing или Core рисование в iOS. С помощью объекта Canvas предоставляет наибольший контроль как двумерной графики создаются. Это подходит для ситуаций, где Drawable ресурс не будет работать или будет трудно работать. Например может потребоваться для рисования пользовательского управления "ползунок" внешний вид которого будет изменен на основе вычислений, связанных с значение ползунка.

Давайте рассмотрим Прорисовываемые ресурсы. Они проще и охватывают наиболее распространенные варианты пользовательского рисования.


### <a name="drawable-resources"></a>Прорисовываемые ресурсы

Прорисовываемые ресурсы определяются в XML-файл в каталоге `/Resources/drawable`. В отличие от внедрения, PNG или JPEG, не требуется указать плотность версии Прорисовываемые ресурсы.
Во время выполнения приложение Android загрузит эти ресурсы и используйте инструкции, содержащиеся в этих файлах XML для создания двумерной графики.
Android определяет несколько разных видов Прорисовываемые ресурсы:

-   [ShapeDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash; это объект Drawable, которая рисует примитивов геометрические фигуры и применяет ограниченный набор графических эффектов на этой фигуре. Они очень полезны для таких вещей, такие как настройка кнопки или задание фона TextViews. Мы увидим пример того, как использовать `ShapeDrawable` далее в этой статье.

-   [StateListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash; Drawable ресурс, который будет изменить внешний вид, исходя из состояния мини-приложения или элемента управления. Например кнопки может изменить его внешний вид, в зависимости от того, является ли она нажата или не.

-   [LayerDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash; This Drawable ресурс, который будет стека несколько прорисовываемых ресурсов один поверх другого. Пример *LayerDrawable* показано на следующем снимке экрана:

    ![Пример LayerDrawable](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash; это *LayerDrawable* , но с одним отличием. Объект *TransitionDrawable* может анимировать один слой отображается поверх другого.

-   [LevelListDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash; это очень похоже на *StateListDrawable* тем, что она выводит изображение на основании определенных условий. Однако в отличие от *StateListDrawable*, *LevelListDrawable* показывает изображение на основе значения целого числа. Пример *LevelListDrawable* позволит отображать это интенсивность сигнала Wi-Fi. Как это интенсивность сигнала изменения Wi-Fi drawable, отображаемый изменится соответствующим образом.

-   [ScaleDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash; как их названия, эти прорисовываемых ресурсов обеспечения масштабирования и обрезки функциональные возможности. *ScaleDrawable* масштабируется другой Drawable, время *ClipDrawable* отсекает другой Drawable.

-   [InsetDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash; This Drawable применит отступы на краях другого Drawable ресурса. Он используется, когда представление фон, меньше, чем фактические границы представления.

-   XML [BitmapDrawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash; этот файл представляет собой набор инструкций, в XML, которые должны выполняться на фактическим растровым. Некоторые действия, которые могут выполнять Android: разбиение, дизеринга и сглаживания. Одной из очень распространенные варианты применения этого является Плитка растрового изображения в фоновом режиме макета.


#### <a name="drawable-example"></a>Drawable пример

Давайте рассмотрим краткий пример того, как создать двухмерной графики с помощью `ShapeDrawable`. Объект `ShapeDrawable` можно определить одно из четырех базовых фигур: прямоугольник, Овал, строки и кольцо. Можно также применить базовые эффекты, такие как градиента, цвет и размер. В следующем XML-коде `ShapeDrawable` , можно найти в *AnimationsDemo* сопутствующий проект (в файле `Resources/drawable/shape_rounded_blue_rect.xml`).
Он определяет прямоугольник с фиолетовым градиентным фоном и скругленные углы:

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

Мы можем ссылаться на этот ресурс Drawable декларативно в макете или других Drawable, как показано в следующем коде XML:

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

Прорисовываемые ресурсы могут также применяться программным способом. В следующем фрагменте кода показано, как программно задать фон текстового представления:

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

Чтобы узнать, как это будет выглядеть, выполните *AnimationsDemo* проекта и выберите элемент Drawable фигуры в главном меню. Мы должны увидеть нечто похожее на следующем снимке экрана:

![TextView с настраиваемый фон, drawable со градиента и скругленными внутренними углами](graphics-and-animation-images/image1.png)

Дополнительные сведения о XML-элементов и синтаксисе Прорисовываемые ресурсы, обратитесь к [документации Google](http://developer.android.com/guide/topics/resources/drawable-resource.html#Shape).


### <a name="using-the-canvas-drawing-api"></a>С помощью API рисования Canvas

Прорисовываемых ресурсов являются мощным средством, но имеет свои ограничения. Определенные действия, которые являются невозможно, или сложными (например: применение фильтра на рисунок, который был занят камеры на устройстве). Было бы очень трудно применить уменьшение эффекта красных глаз с помощью Drawable ресурса.
Вместо этого API холст позволяет приложению детальный контроль выборочно изменять цвета в определенной части рисунка.

Является один класс, который часто используется с холстом [Paint](https://developer.xamarin.com/api/type/Android.Graphics.Paint/) класса. Этот класс содержит цвет и стиль сведения о рисовании. Он используется для предоставления вещи, цвета и прозрачности.

Использует API холст *модели художника* для отрисовки двумерной графики.
Операции применяются в последовательные слои друг над другом. Каждой операции будут рассмотрены некоторые области базовой растрового изображения. Если области перекрывается ранее закрашиваемой области, новый краске будет частично или полностью скрыть старый. Это же способ работы многими другими рисования API, например, System.Drawing и двухмерной графики в iOS.

Существует два способа получения `Canvas` объекта. Первый способ заключается в определении [точечного рисунка](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) объекта, а затем создание экземпляра `Canvas` объекта с ним. Например следующий фрагмент кода создает новый холст с базовой точечного рисунка:

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

Другой способ получить `Canvas` является объект [OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/) метод обратного вызова, который предоставляется [представление](https://developer.xamarin.com/api/type/Android.Views.View/) базового класса. Android вызывает этот метод, когда он решает представления необходимо нарисовать сам себя и передает в `Canvas` объект для представления для работы с.

Класс Canvas предоставляет методы могут программно инструкции draw. Пример:

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash; заполняет весь холст растровое изображение указанного paint.

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash; Рисует указанный геометрические фигуры, с помощью указанного paint.

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash; Рисует текст на холсте указанного цвета. Текст рисуется в расположении `x,y` .



#### <a name="drawing-with-the-canvas-api"></a>Рисование с помощью холста API

Рассмотрим пример API Canvas в действие. В следующем фрагменте кода показано, как нарисовать представления:

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

Приведенный выше код сначала создает красный рисования и зеленый paint объекта. Он заполняет содержимое части холста красным цветом, а также сообщает холст для рисования зеленый прямоугольник, который составляет 25% от ширины полотна. Примером этого могут просматриваться в `AnimationsDemo` проект, который входит в состав исходный код для этой статьи. Запуск приложения и выбрав в главном меню элемент Рисование, мы должны экран следующего вида:

![Экран с paint красный и зеленый рисования объектов](graphics-and-animation-images/image3.png)


## <a name="animation"></a>Анимация

Пользователям, и действия, которые выполняют перемещение в своих приложениях. Анимация — отличный способ улучшить взаимодействие с пользователем приложения и фоне. Лучшие анимации являются те, которые пользователи не замечают, так как они естественным. Android предоставляет следующие три интерфейса API для анимации.

-   **Просмотреть анимацию** &ndash; это исходный API. Эти анимации привязаны к определенному представлению и может выполнять простые преобразования на содержимое представления. Из-за его простоты этот API, по-прежнему удобна для таких вещей, такие как альфа-анимации, вращения и т. д.

-   **Свойство анимации** &ndash; анимаций свойства были введены в Android 3.0. Они позволяют приложению использовать для анимации, практически все. Анимация свойств можно использовать для изменения любого свойства любого объекта, даже если этот объект не отображается на экране.

-   **Drawable анимации** &ndash; это специальные Drawable ресурс, который используется для применения очень простая анимация в силу макеты.

В общем случае свойство анимации — это предпочтительный система для использования, так как он является более гибким и предлагает дополнительные функции.


### <a name="view-animations"></a>Посмотрите видеоролики

Посмотрите видеоролики ограничен представления и анимации можно выполнить только на значения, такие как start и конечных точек, размер, поворот и прозрачности.
Эти виды анимации, обычно называются *анимации*. Посмотрите видеоролики можно определить двумя способами &ndash; программно в коде или с помощью XML-файлов. XML-файлы являются предпочтительным способом для объявления представления анимации, так как они являются более читаемым и упрощает поддержку.

XML-файлы анимации, которые будут храниться в `/Resources/anim` каталоге проекта Xamarin.Android. Этот файл должен иметь одно из следующих элементов в качестве корневого элемента:

-   `alpha` &ndash; Анимации постепенного появления или постепенного исчезновения.

-   `rotate` &ndash; Анимация вращения.

-   `scale` &ndash; Анимированный значок изменения размера.

-   `translate` &ndash; Горизонтальные или вертикальные движения.

-   `set` &ndash; Контейнер, который может содержать один или несколько других элементов анимации.

По умолчанию все анимации в XML-файл будет применяться одновременно. Чтобы сделать анимации происходят последовательно, установите `android:startOffset` атрибут на одном из элементов, определенных выше.

Существует возможность повлиять на скорость изменения в анимации с помощью *интерполятора*. Интерполятора делает возможным для ускоренной, повторяющихся или decelerated эффектов анимации. Платформы Android предоставляет несколько interpolators карты по умолчанию, такие как (но не ограничиваясь ими):

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; Эти interpolators карты увеличить или уменьшить скорость изменения в анимации.

-   `BounceInterpolator` &ndash; изменение будет передаваться в конце.

-   `LinearInterpolator` &ndash; скорость изменения является константой.


Следующий код XML показывает пример файла анимации, которая объединяет некоторые из этих элементов:

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

Эта анимация будет выполнять все анимации одновременно. Первой анимации масштабирования будет изображение растягивается по горизонтали и уменьшить его по вертикали, а затем изображение будет одновременно Повернуть по часовой стрелке на 45 градусов и сжиматься, исчезает с экрана.

Анимации программным образом применим к представлению, подобных анимации, а затем применить ее к представлению. Android предоставляет вспомогательный класс `Android.Views.Animations.AnimationUtils` , которые будут увеличению ресурс анимации и возвращают экземпляр `Android.Views.Animations.Animation`. Этот объект применяется к представлению, вызвав `StartAnimation` и передачи `Animation` объекта. В следующем фрагменте кода показан пример этого:

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

Теперь, когда у нас есть основные сведения о работе анимации представления, позволяет переместить анимаций свойства.


### <a name="property-animations"></a>Анимация свойств

Свойство мультипликаторы — это новый API, который появился в Android 3.0.
Они предоставляют большую гибкость, API, который может использоваться для анимировать любое свойство для любого объекта.

Все свойства анимации создаются экземпляры [Animator](https://developer.xamarin.com/api/type/Android.Animation.Animator/) подкласс. Приложения не используют этот класс напрямую, вместо этого они используют один из его подклассов.

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash; этот класс является наиболее важный класс анимации всего свойства API. Она вычисляет значения свойств, которые должны быть изменены. `ViewAnimator` Напрямую не обновить эти значения; вместо этого он вызывает события, которые можно использовать для обновления анимированных объектов.

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash; этот класс является подклассом `ValueAnimator` . Он предназначен для упрощения процесса анимированными объектами, принимая целевой объект и свойства для обновления.

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash; этот класс отвечает за управление выполнением анимаций в отношении друг с другом. Анимации могут выполняться одновременно, последовательно или с заданной задержкой между ними.


*Средства оценки* — это специальные классы, используемые мультипликаторы, чтобы рассчитать новые значения во время анимации. По умолчанию Android предоставляет следующие средства оценки:

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash; вычисляет значения для свойства целого числа.

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash; вычисляет значения для свойств с плавающей запятой.

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash; вычисляет значения для свойств цвета.

Если свойство анимируемого не `float`, `int` или цветной, приложения могут создавать свои собственные средства оценки путем реализации `ITypeEvaluator` интерфейс. (Реализация пользовательского средства оценки выходит за рамки этой статьи.)

#### <a name="using-the-valueanimator"></a>С помощью ValueAnimator

Любой анимации, состоит из двух частей: вычисление анимированных значений, а затем установив эти значения свойств на определенном объекте. 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) вычислит только значения, но он не будет работать на объектах напрямую. Вместо этого объекты будут обновляться внутри обработчиков событий, которые будут вызываться в течение времени существования анимации. Такой подход позволяет выполнить обновление от одного значения анимированного несколько свойств.

Получить экземпляр `ValueAnimator` путем вызова одного из следующих методов фабрики:

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

После завершения `ValueAnimator` экземпляр должен иметь значение его длительность, а затем его можно запустить. Приведенный ниже показано, как анимировать значение от 0 до 1 в промежутке 1000 миллисекунд:

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

Но, приведенном выше фрагменте кода не очень полезен &ndash; animator будет выполняться, но нет цели для обновленного значения. `Animator` Класса будет вызывать событие обновления, когда он решает, что это необходимо для информирования прослушивателей нового значения. Приложения может предоставить обработчик событий, чтобы ответить на это событие, как показано в следующем фрагменте кода:

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

Теперь, когда у нас есть представление о `ValueAnimator`, позволяет получить дополнительные сведения о `ObjectAnimator`.

#### <a name="using-the-objectanimator"></a>С помощью ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) является подклассом `ViewAnimator` , объединяющее времени ядра и значение вычисления `ValueAnimator` с логику, необходимую для подключения обработчиков событий. `ValueAnimator` Требует, чтобы явно подключить обработчик событий приложения &ndash; `ObjectAnimator` позаботится о этот шаг для нас.

API для `ObjectAnimator` очень похожа на API для `ViewAnimator`, но необходимо указать объект и имя свойства для обновления. В следующем примере показано пример использования `ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

Как видно из предыдущего фрагмента кода, `ObjectAnimator` можно сократить и упростить код, который необходим для анимации объекта.


### <a name="drawable-animations"></a>Drawable анимации

Последняя анимация API является Drawable API анимации. Drawable анимации загрузить ряд Прорисовываемые ресурсы один за другим и отобразить их последовательно, аналогичную мультфильма пролистывание ИТ.

Прорисовываемые ресурсы определяются в XML-файл, имеющий `<animation-list>` элемента в качестве корневого элемента и ряд `<item>` элементы, определяющие каждый кадр анимации. Этот XML-файл хранится в `/Resource/drawable` папку приложения. Следующий XML-код является примером drawable анимации:

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

Эта анимация будет выполняться через шесть кадров. `android:duration` Атрибут объявляет, как долго будет отображаться каждого кадра. В следующем фрагменте кода показан пример создания Drawable анимации, а затем запустить ее, когда пользователь нажимает кнопку на экране:

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

На этом этапе мы рассмотрели основы анимации интерфейсы API, доступные в приложении Android.


## <a name="summary"></a>Сводка

В этой статье представлен ряд новых концепций и интерфейсы API, для добавления некоторых графических изображений в приложение Android. Сначала он обсуждается различные API двумерная графика и демонстрируется, как Android позволяет приложениям для рисования непосредственно на экран, с помощью объекта Canvas. Мы также увидели некоторые альтернативные методы, которые позволяют графики декларативно создается с использованием XML-файлов. Затем мы мы объяснили, старого и нового интерфейса API для создания анимации в Android.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация анимации (пример)](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [Анимация и графика](http://developer.android.com/guide/topics/graphics/index.html)
- [Вдохните жизнь в ваши мобильные приложения с помощью анимации](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [Canvas](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [Объект Animator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [Значение Animator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)
