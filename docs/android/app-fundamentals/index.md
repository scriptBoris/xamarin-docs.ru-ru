---
title: Принципы работы приложения Xamarin.Android
description: Основные понятия приложения
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1d3bd071eeffb77f94a1b5f35f1df59f2d8c7a8a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105562"
---
# <a name="xamarinandroid-application-fundamentals"></a>Принципы работы приложения Xamarin.Android

Этот раздел содержит указания на некоторые наиболее распространенных вещей задачи или концепции, которые разработчики должны иметь в виду при разработке приложений Android приложения.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Специальные возможности](~/android/app-fundamentals/accessibility.md)

Эта страница описывает, как использовать API Android специальных возможностей для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md)

В этом руководстве описывается, как Android использует уровни API, управление совместимостью приложений в разных версиях Android, и здесь объясняется, как настроить параметры проекта Xamarin.Android, чтобы развернуть эти уровни API в вашем приложении. Кроме того в этом руководстве объясняется процесс написания кода среды выполнения, который работает с разными уровнями API, и предоставляет список всех уровнях Android API, номера версий (например, Android 8.0), Android имена кода (например, Oreo) и коды версий сборки.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Ресурсы в Android](~/android/app-fundamentals/resources-in-android/index.md)

В этой статье введено понятие ресурсы Android в документах и Xamarin.Android, их использование. Здесь рассматривается, как использовать ресурсы в приложении Android для поддержки локализации приложения и несколько устройств, включая различными размерами экранов и плотности.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)

Действия являются основной строительный блок рабочих приложений Android, и они могут присутствовать в нескольких различных состояний. Жизненный цикл действия начинается с создания экземпляра и заканчивается уничтожение и включает много состояний между ними. При изменении состояния действия соответствующие жизненного цикла события вызове метода уведомления активности внесение изменений состояния и позволяя ему для выполнения кода, чтобы адаптироваться к это изменение. В этой статье рассматриваются жизненного цикла действий и объясняется ответственность за наличие действия выполнения каждой из этих изменений как часть долгосрочных, надежные приложения.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Локализация](~/android/app-fundamentals/localization.md)

В этой статье объясняется, как локализовать Xamarin.Android на другие языки путем перевода строки и предоставляя альтернативные изображения.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Службы](~/android/app-fundamentals/services/index.md)

В этой статье рассматриваются службы Android, которые являются компоненты Android, позволяющие должны быть выполнены в фоновом режиме. Он объясняет различные сценарии, подходящие для служб и показано, как реализовать их для выполнения длительных фоновых задач также относительно предоставляют интерфейс для вызовов удаленных процедур.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Широковещательные приемники](~/android/app-fundamentals/broadcast-receivers.md)

В этом руководстве описывается, как создать и использовать широковещательных получателей Android компонент, который отвечает на широковещательные рассылки всей системы, в Xamarin.Android.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Разрешения](~/android/app-fundamentals/permissions.md)

Чтобы создать и добавить разрешения в манифесте Android можно использовать поддержку средств, встроенных в Visual Studio для Mac или Visual Studio. В этом документе описывается, как добавить разрешения в Visual Studio и Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Графика и анимация](~/android/app-fundamentals/graphics-and-animation.md)

Android предоставляет очень широкие и разнообразные платформу для поддержки двумерной графики и анимации. В этом документе представлены эти платформы и обсуждаются способы создания пользовательской графики и анимации и их использования в приложениях Xamarin.Android.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Архитектуры процессоров](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android поддерживает несколько архитектур ЦП, включая 32-разрядных и 64-разрядные устройства. В этой статье объясняется, как для нацеливания на приложение на один или несколько архитектур ЦП поддерживается Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Обработка поворота](~/android/app-fundamentals/handling-rotation.md)

В этой статье описывается, как обрабатывать изменения ориентации устройства в Xamarin.Android. Здесь рассматривается, как для работы с системой Android ресурсов автоматически загрузить ресурсы для с ориентацией для конкретного устройства, как программным образом обрабатывать ориентации изменяется. Затем описываются методы для поддержки состояния при повороте устройства.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android аудио](~/android/app-fundamentals/android-audio.md)

ОС Android предоставляет расширенную поддержку мультимедиа, которая объединяет в себе аудио и видео. В этом руководстве рассматриваются аудио в Android и охватывает воспроизводить и записывать звук, с помощью встроенных аудио-плейер и классов средства записи, а также API низкого уровня аудио. Он также рассматривается работа с аудио события широковещательных другими приложениями, таким образом, разработчики могут создавать правильно работающие приложения.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Уведомления](~/android/app-fundamentals/notifications/index.md)

В этом разделе объясняется, как реализовать локальных и удаленных уведомлений в Xamarin.Android. Он описывает различные элементы пользовательского интерфейса уведомление Android и описываются API связанных с созданием и отображение уведомления. Для удаленных уведомлений рассматриваются как Google Cloud Messaging и Firebase Cloud Messaging. Включены пошаговые руководства и примеры кода.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Сенсорные технологии](~/android/app-fundamentals/touch/index.md)

В этом разделе описываются основные понятия и Дополнительные сведения о реализации сенсорные жесты в Android. API-интерфейсы сенсорного ввода являются появился и описано следуют просмотра распознавателей жестов.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Стек HttpClient и SSL/TLS](~/android/app-fundamentals/http-stack.md)

В этом разделе объясняются селекторы HttpClient Stack и реализация SSL/TLS для Android. Эти параметры определяют реализация HttpClient и SSL/TLS, который будет использоваться для приложений Xamarin.Android.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Написание быстро реагирующих приложений](writing-responsive-apps.md)

В этой статье описывается использование потоков для сохранения быстрые приложения Xamarin.Android, переместив длительные задачи в фоновом потоке.