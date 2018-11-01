---
title: Требования к Xamarin.Forms
description: Требования к платформе и системные требования к разработке Xamarin.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 504f20f6575e559d7c4965643b74b407d5e84de8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112368"
---
# <a name="xamarinforms-requirements"></a>Требования к Xamarin.Forms

_Требования к платформе и системные требования к разработке Xamarin.Forms._

Обзор рекомендаций по установке и настройке, действующих для различных платформ, см. в статье [Установка](~/cross-platform/get-started/installation/index.md).

## <a name="target-platforms"></a>Целевые платформы

Приложения Xamarin.Forms могут быть написаны для следующих операционных систем:

- iOS 8 или более поздние версии;
- Android 4.4 (API 19) или более поздние версии ([подробнее](#android));
- универсальная платформа Windows для Windows 10 ([подробнее](#windows10));

Предполагается, что разработчики знакомы с [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) и [общими проектами](~/cross-platform/app-fundamentals/shared-projects.md).

### <a name="additional-platform-support"></a>Поддержка дополнительных платформ

Сведения о состоянии этих платформ можно найти на странице [Xamarin.Forms в GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen;
- macOS
- GTK#
- WPF

### <a name="platforms-from-earlier-versions"></a>Платформы для более ранних версий

Эти платформы не поддерживаются при использовании Xamarin.Forms 3.0:

- *Windows 8.1 или Windows Phone 8.1 WinRT;*
- *Windows Phone 8 Silverlight.*

### <a name="android"></a>Android

Необходимо установить последнюю версию Android SDK Tools и платформы API Android. Для обновления до последних версий можно воспользоваться [диспетчером пакетов SDK Android](~/android/get-started/installation/android-sdk.md).

Кроме того, в целевой версии или версии компиляции для проектов Android **должен** быть выбран параметр *Использовать самую новую установленную платформу*. Однако минимальной версией может быть API 19, что позволяет поддерживать устройства, использующие Android 4.4 и более поздние версии. Эти значения задаются в разделе **Параметры проекта**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Параметры проекта > Приложение > Свойства приложения**

![](installation-images/options-android-vs-sml.png "Параметры сборки Android в Visual Studio")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

**Сборка > Общие**

![](installation-images/options-general-sml.png "Сборка > Общие")

**Сборка > Приложение Android**

![](installation-images/options-android-sml.png "Сборка > Приложение Android")

-----

## <a name="development-system-requirements"></a>Требования к системе для разработки

Приложения Xamarin.Forms можно разрабатывать в ОС в macOS и Windows. Однако для создания Windows-версий приложения требуются Windows и Visual Studio.

## <a name="mac-system-requirements"></a>Требования к системе Mac

Visual Studio для Mac можно использовать для разработки приложений Xamarin.Forms в OS X El Capitan (10.11) или более поздних версиях. Для разработки приложений iOS рекомендуется установить по меньшей мере пакет SDK iOS 10 и Xcode 8.

> [!NOTE]
>  Приложения Windows нельзя разрабатывать в macOS.

## <a name="windows-system-requirements"></a>Требования к системе Windows

Приложения Xamarin.Forms для iOS и Android можно создавать в любой установке Windows, которая поддерживает возможности разработки Xamarin. Для этого в ОС Windows 7 и более поздних версиях следует установить Visual Studio 2017 или более позднюю версию. Для разработки в iOS требуется подключенный к сети компьютер Mac.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>Универсальная платформа Windows (UWP)

Для разработки приложений Xamarin.Forms для UWP требуются следующие компоненты:

- Windows 10 (рекомендуется Fall Creators Update)

- Visual Studio 2017

- [Пакет средств разработки Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Проекты UWP включены в решения Xamarin.Forms, созданные в Visual Studio 2017 (но не в Visual Studio для Mac).
[Приложение универсальной платформы Windows (UWP) можно добавить](~/xamarin-forms/platform/windows/installation/index.md) в существующее решение Xamarin.Forms в любое время.
