---
title: Отсутствуют расширения Visual Studio после установки
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 066d36a3-e553-48d6-8769-c972274d7641
author: asb3993
ms.author: amburns
ms.date: 03/20/2017
ms.openlocfilehash: 7b1f96807d77d9db0a892c5e78124eb3a9890edc
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251225"
---
# <a name="missing-visual-studio-extensions-after-installation"></a>Отсутствуют расширения Visual Studio после установки

## <a name="error-message-this-project-is-incompatible-with-the-current-edition-of-visual-studio"></a>Сообщение об ошибке: Этот проект несовместим с текущим выпуском Visual Studio

Убедитесь, что установить совместимую версию Visual Studio:

-   Visual Studio 2017 (Community, Professional или Enterprise)
-   Visual Studio 2015 (Community, Professional или Enterprise)

См. также [требования к Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

## <a name="possible-fix-1-change-the-installation-to-make-sure-the-visual-studio-extensions-are-installed"></a>Возможные исправления 1: Измените параметры установки, чтобы убедиться в том, что установлены соответствующие расширения Visual Studio

В некоторых случаях установщика Xamarin могут автоматически отмените параметры установки для расширений Visual Studio. Если, являющееся причиной проблемы, установите отсутствуют расширения Visual Studio, с помощью установщика **изменение** команды. Например, чтобы установить расширения для Visual Studio 2013:

1. Откройте Windows **программы и компоненты** панель управления.

2. Щелкните правой кнопкой мыши **Xamarin** запись и выберите **изменение**.

3. Нажмите кнопку **Далее**, затем **изменение**.

4. Убедитесь, что **Xamarin для Visual Studio 2013** включен режим установки:

    ![](missing-vs-extensions-images/installer.png "Включить Xamarin для варианта установки Visual Studio 2013")

5. Пройдите остальные инструкции мастера установки.

## <a name="possible-fix-2-ask-visual-studio-to-set-up-the-extensions-again"></a>Возможному исправлению 2: Visual Studio для настройки расширений попросите

1. Проверьте, если расширения Xamarin были скопированы в папку расширений Visual Studio:

    `C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\Extensions\Xamarin\Xamarin\3.1.228.0`

    Если расширения установлены правильно (для версии 3.1.228), в папке будет 60 элементов:


    ![](missing-vs-extensions-images/folder.png "Список содержимого папки «Xamarin\3.1.228.0» в обозревателе")

2. После подтверждения того, что эта папка выглядит правильно, дайте команду Visual Studio, чтобы повторить попытку установки расширения:

    `"C:\Program Files (x86)\Microsoft Visual Studio 12.0\Common7\IDE\devenv.exe" /setup`

## <a name="possible-fix-3-try-a-fresh-reinstall-of-xamarin"></a>Возможные варианты исправления 3: Попробуйте новую переустановки Xamarin

1.  С помощью панели управления Windows удалите любые из следующих имеющихся:

    *   Xamarin

    *   Xamarin для Windows

    *   Xamarin.Android

    *   Xamarin.iOS

    *   Xamarin для Visual Studio

2.  В обозревателе удалите все оставшиеся файлы из папок расширения Xamarin для Visual Studio (все версии, включая **Program Files** и **Program Files (x86)**):

    `C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin`

3.  Также проверьте в каталоге «VirtualStore», чтобы увидеть, если может быть любой копии «наложения» любого расширения каталога:

    `%LOCALAPPDATA%\VirtualStore`

4.  Откройте редактор реестра (regedit).

5.  Найдите следующий раздел.

    _Открываемый раздел HKEY\_ЛОКАЛЬНОГО\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls_

6.  Найдите и удалите все записи, соответствующие этому шаблону.

    _C:\Program Files\*\Microsoft Visual Studio 1\*.0\Common7\IDE\Extensions\Xamarin_

7.  Найдите следующий раздел.

    `HKEY\_CURRENT\_USER\Software\Microsoft\VisualStudio\1\*.0\ExtensionManager\PendingDeletions`

8.  Удалите все записи, которые могут иметь отношение к Xamarin. Например ниже приведен один, можно вызвать проблемы в более старых версиях Xamarin:

    _Mono.VisualStudio.Shell,1.0_

9.  Перезагрузите систему.

10.  Переустановите текущей стабильной версии Xamarin из [visualstudio.com](https://visualstudio.com/xamarin).

## <a name="possible-fix-4-repair-visual-studio-installation"></a>Возможному исправлению 4: Установки Visual Studio восстановить

1.  Откройте Windows **программы и компоненты** панель управления.

2.  Соответствующая запись Microsoft Visual Studio щелкните правой кнопкой мыши и выберите **изменений**

3.  Нажмите кнопку **восстановления** кнопку в Открывшееся диалоговое окно Visual Studio.
