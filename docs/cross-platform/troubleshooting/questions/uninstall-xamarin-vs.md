---
title: Как выполнять тщательную удаление для Xamarin для Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 99fde9330498ee62d3cf6b5910c2cbfae39cfdeb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917672"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Как выполнять тщательную удаление для Xamarin для Visual Studio?


1.  С помощью панели управления Windows можно удалите любую из следующих строк, присутствующих:

    -   Xamarin
    -   Xamarin для Windows
    -   Xamarin.Android
    -   Xamarin.iOS
    -   Xamarin для Visual Studio

2.  В обозревателе, удалить все оставшиеся файлы из папки расширений Visual Studio с Xamarin (все версии, включая оба _Program Files_ и _Program Files (x86)_):

    _C:\\программные файлы\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\расширения\\Xamarin_

3.  Удалите каталог кэша компонент MEF Visual Studio также:

    _% LOCALAPPDATA %\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    На самом деле этот шаг сам по себе, обычно достаточно для устранения ошибок, таких как:

    -   «Пакет «XamarinShellPackage» не был правильно загружен»

    -   «… Файл проекта не может быть открыт. Нет отсутствующих подтипом проекта»

    -   «Ссылка на объект не указывает на экземпляр объекта.  в Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()»

    -   «Ошибка SetSite пакета» (в Visual Studio _ActivityLog.xml_)

    -   «Ошибка LegacySitePackage пакета» (в Visual Studio _ActivityLog.xml_)

    (См. также [очистить кэш компонент MEF](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) расширение Visual Studio.  И в разделе [ошибки 40781 комментарий 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) для контекста немного больше о вышестоящих проблеме в Visual Studio, которая может вызвать эти ошибки.)

4.  Также проверьте _VirtualStore_ каталог и проверяет, если Windows могут сохранять любые наложения файлы для _расширения\\Xamarin_ или _ComponentModelCache_существует каталоги:

    _% LOCALAPPDATA %\\VirtualStore_

5.  Откройте редактор реестра (`regedit`).

6.  Найдите следующий раздел:

    _Раздел HKEY\_ЛОКАЛЬНОГО\_МАШИНЫ\\программного обеспечения\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7.  Найдите и удалите все записи, соответствующие этому шаблону.

    _C:\\программные файлы\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\расширения\\Xamarin_

8.  Найдите следующий раздел.

    _Раздел HKEY\_текущей\_пользователя\\программного обеспечения\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9.  Удалите все записи, которые могут иметь отношение к Xamarin.  Например вот, использовать стать причиной проблем в более старых версий Xamarin:

    _Mono.VisualStudio.Shell,1.0_

10. Откройте администратор `cmd.exe` командную строку, а затем запустите `devenv /setup` и `devenv /updateconfiguration` команды для каждой установленной версии Visual Studio.  Например, для Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Перезагрузите систему.

12. Переустановите текущей стабильной версии с помощью Xamarin из [visualstudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Дополнительные действия по устранению неполадок для «пакет не был правильно загружен»

В случаях, где указанные выше шаги не устранить ошибку «пакет не был правильно загружен» Здесь приведены несколько дополнительных действий, чтобы повторить.

1.  Создайте новую учетную запись пользователя Windows.

2.  Проверьте, если расширения Xamarin для Visual Studio загрузить без ошибок для нового пользователя.

3.  Если правильно загрузить расширения, проблема скорее связана некоторыми сохраненные параметры для исходного пользователя:

    -   **В обозревателе** — _% LOCALAPPDATA %\\Microsoft\\VisualStudio\\1\*.0_
    -   **В редакторе реестра** — _HKEY\_текущей\_пользователя\\программного обеспечения\\Microsoft\\VisualStudio\\1\*.0_
    -   **В редакторе реестра** — _HKEY\_текущей\_пользователя\\программного обеспечения\\Microsoft\\VisualStudio\\1\*.0\_конфигурации_

4.  Если эти сохраненные параметры на самом деле указаны проблемы, можно попробовать их резервное копирование и их удаления.
