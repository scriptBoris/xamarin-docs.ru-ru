---
title: Отсутствующие расширения Visual Studio после установки
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: e47cfc4de77a6310a81867eefb07c3c1e5cc7060
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917893"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Отсутствующие расширения Visual Studio после установки

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Сообщение об ошибке: Этот проект несовместим с текущим выпуском Visual Studio

Убедитесь, что установлена совместимая версия среды Visual Studio.

-   Visual Studio 2017 г. (Community, Professional или Enterprise)
-   Visual Studio 2015 (Community, Professional или Enterprise)

См. также [требования к Windows](~/cross-platform/get-started/requirements.md#windows).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Возможные исправления 1: Измените установки, чтобы убедиться в том, что установлены расширения Visual Studio

В некоторых случаях установщик Xamarin может автоматически снять параметров установки для расширений Visual Studio. Если это причиной проблемы, установите отсутствующие расширения Visual Studio, с помощью установщика **изменений** команды. Например, чтобы установить расширения для Visual Studio 2013:

1. Откройте Windows **программы и компоненты** панель управления.

2. Щелкните правой кнопкой мыши **Xamarin** входа, а затем выберите **изменений**.

3. Нажмите кнопку **Далее**, затем **изменений**.

4. Убедитесь, что **Xamarin для Visual Studio 2013** включен режим установки:

    ![](missing-vs-extensions-images/installer.png "Включить Xamarin для варианта установки Visual Studio 2013")

5. Следуйте указаниям мастера установки до конца.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Возможные варианты исправления 2: Попросите Visual Studio для настройки расширений

1. Проверьте, если расширения Xamarin были скопированы в папку расширений Visual Studio.

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Если расширения установлены правильно (для версии 3.1.228), в папке будет 60 элементов:


    ![](missing-vs-extensions-images/folder.png "Список содержимого папки «Xamarin\3.1.228.0» в обозревателе")

2. После подтверждения того, что эта папка выглядит правильно, указать Visual Studio, чтобы повторить попытку установки расширения:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Возможные варианты исправления 3: Попробуйте новую переустановка Xamarin

1.  С помощью панели управления Windows можно удалите любую из следующих строк, присутствующих:

    *   Xamarin

    *   Xamarin для Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin для Visual Studio

2.  В обозревателе, удалить все оставшиеся файлы из папки расширений Visual Studio с Xamarin (все версии, включая оба **Program Files** и **Program Files (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  Также проверьте в каталоге «VirtualStore», чтобы узнать, не все копии «наложения» любого расширения каталога:

    `%LOCALAPPDATA%\VirtualStore`

4.  Откройте редактор реестра (regedit).

5.  Найдите следующий раздел.

    _Раздел HKEY\_ЛОКАЛЬНОГО\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  Найдите и удалите все записи, соответствующие этому шаблону.

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  Найдите следующий раздел.

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  Удалите все записи, которые могут иметь отношение к Xamarin. Например вот, использовать стать причиной проблем в более старых версий Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9.  Перезагрузите систему.

10.  Переустановите текущей стабильной версии Xamarin из [visualstudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Невозможно исправить 4: Установка Visual Studio восстановления

1.  Откройте Windows **программы и компоненты** панель управления.

2.  Щелкните правой кнопкой мыши соответствующую операцию Microsoft Visual Studio и выберите **изменений**

3.  Нажмите кнопку **восстановления** кнопки в открывшемся диалоговом окне Visual Studio.
