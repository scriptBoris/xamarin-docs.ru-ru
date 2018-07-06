---
title: Начало работы с Xamarin.Essentials
description: Xamarin.Essentials предоставляет один кросс платформенных API, который работает с iOS, Android и универсальной платформы Windows приложение, которое может осуществляться из общего кода независимо от того, как создается пользовательский интерфейс.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e371a6125d223d354b75ce7e09dcc28efb3dffa
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855238"
---
# <a name="get-started-with-xamarinessentials"></a>Начало работы с Xamarin.Essentials

![Предварительные версии NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials предоставляет один кросс платформенных API, который работает с iOS, Android и универсальной платформы Windows приложение, которое может осуществляться из общего кода независимо от того, как создается пользовательский интерфейс.

## <a name="platform-support"></a>Поддержка платформ

Xamarin.Essentials поддерживает следующие платформы и операционные системы:

| Platform | Версия |
| --- | --- |
| Android | 4.4 (API 19) или более поздней версии |
| iOS |10.0 или более поздней версии |
| UWP | 10.0.16299.0 или более поздней версии |

## <a name="installation"></a>Установка

Xamarin.Essentials доступна как пакет NuGet, который может быть добавлен в существующем или новом проекте, с помощью Visual Studio.

1. Скачайте и установите [Visual Studio](http://visualstudio.com) с [средств Visual Studio для Xamarin](~/cross-platform/get-started/installation/index.md).

2. Откройте существующий проект или создайте новый проект, используя шаблон пустого приложения в разделе **Visual Studio C#** (Android, iPhone и iPad или кросс-платформенных). **Важные**: Если добавление в проект универсальной платформы Windows установите сборки 16299 или более поздней версии в свойствах проекта.

3. Добавить **Xamarin.Essentials** пакет NuGet для каждого проекта:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    На панели обозревателя решений, щелкнуть правой кнопкой мыши имя решения и выберите **управление пакетами NuGet**. Поиск **Xamarin.Essentials** и установите пакет в **все** проектов, включая Android, iOS, универсальной платформы Windows и .NET Standard библиотек.

    > [!TIP]
    > Проверьте **включить предварительные выпуски** поле при [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) находится в предварительной версии.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

    На панели обозревателя решений щелкните правой кнопкой мыши имя проекта и выберите **Добавить > Добавить пакеты NuGet...** . Поиск **Xamarin.Essentials** и установите пакет в **все** проектов, включая Android, iOS и .NET Standard библиотек.

    > [!TIP]
    > Проверьте **Показать пакеты предварительного выпуска** поле при [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) находится в предварительной версии.

    -----

4. Добавьте ссылку на Xamarin.Essentials в любом классе C# для ссылки на API-интерфейсы.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials требует установки платформы:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials поддерживает Минимальная версия Android 4.4, соответствующий уровень API 19, но целевая версия Android для компиляции должен быть 8.1, соответствующий API уровня 27. (В Visual Studio, эти две версии задаются в диалоговом окне свойств проекта для проекта Android, на вкладке "манифест Android". В Visual Studio для Mac их всегда можно изменить в диалоговом окне параметров проекта для проекта Android, на вкладке "приложение Android".) 
    
    Xamarin.Essentials устанавливается версия 27.0.2.1 Xamarin.Android.Support библиотек, которые необходимы. Другие библиотеки Xamarin.Android.Support, которые требуются приложению также должны обновляться до версии 27.0.2.1, с помощью диспетчера пакетов NuGet. Все библиотеки Xamarin.Android.Support, используемый в приложении должны быть одинаковыми и должен быть по крайней мере версии 27.0.2.1. Ссылаться на [страница устранения неполадок](troubleshooting.md) при возникновении проблем, добавление Xamarin.Essentials NuGet или обновление пакеты NuGet в решении.

    В проекте Android `MainLauncher` или любой `Activity` то есть запущенное Xamarin.Essentials должны быть инициализированы в `OnCreate` метод:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Для обработки разрешения среды выполнения на устройстве Android, Xamarin.Essentials должен получить любой `OnRequestPermissionsResult`. Добавьте следующий код для всех `Activity` классы:

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

6. Выполните [Xamarin.Essentials направляющие](index.md) , которые позволяют копировать и вставлять фрагменты кода для каждого компонента.

## <a name="other-resources"></a>Другие ресурсы

Мы рекомендуем разработчикам приходилось посещение Xamarin [Приступая к разработке приложений Xamarin](~/cross-platform/getting-started/index.md).

Посетите [репозиторий GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) определения текущего исходный код, что дальше, выполнение примеров и закройте хранилище. Приветствуются предложения участников сообщества!

Просмотрите [документации по API](xref:Xamarin.Essentials) для каждого компонента Xamarin.Essentials.
