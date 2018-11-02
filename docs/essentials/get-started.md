---
title: Начало работы с Xamarin.Essentials
description: Xamarin.Essentials обеспечивает единый кроссплатформенных API-интерфейс, который предоставляет доступ из общего кода для любого приложения iOS, Android и универсальной платформы Windows независимо от используемого метода создания пользовательского интерфейса.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 08/08/2018
ms.openlocfilehash: 78b7235d8c9e45c2179b1cca2827f45fe6edd8b2
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675111"
---
# <a name="get-started-with-xamarinessentials"></a>Начало работы с Xamarin.Essentials

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials обеспечивает единый кроссплатформенных API-интерфейс, который предоставляет доступ из общего кода для любого приложения iOS, Android и универсальной платформы Windows независимо от используемого метода создания пользовательского интерфейса.

## <a name="platform-support"></a>Поддержка платформ

Xamarin.Essentials поддерживает указанные ниже платформы и операционные системы.

| Platform | Версия |
| --- | --- |
| Android | 4.4 (API 19) или более поздней версии |
| iOS |10.0 или выше |
| UWP | 10.0.16299.0 или более поздней версии |

## <a name="installation"></a>Установка

Xamarin.Essentials предоставляется в виде пакета NuGet, который с помощью Visual Studio можно добавить в любой существующий или новый проект.

1. Скачайте и установите [Visual Studio](http://visualstudio.com) с помощью [средств Visual Studio для Xamarin](~/cross-platform/get-started/installation/index.md).

2. Откройте существующий проект или создайте новый, используя шаблон пустого приложения в разделе **Visual Studio C#** (для Android, для iPhone и iPad или кроссплатформенный). **Важно.** Если это проект универсальной платформы Windows, в свойствах проекта необходимо указать сборку 16299 или более поздней версии.

3. Добавьте пакет NuGet для **Xamarin.Essentials** в каждый из проектов:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    На панели обозревателя решений щелкните правой кнопкой мыши имя решения и выберите **Управление пакетами NuGet**. Найдите **Xamarin.Essentials** и установите пакет во **ВСЕ** проекты, в том числе для Android, iOS, универсальной платформы Windows и .NET Standard.

    > [!TIP]
    > Установите флажок **Включить предварительные выпуски**, если доступна только [предварительная версия пакета NuGet для **Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials).

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

    На панели обозревателя решений щелкните правой кнопкой мыши имя проекта и выберите **Добавить > Add NuGet Packages... (Добавить пакеты NuGet...)**. Найдите **Xamarin.Essentials** и установите пакет во **ВСЕ** проекты, в том числе для Android, iOS и .NET Standard.

    > [!TIP]
    > Установите флажок **Показать пакеты предварительного выпуска**, если [доступна только предварительная версия пакета NuGet для **Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials).

    -----

4. Добавьте ссылку на Xamarin.Essentials в любой класс C#, чтобы включить в него API-интерфейсы.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Для Xamarin.Essentials нужно настроить в соответствии с конкретной платформой:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    Xamarin.Essentials поддерживает Android начиная с минимальной версии 4.4, что соответствует уровню API 19. Но целевая версия Android для компиляции должна быть не ниже 8.1, что соответствует уровню API 27. (В Visual Studio эти две версии задаются в диалоговом окне свойств проекта для проекта Android, на вкладке "Манифест Android". В Visual Studio для Mac эти значения задаются в диалоговом окне свойств проекта для проекта Android, на вкладке "Приложение Android".) 

    Xamarin.Essentials устанавливает версию 27.0.2.1 всех требуемых библиотек Xamarin.Android.Support. Все другие библиотеки Xamarin.Android.Support, которые использует приложение, также следует обновить до версии 27.0.2.1 с помощью диспетчера пакетов NuGet. Все библиотеки Xamarin.Android.Support, используемые в приложении, должны иметь одну и ту же версию (не ниже версии 27.0.2.1). Если вы столкнетесь с проблемами при добавлении пакета NuGet для Xamarin.Essentials или при обновлении пакетов NuGet в решении, воспользуйтесь [страницей устранения неполадок](troubleshooting.md).

    В `MainLauncher` проекта Android или в любом запущенном действии `Activity` следует инициализировать Xamarin.Essentials в методе `OnCreate` следующим образом:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code
        //...
    ```

    Для обработки на устройстве Android разрешений среды выполнения Xamarin.Essentials нужно получить любой `OnRequestPermissionsResult`. Добавьте следующий код во все классы `Activity`:

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

6. Выполните инструкции, приведенные в [руководствах Xamarin.Essentials](index.md) по копированию и вставке фрагментов кода для каждого компонента.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials — кроссплатформенные API-интерфейсы для мобильных приложений (видео)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Другие ресурсы

Мы рекомендуем разработчикам, которые еще не работали с Xamarin, начать с [этой статьи](~/cross-platform/getting-started/index.md).

Посетите [репозиторий GitHub для Xamarin.Essentials](http://github.com/xamarin/Essentials), чтобы изучить актуальный исходный код, узнать ближайшие планы развития, выполнить примеры кода и клонировать репозиторий. Мы рады любому вкладу в сообщество!

Изучите [документацию по API](xref:Xamarin.Essentials) для любого компонента Xamarin.Essentials.
