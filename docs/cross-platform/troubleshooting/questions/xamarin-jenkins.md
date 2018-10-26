---
title: Почему Jenkins не поддерживается корпорацией Майкрософт?
description: Этот документ описывает на высоком уровне, Xamarin взаимодействия с системой непрерывной Интеграции Jenkins. Здесь также рассматриваются несколько распространенных проблем, которые возникают при работе с Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: c2e409b796d5ef2525079e02aafdd0c6e8db5d81
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113447"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Почему Jenkins не поддерживается корпорацией Майкрософт?

## <a name="jenkins-support-explanation"></a>Описание поддержки Jenkins

Jenkins — это набор CI открытым исходным кодом; из-за количества проблем, вызванных непосредственно Jenkins *сам* нужно будет отправить как проблемы, с которой был взят код, например [основного репозитория Jenkins](https://github.com/jenkinsci/jenkins), или из репозитория для [ Jenkins.App](https://github.com/stisti/jenkins-app).

Исключением из этого является о проблемах, которые могут быть изолированы для конкретной ошибки в средствами Xamarin; Если вы подозреваете, что это так, вы можете проверить ваш [варианты поддержки](~/cross-platform/troubleshooting/support-options.md), хотя опять же, проблема может быть что-то вне поддержка Xamarin можно team *непосредственно* помощь.

## <a name="setup-jenkins-with-xamarin"></a>Настройка Jenkins с Xamarin

Как отмечалось выше проблемы Jenkins не поддерживается напрямую нашей группой; [с помощью Jenkins с Xamarin](~/tools/ci/jenkins-walkthrough.md) руководство позволяют настроить сервер Jenkins CI, интегрированного с помощью Xamarin. 

## <a name="fixes-for-common-issues"></a>Исправления для распространенных проблем

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins — не удается найти пакет SDK для Android

Сообщение об ошибке для этой проблемы является примерно следующее:

> Ошибка XA5205: не удалось найти каталог Android SDK. Задайте с помощью /p:AndroidSdkDirectory

Параметры, позволяющие Задание расположения пакета SDK могут различаться в зависимости точное подключаемый модуль Jenkins Android, которую вы используете; хорошим местом для поиска способах настройки находится в руководство по подключаемому модулю. Например, если для [Android Emulator подключаемый модуль](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) автоматически ищет пакета SDK, но если его не удается найти; также можно задать расположение на странице конфигурации системы Jenkins для этого подключаемого модуля. 


## <a name="deprecated-errors"></a>Устаревшие ошибки

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins сообщает недействительную лицензию Xamarin
Сообщения об ошибках для этой проблемы обычно являются примерно так

> Ошибка XA9008: сборки из командной строки требуется лицензия для бизнеса

или

> Ошибка: Starter Edition Xamarin.iOS не поддерживает сборку вне Xamarin Studio 

Наиболее распространенной причиной этого сценария является использование Jenkins с помощью учетной записи пользователя, не связанные с лицензией Xamarin. Разрешения, проще всего установить Jenkins в качестве приложения непосредственно с использованием учетной записи пользователя. Этот процесс и некоторые другие аспекты описаны здесь: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
