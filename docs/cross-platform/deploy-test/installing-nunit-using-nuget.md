---
title: Установка NUnit 2.6.4 с помощью NuGet
description: Этот документ описывает переход с NUnit 3.0 на более раннюю версию NUnit 2.6.4 с помощью NuGet. Это необходимо при работе с Xamarin.UITest, который не поддерживает NUnit 3.x.
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: fb0ffd87a61f20744e68eafdd7eb8de691df6f75
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781524"
---
# <a name="installing-nunit-264-using-nuget"></a>Установка NUnit 2.6.4 с помощью NuGet

_В этом руководстве содержатся сведения о переходе с NUnit 3.0 на NUnit 2.6.4 (более раннюю версию) с помощью NuGet._

Разработчики, которые пишут тесты в Visual Studio для Mac или применяют Xamarin.UITest, должны использовать [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4), так как NUnit 3.0 или более поздние версии не совместимы с Visual Studio для Mac или Xamarin.UITest. Попытки запустить модульные тесты в Visual Studio для Mac или Xamarin.UITests с NUnit 3.0 завершатся ошибкой.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

В этом руководстве описывается установка NUnit 2.6.4 с помощью NuGet в Visual Studio для Mac. При необходимости эти действия позволяют удалить NUnit 3.0.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В этом руководстве описывается переход с NUnit 3.0 на NUnit 2.6.4 (более раннюю версию) с помощью NuGet в Visual Studio 2015.

-----

## <a name="requirements"></a>Требования

В этом руководстве предполагается наличие существующего решения с проектом мобильного приложения и тестовым проектом.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>Установка NUnit 2.6.4 в Visual Studio для Mac

Далее приводятся действия по установке NUnit 2.6.4.


1. **Откройте диспетчер пакетов.** Щелкните правой кнопкой мыши **Пакеты** и во всплывающем меню выберите пункт **Добавить пакеты**:

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "Щелчок элемента \"Пакеты\" правой кнопкой мыши и выбор пункта \"Добавить пакеты\" во всплывающем меню")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **Выполните поиск `NUnit version:2.6.4`.** Visual Studio для Mac удалит NUnit 3.0 (при необходимости) и затем скачает и установит NUnit 2.6.4. В диалоговом окне **Добавление пакетов** в поле **Найти**, расположенном в правом верхнем углу окна, введите `nunit version:2.6.4`. В результатах поиска выберите **NUnit** и нажмите кнопку **Добавить пакет**:

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "Выбор NUnit в результатах поиска и нажатие кнопки \"Добавить пакет\"")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)


Чтобы проверить, установлен ли NUnit 2.6.4, можно просмотреть номер версии пакета NUnit на панели решения:

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "Проверка номера версии пакета NUnit на панели решения")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>Сводка

В этом руководстве рассматривался переход с NUnit 3.0 на NUnit 2.6.4 (более раннюю версию) в Visual Studio для Mac с помощью консоли диспетчера пакетов.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installing-nunit-264-in-visual-studio"></a>Установка NUnit 2.6.4 в Visual Studio

В этом разделе рассматривается использование _консоли диспетчера пакетов NuGet_ в Visual Studio 2015 для удаления NUnit 3.0 и установки NUnit 2.6.4.


1. **Откройте консоль диспетчера пакетов NuGet.** Последовательно выберите **Сервис > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**:

    [![](installing-nunit-using-nuget-images/package-manager-console.png "Открытие консоли диспетчера пакетов NuGet. Последовательный выбор пунктов \"Сервис\" > \"Диспетчер пакетов NuGet\" > \"Консоль диспетчера пакетов\"")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **Проверьте версию NUnit.** При необходимости можно проверить установленную версию NUnit, выполнив команду `Get-Package -Project <UITEST PROJECT>`:

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

Если вы отображается NUnit 3.0 или более поздняя версия, необходимо перейти на версию NUnit 2.6.4.

1. **Удалите NUnit 3.0.** Выполните командлет `Uninstall-Package`, чтобы удалить NUnit 3.0:

        <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Resolving actions to uninstall package 'NUnit.3.0.1'
        Resolved actions to uninstall package 'NUnit.3.0.1'
        Removed package 'NUnit.3.0.1' from 'packages.config'
        Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>

1. **Установите NUnit 2.6.4.** Установите Nunit 2.6.4 с помощью командлета `Install-Package`, как показано в следующем фрагменте:

        <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
        Resolving actions to install package 'NUnit.2.6.4'
        Resolved actions to install package 'NUnit.2.6.4'
        Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to 'packages.config'
        Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    
## <a name="summary"></a>Сводка

В этом руководстве рассматривался переход с NUnit 3.0 на NUnit 2.6.4 (более раннюю версию) в Visual Studio 2015 с помощью консоли диспетчера пакетов.

-----

## <a name="related-links"></a>Связанные ссылки

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)
- [Пакет NuGet для NUnit 2.6.4](https://www.nuget.org/packages/NUnit/2.6.4)
