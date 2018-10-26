---
title: Тестирование, оптимизация и развертывание приложений Xamarin.Android
description: В этом разделе перечислены руководства, в которых рассматриваются вопросы тестирования приложения, оптимизации его производительности, подготовки к выпуску, подписывания с помощью сертификата и публикации в магазине приложений.
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 4c9ab8c14db131427329cef51e7b74e982a1c7b8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103690"
---
# <a name="deployment-and-testing"></a>Развертывание и тестирование

В этом разделе перечислены руководства, в которых рассматриваются вопросы тестирования приложения, оптимизации его производительности, подготовки к выпуску, подписывания с помощью сертификата и публикации в магазине приложений.


##  <a name="application-package-sizesapp-package-sizemd"></a>[Application Package Sizes](app-package-size.md) (Размеры пакета приложения)

В этой статье рассматриваются элементы, входящие в пакет приложения Xamarin.Android, и связанные с ними стратегии эффективного развертывания пакетов на этапах отладки и выпуска.

##  <a name="building-appsbuilding-appsindexmd"></a>[Building Apps](building-apps/index.md) (Сборка приложений)

В этой статье описано, как создавать приложения и пакеты APK для конкретного набора ABI.

##  <a name="command-line-emulatorcommand-line-emulatormd"></a>[Command Line Emulator](command-line-emulator.md) (Эмулятор командной строки)

В этой статье кратко описывается запуск эмулятора через командную строку.

## <a name="debuggingandroiddeploy-testdebuggingindexmd"></a>[Отладка](~/android/deploy-test/debugging/index.md)

Сведения в руководствах в этом разделе будут полезны при отладке приложения с помощью эмуляторов Android, реальных устройств Android и журнала отладки.

##  <a name="setting-the-debuggable-attributeandroiddeploy-testdebuggable-attributemd"></a>[Установка атрибута Debuggable](~/android/deploy-test/debuggable-attribute.md)

В этой статье объясняется, как задать атрибут Debuggable, чтобы `adb` и другие средства могли взаимодействовать с виртуальной машиной Java.

##  <a name="environmentenvironmentmd"></a>[Среда](environment.md)

Эта статья описывает среду выполнения Xamarin.Android и системные свойства Android, влияющие на выполнение программы.

##  <a name="gdbgdbmd"></a>[GDB](gdb.md)

В этой статье описывается применение `gdb` для отладки приложений Xamarin.Android.

##  <a name="installing-a-system-appinstall-system-appmd"></a>[Installing a System App](install-system-app.md) (Установка системного приложения)

В этом руководстве объясняется, как установить приложение Xamarin.Android в роли системного приложения на устройстве Android или на пользовательском диске.

##  <a name="linking-on-androidlinkermd"></a>[Linking on Android](linker.md) (Компоновка на Android)

В этой статье рассматривается процесс компоновки, который в Xamarin.Android помогает уменьшить размер конечного приложения. Здесь описаны разные доступные уровни компоновки и предложены некоторые рекомендации по работе и устранению ошибок, которые могут возникнуть при использовании компоновщика.

## <a name="xamarinandroid-performanceandroiddeploy-testperformancemd"></a>[Производительность Xamarin.Android](~/android/deploy-test/performance.md)

Существует множество методов повышения производительности приложений, созданных с помощью Xamarin.Android. Вместе они могут значительно снизить объем работы, выполняемой ЦП, а также объем используемой приложением памяти.

## <a name="profiling-android-appsandroiddeploy-testprofilingmd"></a>[Профилирование приложений Android](~/android/deploy-test/profiling.md)

Это руководство поясняет, как использовать средства профилирования для изучения производительности и использования памяти в приложении Android.


## <a name="preparing-an-application-for-releaseandroiddeploy-testrelease-prepindexmd"></a>[Подготовка приложения к выпуску](~/android/deploy-test/release-prep/index.md)

После создания и тестирования кода приложения необходимо подготовить пакет для распространения. Первой задачей при подготовке этого пакета является сборка приложения для выпуска, в основном предполагающая установку некоторых атрибутов приложения.

## <a name="signing-the-android-application-packageandroiddeploy-testsigningindexmd"></a>[Подписывание пакета приложения Android](~/android/deploy-test/signing/index.md)

Узнайте, как создать удостоверение подписывания Android, создать сертификат подписи для приложений Android и подписать приложение с помощью этого сертификата. Кроме того, здесь объясняется, как экспортировать приложение на диск для *прямого* распространения. Полученный неопубликованный APK-файл можно установить на устройства с Android, минуя магазин приложений.

## <a name="publishing-an-applicationandroiddeploy-testpublishingindexmd"></a>[Публикация приложения](~/android/deploy-test/publishing/index.md)

В этой серии статей описано, как распространять приложения, созданные с помощью Xamarin.Android. Распространение может осуществляться по таким каналам, как электронная почта, частный веб-сервер, Google Play или Amazon App Store для Android.
