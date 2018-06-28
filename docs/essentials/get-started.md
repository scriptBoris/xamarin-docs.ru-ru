---
title: Приступая к работе с Xamarin.Essentials
description: Xamarin.Essentials предоставляет один кросс платформенных API, который работает с iOS, Android или UWP приложения, которое может осуществляться из общего кода независимо от того, как создается пользовательский интерфейс.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a42086f70eb81a761358655b3effb9f8f934c8d4
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067383"
---
# <a name="get-started-with-xamarinessentials"></a>Приступая к работе с Xamarin.Essentials

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials предоставляет один кросс платформенных API, который работает с iOS, Android или UWP приложения, которое может осуществляться из общего кода независимо от того, как создается пользовательский интерфейс.

## <a name="platform-support"></a>Поддержка платформ

Xamarin.Essentials поддерживает следующие платформы и операционные системы:

| Platform | Версия |
| --- | --- |
| Android | 4.4 (API 19) или более поздней версии |
| iOS |10.0 или более поздней версии |
| UWP | 10.0.16299.0 или более поздней версии |

## <a name="installation"></a>Установка

Xamarin.Essentials доступна как пакет NuGet, который может быть добавлен в существующий или новый проект, с помощью Visual Studio.

1. Загрузите и установите [Visual Studio](http://visualstudio.com) с [средств Visual Studio для Xamarin](~/cross-platform/get-started/installation/index.md).

2. Откройте существующий проект или создайте новый проект с помощью шаблона пустого приложения под **Visual Studio C#** (Android, iPhone и iPad или кросс-платформенных). **Важные**: при добавлении в проект UWP убедитесь сборки 16299 или более поздней версии задано в свойствах проекта.

3. Добавить **Xamarin.Essentials** пакет NuGet для каждого проекта:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    На панели обозревателя решений щелкните правой кнопкой мыши имя решения и выберите **управление пакетами NuGet**. Поиск **Xamarin.Essentials** и установки пакета **всех** проектов, включая Android, iOS, UWP и .NET стандартные библиотеки.

    > [!TIP]
    > Проверьте **включить предварительный выпуск** поле при [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) находится в предварительной версии.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

    На панели обозревателя решений щелкните правой кнопкой мыши имя проекта и выберите **Добавить > Добавление пакетов NuGet...** . Поиск **Xamarin.Essentials** и установки пакета **всех** проектов, включая Android, iOS и .NET стандартные библиотеки.

    > [!TIP]
    > Проверьте **Показать пакеты предварительного выпуска** поле при [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) находится в предварительной версии.

    -----

4. Добавьте ссылку на Xamarin.Essentials любого класса C# для ссылки на API-интерфейсы.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials требуется настройка платформ:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials поддерживает минимальной версии Android 4.4, соответствующий уровень API 19, но целевой версии Android для компиляции должны быть 8.1, соответствующий уровню API 27. (В Visual Studio, эти две версии устанавливаются в диалоговом окне свойств проекта для проекта Android, на вкладке Android манифеста. В Visual Studio для Mac их всегда можно изменить в диалоговом окне Параметры проекта для проекта Android, на вкладке приложения Android.) 
    
    Xamarin.Essentials устанавливает версию 27.0.2 Xamarin.Android.Support библиотек, которые необходимы. Другие Xamarin.Android.Support библиотеки, которые требуются приложению, также должен быть обновлен до версии 27.0.2, с помощью диспетчера пакетов NuGet. Все библиотеки Xamarin.Android.Support, используемым в приложении должны быть одинаковыми и должна быть не меньше версии 27.0.2. Ссылаться на [страницу устранения неполадок](troubleshooting.md) при возникновении проблем Добавление Xamarin.Essentials NuGet или обновление NuGets в решении.

    В проекте Android `MainLauncher` или `Activity` , запущенное Xamarin.Essentials должны быть инициализированы в `OnCreate` метод:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Для обработки разрешения среды выполнения на Android, Xamarin.Essentials должны отправляться `OnRequestPermissionsResult`. Добавьте следующий код для всех `Activity` классы:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    Дополнительная настройка не требуется.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    Дополнительная настройка не требуется.

    -----

6. Выполните [Xamarin.Essentials направляющие](index.md) , позволяющие копировать и вставлять фрагменты кода для каждого компонента.

## <a name="other-resources"></a>Другие ресурсы

Мы рекомендуем разработчикам, которые знакомы с Xamarin посещение [начало разработки Xamarin](~/cross-platform/getting-started/index.md).

Посетите [репозитория GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) для просмотра текущего исходного кода, предстоящие Далее, запустите образцы и закройте хранилище. Материалы сообщества Добро пожаловать!

Просмотрите [документации по API](xref:Xamarin.Essentials) для каждого компонента Xamarin.Essentials.
