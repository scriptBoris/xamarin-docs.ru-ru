---
title: Введение в iOS 7
description: В этой статье рассматриваются основные новые интерфейсы API, представленные в iOS 7, включая смену View Controller, усовершенствования UIView анимации, UIKit Dynamics и текст пакета. Она также охватывает некоторые изменения в пользовательском интерфейсе и новые возможности многозадачной расширенная.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 9ae82eba78f099f675d21bf53a250923630a0ff6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780022"
---
# <a name="introduction-to-ios-7"></a>Введение в iOS 7

_В этой статье рассматриваются основные новые интерфейсы API, представленные в iOS 7, включая смену View Controller, усовершенствования UIView анимации, UIKit Dynamics и текст пакета. Она также охватывает некоторые изменения в пользовательском интерфейсе и новые возможности многозадачной расширенная._

iOS 7 — это крупное обновление для операций ввода-вывода. В ней представлен совершенно новую пользовательского интерфейса, устанавливает фокус на chrome содержимого, а не приложения. Наряду с визуальные изменения iOS 7 добавляет множество новых интерфейсов API для создания обширные возможности взаимодействия и взаимодействия. Этот документ опросов новые технологии предварен iOS 7 и служит в качестве отправной точки для дальнейшего изучения.

## <a name="uiview-animation-enhancements"></a>Усовершенствования UIView анимации

iOS 7 расширяет поддержку анимации в UIKit, позволяя приложениям выполнять действия, ранее удаление непосредственно в framework Core анимации. Например `UIView` теперь может выполнять spring анимации, а также анимации ключевого кадра, который ранее `CAKeyframeAnimation` применены к `CALayer`.

### <a name="spring-animations"></a>Анимация пружины

 `UIView` Теперь поддерживает анимации изменения свойств с эффектом spring. Чтобы добавить это, вызовите `AnimateNotify` или `AnimateNotifyAsync` метод, передав значения для коэффициент затухания spring и скорости начальной spring, как описано ниже:

-  `springWithDampingRatio` — Значение от 0 до 1, где колебаний возрастает для меньшее значение.
-  `initialSpringVelocity` — Начальной spring скорости в процентах от расстояния общее анимации в секунду.


В следующем коде создается эффект spring при изменении центра просмотра изображений:

```csharp
void AnimateWithSpring ()
{ 
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;
    
    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {
    
        imageView.Center = new CGPoint (imageView.Center.X, 400);   
            
    }, null);
}
```

Этот эффект spring в результате представление изображения для скачками по мере его анимации в новое расположение центра, как показано ниже:

 ![](images/spring-animation.png "Этот эффект spring вызывает скачками по мере его анимации на новое место центр представления изображения")

### <a name="keyframe-animations"></a>Ключевой кадр анимации

`UIView` Класс теперь включает `AnimateWithKeyframes` метод для создания анимации ключевого кадра на `UIView`. Этот метод похож на другие `UIView` методов анимации, кроме тех, которые еще `NSAction` передается как параметр для включения опорные кадры. В пределах `NSAction`, опорные кадры добавляются путем вызова `UIView.AddKeyframeWithRelativeStartTime`.

Например следующий фрагмент кода создает ключевой кадр анимации, чтобы анимировать центр представления также относительно Поворот представления:

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => { 
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => { 
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

Первые два параметра для `AddKeyframeWithRelativeStartTime` метод указать время начала и длительность ключевой кадр, соответственно, в процентах от общей длины анимации. В примере выше результаты в образ представление анимации его новый центр по первой второй следуют поворот на 90 градусов по следующей секунды. Поскольку указывает анимации `UIViewKeyframeAnimationOptions.Autoreverse` в качестве альтернативного варианта оба ключевых кадров анимации в обратном направлении также. Наконец конечные значения устанавливаются в исходное состояние, в обработчик завершения.

На снимке экрана ниже показано объединенный анимации через ключевые кадры.

 ![](images/keyframes.png "Это снимки экрана показан объединенный анимации через опорные кадры")

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics — это новый набор API-интерфейсов в UIKit, которые позволяют приложениям для создания анимированных взаимодействий, в зависимости от физических. UIKit Dynamics инкапсулирует подсистему 2D физических для этого.

API-Интерфейс является декларативным по своей природе. Объявите поведение физических взаимодействий с помощью создания объектов - вызывается *поведения* - express физических концепций, например тяжести, конфликтов, пружины, и т. д. Затем присоедините behavior(s) к другой объект, называемый *динамическое animator*, который инкапсулирует представления. Динамические animator принимает различные применения поведения физических объявленный в *динамические элементы* -элементы, реализующие `IUIDynamicItem`, такие как `UIView`.

Существует несколько разных примитивов поведения доступность для запуска сложных взаимодействий, в том числе:

-  `UIAttachmentBehavior` — Присоединяет два динамических элементов таким образом, чтобы они перемещались вместе или присоединяет динамических элементов к точке вложения.
-  `UICollisionBehavior` — Позволяет участвовать в конфликтах динамических элементов.
-  `UIDynamicItemBehavior` — Задает общий набор свойств, которые применяются к динамические элементы, такие как эластичности, плотность и повышение эффективности.
-  `UIGravityBehavior` -Применяется тяжести на динамический элемент, вызывает элементы для ускорения в направлении сила притяжения.
-  `UIPushBehavior` – Применяется принудительно для динамических элементов.
-  `UISnapBehavior` — Разрешает привязать к позиции с эффектом spring динамического элемента.


Хотя существует много примитивов, общий процесс для добавления в представлении с помощью UIKit Dynamics взаимодействия на основе физических является согласованным для всех поведений:

1.  Создание динамических animator.
1.  Создайте behavior(s).
1.  Добавление динамического animator поведения.


### <a name="dynamics-example"></a>Пример Dynamics

Давайте рассмотрим пример, который добавляет тяжести и границу конфликтов для `UIView`.

#### <a name="uigravitybehavior"></a>UIGravityBehavior

Добавление центра тяжести представление изображения следует 3 шаги, описанные выше.

Мы будем работать `ViewDidLoad` метода для этого примера. Сначала добавьте `UIImageView` экземпляра следующим образом:

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Это создает представление изображения по центру на верхний край экрана. Чтобы сделать изображение «падение» с центра тяжести, создайте экземпляр `UIDynamicAnimator`:

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator` Принимает экземпляр ссылку `UIView` или `UICollectionViewLayout`, который содержит элементы, которые будут анимированы каждого вложенного behavior(s).

Создайте `UIGravityBehavior` экземпляра. Можно передать один или несколько объектов, реализующая `IUIDynamicItem`, таких как `UIView`:

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Поведение передается массив `IUIDynamicItem`, таким образом, который содержит один `UIImageView` анимации мы экземпляра.

Наконец добавьте поведение динамических animator:

```csharp
dynAnimator.AddBehavior (gravity);
```

Это приводит к изображения, анимации вниз с центра тяжести, как это показано ниже:

![](images/gravity2.png "Начальное положение изображения") 
![](images/gravity3.png "конечное расположение образа")

Так как нет ничего ограничение границы экрана, представление изображения просто попадает в нижней. Чтобы ограничить представление, чтобы изображение в противоречие с границами экрана, можно добавить `UICollisionBehavior`. Мы обсудим в следующем разделе.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Начнем с создания `UICollisionBehavior` и добавление его в динамический animator, так же, как мы сделали `UIGravityBehavior`.

Измените код, включив `UICollisionBehavior`:

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior` Имеет свойство с именем `TranslatesReferenceBoundsIntoBoundry`. Установка этого параметра `true` делает ссылку границы представления для использования в качестве границ конфликтов.

Теперь когда изображение анимирует вниз с центра тяжести, он будет передаваться немного в нижней части экрана перед этим для rest существует.

<!--, as shown below:

 ![](images/bounce.png "Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there")-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Мы можно далее контролировать поведение падающим представление изображения с помощью дополнительных вариантов поведения. Например, мы добавляем `UIDynamicItemBehavior` для увеличения гибкости, вызывая представление изображения более скачками, если оно конфликтует с нижней части экрана.

Добавление `UIDynamicItemBehavior` следует те же действия, как и другие варианты поведения. Сначала создайте поведение:

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Затем можно добавьте поведение динамических animator:

 `dynAnimator.AddBehavior (dynBehavior);`

С этим поведением на месте представление изображения будет передаваться несколько при встрече с границей.

## <a name="general-user-interface-changes"></a>Пользовательский интерфейс общие изменения

Помимо новых API UIKit UIKit, переходы контроллера, и улучшенная UIView анимации, описанных выше iOS 7 множеству визуальные изменения в пользовательский интерфейс и связанные изменения в API для различных представлений и элементов управления. Дополнительные сведения см. [iOS 7 Обзор пользовательского интерфейса](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Текст пакета

Набор текста — это новый API, который предлагает мощный текст функции макета и подготовки к просмотру. Является надстройкой низкий уровень framework основного текста, но гораздо проще по сравнению с основного текста.

Дополнительные сведения см. в разделе нашей [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Многозадачность

iOS 7 изменяется, когда и как выполняется фоновая операция. Завершение задачи в iOS 7 больше не поддерживает приложения переходит в спящий режим, когда задачи выполняются в фоновом режиме и приложений активируются для фоновой обработки несмежных образом. iOS 7 также добавляет три новые интерфейсы API для обновления приложений с новым содержимым в фоновом режиме.

-  Выборку в фоновом режиме — приложения для обновления содержимого в фоновом режиме, через регулярные интервалы.
-  Удаленный уведомления - позволяет приложениям для обновления содержимого при получении push-уведомлений. Уведомления могут быть либо автоматической или отображения баннера на экране блокировки.
-  Фоновая служба передачи — позволяет отправка и загрузка данных, таких как большие файлы без строгого ограничения времени.


Дополнительные сведения о новых возможностях многозадачной см. в разделах операций ввода-вывода для Xamarin [Backgrounding руководство](~/ios/app-fundamentals/backgrounding/index.md).

## <a name="summary"></a>Сводка

В этой статье рассматриваются некоторые основные элементы для добавления операций ввода-вывода. Во-первых показано, как добавить пользовательский переходы Просмотр контроллеров. Затем показано, как использовать переходы в представления коллекций, как в контроллере навигации, а также интерактивно между представлениями коллекции. Затем она порождает несколько усовершенствований, внесенных в UIView анимации, показывающая использование UIKit приложениями для того, что раньше требовал напрямую с основной анимации. Наконец новый API Dynamics UIKit, который предоставляет механизм физических для UIKit, впервые появился наряду с поддержки форматированного текста, теперь доступны в платформе текст пакета.

## <a name="related-links"></a>Связанные ссылки

- [Введение в iOS 7 (пример)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Обзор пользовательского интерфейса iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Фоновая обработка](~/ios/app-fundamentals/backgrounding/index.md)
