---
redirect_url: /xamarin/cross-platform/troubleshooting/questions/
title: Можно использовать Visual Studio, версия-кандидат 2017 г. с помощью Xamarin
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4c93653793c7c8cce0d2257107097c89f73fc726
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33946724"
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>Можно использовать Visual Studio, версия-кандидат 2017 г. с помощью Xamarin

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>Можно использовать Visual Studio, версия-кандидат 2017 г. с помощью Xamarin

Да. Имеется возможность использовать Xamarin до версии-кандидата Visual Studio 2017 г. Тем не менее Обратите внимание, что в настоящее время установки Xamarin для Visual Studio RC 2017 г удалит все предыдущие версии Xamarin установлены в Visual Studio 2015 июля 2013 г. Xamarin для Visual Studio 2017 г может не устанавливаться одновременно Xamarin для Visual Studio 2015/2013 в результате Visual Studio 2017 г. переход от пакета MSI сторону использования системы установщик Visual Studio.

Хотя команда в данный момент рассмотреть способы для обхода этой проблемы, мы рекомендуем пользователям выбирать среды разработки на основе своих требований. 

> [!IMPORTANT]
> При построении проектов Xamarin.iOS, убедитесь, что Visual Studio для Mac на компьютере Mac парной — та же версия Xamarin.iOS канала.

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>Как установить Xamarin для Visual Studio, версия-кандидат 2017 г.

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>Установка во время установщик версии-Кандидата Visual Studio 2017 г.

* Выберите **Xamarin** компонента в рамках нового **установщик Visual Studio**

  [![](visualstudio-2017-rc-images/install1-sml.png "Экран версии-Кандидата установщика Visual Studio 2017 г.")](visualstudio-2017-rc-images/install1-orig.png#lightbox)

Расширение Visual Studio для разработки Xamarin.iOS и Xamarin.Android будут установлены.

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>Установка или переустановить Xamarin в существующую установку версии-Кандидата Visual Studio 2017 г.

#### <a name="using-the-visual-studio-installer"></a>С помощью установщика Visual Studio:

1. Поиск приложений установщик Visual Studio

  [![](visualstudio-2017-rc-images/reinstall1-sml.png "Результаты поиска для приложения установщика Visual Studio")](visualstudio-2017-rc-images/reinstall1-orig.png#lightbox)

2. Выберите:. **Разработка мобильных приложений в .NET Framework (Предварительная версия)** на вкладке рабочих нагрузок или

  [![](visualstudio-2017-rc-images/reinstall2-sml.png "Вкладка рабочих нагрузок установщика VS") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** в **отдельные компоненты** вкладка

  [![](visualstudio-2017-rc-images/reinstall3-sml.png "Откройте вкладку компоненты установщика VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>С помощью установщика Visual Studio в Visual Studio:
1. Перейдите к начальной страницы Visual Studio 2017 г.
2. Щелкните **более шаблоны проектов** под **новый проект** раздела

    [![](visualstudio-2017-rc-images/reinstall4-sml.png "Начальной страницы Visual Studio")](visualstudio-2017-rc-images/reinstall4-orig.png#lightbox)
3. Щелкните `Open Visual Studio Installer` на левой панели

    [![](visualstudio-2017-rc-images/reinstall5-sml.png "Экран нового проекта")](visualstudio-2017-rc-images/reinstall5-orig.png#lightbox)
4. Выберите:
    
    1. **Разработка мобильных приложений в .NET Framework (Предварительная версия)** на вкладке рабочих нагрузок или

    [![](visualstudio-2017-rc-images/reinstall2-sml.png "Вкладка рабочих нагрузок установщика VS") ](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b. **Xamarin** в **отдельные компоненты** вкладка

    [![](visualstudio-2017-rc-images/reinstall3-sml.png "Откройте вкладку компоненты установщика VS")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)
