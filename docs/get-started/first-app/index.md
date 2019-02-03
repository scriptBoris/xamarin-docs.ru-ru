---
title: Создание первого приложения Xamarin.Forms
description: Видеоруководство, показывающее, как создать первое приложение Xamarin.Forms в Visual Studio.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 02/02/2019
ms.openlocfilehash: 8adf52d8e08dedeebae8362cf730e5dc3c7c93a9
ms.sourcegitcommit: 9dcb7377dc92ad921285fbb857b0be13030bbea3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668567"
---
# <a name="build-your-first-xamarinforms-app"></a>Создание первого приложения Xamarin.Forms

_Просмотрите это видео и следуйте инструкциям, чтобы создать свое первое приложение для мобильных устройств с помощью Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Пошаговые инструкции для Windows

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Выполните следующие действия, как показано в видео.

1. Выберите **Файл > Создать > Проект...** или нажмите кнопку **Создать проект...**, а затем выберите **Visual C# > Кроссплатформенные > Мобильное приложение (Xamarin.Forms)**:

    [![Мобильное приложение (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Убедитесь, что выбраны **Android** и **iOS** с совместным использованием кода **.NET Standard**:

    [![Android и iOS с .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Подождите, пока восстанавливаются пакеты NuGet (в строке состояния появится сообщение "Восстановление завершено").

4. Запустите эмулятор Android, нажав кнопку отладки (или пункт меню **Отладка > Начать отладку**).

5. Измените **MainPage.xaml**, добавив этот XAML до конца `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Измените **MainPage.xaml**, добавив этот код до конца класса:

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Отладка приложения на Android:

    ![Приложение Android](images/win/07-sml.png)

    > [!TIP]
    > Можно создавать и отлаживать приложения iOS из Visual Studio с компьютером Mac, подключенным к сети. Дополнительные сведения см. в [инструкциях по установке](~/ios/get-started/installation/windows/index.md).

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Пошаговые инструкции для Mac

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

Выполните следующие действия, как показано в видео.

1. Выберите **Файл > Создать решение...** или нажмите кнопку **Создать проект...** и выберите **Многоплатформенность > Приложение > Приложение с пустыми формами**:

    [![Приложение с пустыми формами](images/01-sml.png)](images/01.png#lightbox)

2. Убедитесь, что выбраны **Android** и **iOS** с совместным использованием кода **.NET Standard**:

    [![Android и iOS с .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Восстановите пакеты NuGet, щелкнув правой кнопкой решение:

    ![Приложение Android](images/03-sml.png)

4. Запустите эмулятор Android, нажав кнопку отладки (или **Запуск > Начать отладку**).

5. Измените **MainPage.xaml**, добавив этот XAML до конца `</StackLayout>`:

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Измените **MainPage.xaml**, добавив этот код до конца класса:

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Отладка приложения на Android:

    ![Приложение Android](images/07-sml.png)

8. Щелкните правой кнопкой мыши, чтобы настроить iOS на **Запускаемый проект**:

    [![Задание запускаемого проекта в iOS](images/08-sml.png)](images/08.png#lightbox)

9. Отладка приложения в iOS:

    ![Приложение iOS](images/09-sml.png)

::: zone-end

Загрузите полный код из [коллекции примеров](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/) или просмотрите его на [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Следующие шаги

- [Единый страницу быстрого запуска](~/get-started/quickstarts/single-page.md) &ndash; создать более функциональное приложение.
- [Примеры Xamarin.Forms](~/xamarin-forms/samples/index.yml) &ndash; загрузка и запуск примеров кода и примеров приложений.
- [Электронная книга "Создание мобильных приложений"](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; подробное описание разработки в Xamarin.Forms в виде PDF с сотнями дополнительных примеров.
