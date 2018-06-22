---
title: Принципы работы приложения Xamarin.Android
description: Основные понятия приложения
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: caa51fa0a70da1b799d56c706e6de974f61a14d9
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436434"
---
# <a name="xamarinandroid-application-fundamentals"></a>Принципы работы приложения Xamarin.Android

В этом разделе содержится руководство для некоторых наиболее распространенных задач действия или концепции, разработчики должны иметь в виду при разработке приложений Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Специальные возможности](~/android/app-fundamentals/accessibility.md)

На этой странице описаны способы использования интерфейсов API Android специальных возможностей для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md)

В этом руководстве описывается, как Android использует уровни API для управления совместимость приложений в разных версиях Android, а также описание способов настройки параметров проекта Xamarin.Android для развертывания этих API уровней в приложении. Кроме того в этом руководстве объясняется процесс написания кода среды выполнения, которая имеет дело с разными уровнями API, а также список Справочник по Android API уровней, номера версий (например, Android 8.0), Android имена кода (например, Oreo) и коды версии сборки.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Ресурсы в Android](~/android/app-fundamentals/resources-in-android/index.md)

В этой статье рассматривается понятие Android ресурсов в документах и Xamarin.Android способ их использования. В этом примере рассматривается использование ресурсов в приложении Android для поддержки локализации приложения и несколько устройств, включая различными размерами экранов и плотности.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Жизненный цикл действия](~/android/app-fundamentals/activity-lifecycle/index.md)

Действия — основной строительный блок приложений Android, и они могут присутствовать в нескольких состояниях. Жизненный цикл действия начинается с создания экземпляра и заканчивается уничтожения между включает много состояний. При изменении состояния действия вызывается метод события жизненного цикла соответствующие, уведомив действия внесение изменений состояния и что позволяет выполнять код для адаптации к внесения данного изменения. В этой статье рассматриваются жизненного цикла действия и объясняется ответственность за наличие действия во время каждой из этих изменений в состав обретают, надежные приложения.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Локализация](~/android/app-fundamentals/localization.md)

В этой статье объясняется, как для локализации Xamarin.Android на другие языки перевода строк и другие изображения.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Службы](~/android/app-fundamentals/services/index.md)

В этой статье рассматриваются Android службы, являющиеся Android компонентов, которые позволяют выполнить в фоновом режиме. Он описаны различные сценарии, подходящие для службы и показано, как реализовать их как для выполнения фоновой долго выполняющихся заданий, а также относительно предоставить интерфейс для удаленных вызовов процедур.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Широковещательные приемники](~/android/app-fundamentals/broadcast-receivers.md)

В этом руководстве описывается создание и использование широковещательных приемники Android компонент, который отвечает на уровне системы широковещательные рассылки, в Xamarin.Android.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Разрешения](~/android/app-fundamentals/permissions.md)

Средства, интегрированные в Visual Studio или Visual Studio для Mac можно использовать для создания и добавления разрешений Android манифеста. В этом документе описывается добавление разрешения в Visual Studio и Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Графика и анимация](~/android/app-fundamentals/graphics-and-animation.md)

Android предоставляет очень широкие и разнообразные платформу для поддержки 2D-графики и анимации. В этом документе представлены такие платформы и описывает, как создать пользовательский графики и анимации и использовать их в приложении Xamarin.Android.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Архитектуры процессоров](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android поддерживает несколько архитектуры ЦП, включая устройства с 32-разрядных и 64-разрядной. В этой статье объясняется, как для нацеливания на приложение в один или несколько архитектуры ЦП поддерживается Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Обработка поворота](~/android/app-fundamentals/handling-rotation.md)

В этой статье описывается изменение ориентации устройства в Xamarin.Android обработки. В этом примере рассматривается с системой Android ресурсов для автоматической загрузки ресурсы для использования конкретного устройства ориентации, как можно программным образом обрабатывать ориентации изменяется. Затем он описывает методы для поддержки состояния при повороте устройства.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android аудио](~/android/app-fundamentals/android-audio.md)

Для ОС Android предоставляет широкие возможности для мультимедиа, включающая аудио и видео. В этом руководстве основное внимание уделяется звука в Android и рассматриваются воспроизводить и записывать звук, используя встроенный проигрыватель и записи классов, а также низкоуровневые звуковой API. Она также охватывает работе с событиями аудио широковещательных другими приложениями, чтобы разработчики могут создавать приложения правильно.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Уведомления](~/android/app-fundamentals/notifications/index.md)

В этом разделе описывается реализация уведомлений о локальных и удаленных в Xamarin.Android. Здесь описаны элементы пользовательского интерфейса Android уведомление о и описываются API связанные с созданием и отображает соответствующее уведомление. Для удаленного уведомлений описано Google Cloud Messaging и Firebase Cloud Messaging. Пошаговые руководства и примеры кода включены.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Сенсорные технологии](~/android/app-fundamentals/touch/index.md)

В этом разделе объясняется, что основные понятия и сведения о реализации сенсорные жесты на Android. API-интерфейсы сенсорный ввод появились и объясняются следовали исследования распознавателей жестов.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Стек HttpClient и SSL/TLS](~/android/app-fundamentals/http-stack.md)

В этом разделе объясняются селекторы стека HttpClient и реализации SSL/TLS для Android. Эти параметры определяют, будет использоваться в приложениях Xamarin.Android реализация класса HttpClient и SSL/TLS.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Написание отвечать на запросы приложений](writing-responsive-apps.md)

В этой статье описывается использование потоков для обеспечения быстрого реагирования приложения Xamarin.Android, перемещая длительные задачи в фоновом потоке.