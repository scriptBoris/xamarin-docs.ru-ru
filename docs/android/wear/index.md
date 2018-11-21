---
title: Android Wear
description: Создание приложений для устройств Android носимого.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: 75b78f27fd6b3a726de9075ce36364168401ec60
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171482"
---
# <a name="android-wear"></a>Android Wear

Android Wear — это версия Android, предназначенный для носимого устройств, таких как смарт-часы. Этот раздел содержит инструкции по установке и настройке средств, необходимых для разработки одежды, пошаговое руководство по созданию первого устройства износа и список примеров, который можно использовать для создания собственной ее приложений.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Начало работы](~/android/wear/get-started/index.md)

Представляет Android Wear, описывается установка и настройка компьютера для разработки износа и пошаговые инструкции помогут вам создать и запустили свое первое приложение Android Wear на эмуляторе или устройстве Android Wear.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Пользовательский интерфейс](~/android/wear/user-interface/index.md)

Описание Android Wear конкретных элементов управления и ссылки на примеры, демонстрирующие способы использования этих элементов управления.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Функции платформы](~/android/wear/platform/index.md)

Документы в этом разделе рассматриваются возможности, предназначенные для Android Wear. Здесь вы найдете в этом разделе описывается создание WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Размеры экрана](~/android/wear/screen-sizes.md)

Предварительный просмотр и оптимизировать пользовательский интерфейс для размеров экрана.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Тестирование и развертывание](~/android/wear/deploy-test/index.md)

В этой статье описывается развертывание приложения Android Wear на устройстве Android Wear или эмуляторе Android, настроенный для одежды. Она также включает отладку советы и сведения о настройке подключения Bluetooth на компьютере разработчика устройства Android.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[API-интерфейсы wear](https://developer.android.com/reference/android/support/wearable)

На сайте разработчика Android предоставляет подробные сведения о ключе Wear API-интерфейсы, например [Носимого действия](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [Intents](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [проверки подлинности](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Сложности](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [сложности отрисовки](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [уведомления](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [представления](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), и [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Примеры

Вы найдете ряд [примеры](https://developer.xamarin.com/samples/android/Android%20Wear/) с помощью Android Wear (или перейти непосредственно к [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Пример|Описание:|Снимок экрана|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Простой пример с основами носимого проектов, включая GridViewPager и интерактивные уведомления.|![Снимок экрана Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Простой демонстрационный элемента управления WatchViewStub, который определяет экран фигуры и автоматически загружает подходящего макета.  См. в разделе, как работает WatchViewStub в **Resources/layout/main_activity.xml** макета.|![Снимок экрана WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Демонстрация страниц износа уведомлений, в виде набора команд действия. Уведомления создаются в RecipeService.cs.|![Снимок экрана RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Интересные пример взаимодействия с «личного помощника» вызывается Eliza, с помощью износа интерактивные уведомления для создания диалога, используя стандартные ответы.|![Снимок экрана ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|GridViewPager реализует шаблон 2D навигации, где пользователь предъявляет по вертикали и по горизонтали, чтобы просматривать параметры и содержимое.|![Снимок экрана GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace является пользовательский оформлений стиле аналоговый час, минуту и второй руки. В этом примере демонстрируется создание службы лиц Контрольные значения, который отображает текущее время и окружения режиме дескрипторов и видимость событий изменения. Он включает в себя широковещательный приемник, который прослушивает изменения часового пояса и автоматически обновляет время соответствующим образом.|![Снимок экрана WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Видеоролики

Рекомендуем ознакомиться с видео, что ссылки, в которых обсуждаются Xamarin.Android с износа поддерживают:

|Описание:|Снимок экрана|
|--- |--- |
|[Android L и многое другое](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android Developer Preview L появился множество новых интерфейсов API для разработчиков, чтобы воспользоваться преимуществами, включая материальному дизайну, уведомления и новые анимации лишь некоторые из них.|![Снимок экрана видео презентации](images/video-android-l.png)|
|[C#в моем уши и в глаза: стекла Google и Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; Носимого вычислений и выглядит как что-то из будущего (или эпизода инспектор мини-приложения), но многие пользователи уже используют эту будущего уже сегодня! C#Разработчики знают и уже есть средства и навыки, необходимые для мощь носимого устройств (от развития 2014 г.).|![Снимок экрана видео презентации](images/video-eyes-ears.png)|
|[Новые возможности в Xamarin.Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, Android Wear, Android TV, Android Auto, материальному дизайну и ИСКУССТВО; что делает это означает для вас как разработчика Xamarin? из 2014 развития.|![Снимок экрана видео презентации](Images/video-whats-new.png)|


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
