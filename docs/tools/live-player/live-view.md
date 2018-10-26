---
redirect_url: /xamarin/tools/live-player/
title: XAML Live предварительного просмотра
description: В этом документе описывается использование Xamarin Live Player live страницы XAML предварительной версии и внести изменения в XAML см. в разделе изменения сразу же отображаться на устройстве.
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 1602c98eceaff607c79400a37c4ace60d5bf8807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110600"
---
# <a name="xaml-live-previewing"></a>XAML Live предварительного просмотра

Одним из преимуществ Xamarin Live Player является возможность динамической страницы XAML предварительной версии и внесите изменения в код в Visual Studio см. в разделе изменения сразу же отображаться на устройстве. Динамический просмотр можно сделать на устройстве Android или в симуляторе или эмуляторе. В этом руководстве показано, как использовать функцию динамического просмотра для просмотра отдельных экранов XAML.

## <a name="requirements"></a>Требования

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Компьютер под управлением Windows 7 или более поздней версии.
2. Visual Studio 2017 версии 15.4 или более поздней версии с **разработки мобильных приложений на .NET** установленной рабочей нагрузкой.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Компьютер Mac с OS X 10.11 macOS версии 10.12 или более поздней версии.
2. Visual Studio для Mac 7.2 или более поздней версии. Мы рекомендуем использовать последнюю версию.

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>Развертывание на устройстве

Прежде чем использовать Xamarin Live Player с устройства Android, вам потребуется скачать приложение Xamarin Live Player и свяжите его с Visual Studio, как описано в разделе [установить](~/tools/live-player/install.md) руководства. После успешного подключения устройства к Visual Studio можно начать интерактивный просмотр страницы XAML. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Откройте страницу XAML, который требуется динамического просмотра в редакторе Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Задайте конфигурацию устройства **Отладка** и выберите устройство, Live Player из списка:

    ![](live-view-images/vs-image2.png)

3. Чтобы запустить эту страницу XAML как динамического представления на вашем устройстве, выберите **средства > Xamarin Live Player > Live текущего представления запуска** в строке меню:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Откройте страницу XAML, который вы хотите динамического просмотра, в Visual Studio для Mac редактора:

    ![](live-view-images/image1.png)

2. Задайте конфигурацию устройства **Отладка** и выберите устройство, Live Player из списка:

    ![](live-view-images/image2.png)

3. Чтобы запустить эту страницу XAML как динамического представления на вашем устройстве, выберите **запуска > Live текущего представления запуска** в строке меню:

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>Развертывание в эмуляторе Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Откройте страницу XAML, который требуется динамического просмотра в редакторе Visual Studio 2017:

    ![](live-view-images/vs-image1.png)

2. Задайте конфигурацию устройства **Отладка** для Android и выберите устройство, которое Live Player из списка:

    ![](live-view-images/vs-image4.png)

3. Чтобы запустить эту страницу XAML как динамического представления в эмуляторе Android, выберите **средства > Xamarin Live Player > Live текущего представления запуска** в строке меню:

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Откройте страницу XAML, который вы хотите динамического просмотра, в Visual Studio для Mac редактора:

    ![](live-view-images/image7.png)

2. Задайте конфигурацию устройства **Отладка** для Android и выберите устройство, которое Live Player из списка:

    ![](live-view-images/image6.png)

3. Чтобы запустить эту страницу XAML как динамического представления на вашем устройстве, выберите выполнение > Live текущего представления запуска в строке меню:

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>Связанные ссылки

- [Общие сведения о Xamarin Live Player](https://xamarin.com/live)
- [Запись блога](https://blog.xamarin.com/live-player/)
- [Образцы Xamarin Live Player](~/tools/live-player/samples.md)
