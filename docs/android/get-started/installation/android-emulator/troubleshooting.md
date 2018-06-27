---
title: Устранение неполадок при настройке эмулятора
description: Эта статья описывает, как диагностировать и устранять неполадки, которые могут возникать при использовании Android Device Manager.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: 4cbd7d7626d114856d6c68fe7bc9feb7c2a5abc2
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733834"
---
# <a name="troubleshooting-emulator-setup-problems"></a>Устранение неполадок при настройке эмулятора

_Эта статья описывает, как диагностировать и устранять неполадки, которые могут возникать при использовании Android Device Manager для настройки Android Emulator. Сведения об устранении неполадок во время запуска выполнения Android Emulator см. в разделе [Устранение неполадок в эмуляторе Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md)._

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="android-sdk-in-non-standard-location"></a>Пакет SDK для Android в нестандартном расположении

Как правило, пакет SDK для Android устанавливается в следующем расположении:

**C:\\Program Files (x86)\\Android\\android-sdk**

Если пакет SDK не установлен в этом расположении, при запуске Android Device Manager может возникнуть следующая ошибка:

![Ошибка экземпляра SDK для Android](troubleshooting-images/win/01-sdk-error.png)

Чтобы избежать этой проблемы, сделайте следующее:

1. На рабочем столе Windows перейдите в **C:\\Users\\*имя_пользователя*\\AppData\\Roaming\\XamarinDeviceManager**:

    ![Расположение файла журнала для Android Device Manager](troubleshooting-images/win/02-log-files.png)

2. Дважды щелкните, чтобы открыть один из файлов журнала, и найдите **Путь к файлу конфигурации**. Пример:

    [![Путь к файлу конфигурации в файле журнала](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. Перейдите в это расположение и дважды щелкните **user.config**, чтобы открыть его. 

4. В **user.config** найдите элемент **&lt;UserSettings&gt;** и добавьте к нему атрибут **AndroidSdkPath**. Задайте для этого атрибута путь, по которому установлен пакет SDK для Android на вашем компьютере, и сохраните файл. Например, **&lt;UserSettings&gt;** будет выглядеть следующим образом, если пакет SDK для Android был установлен в **C:\\Programs\\Android\\SDK**:
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

Внесение этого изменения в **user.config** позволит запустить Android Device Manager.

## <a name="snapshot-disables-wifi-on-android-oreo"></a>При создании моментального снимка в Android Oreo отключается Wi-Fi

Если для Android Oreo настроено виртуальное устройство AVD с имитацией доступа по Wi-Fi, перезапуск AVD после создания моментального снимка может привести к отключению доступа по Wi-Fi.

Действия по решению этой проблемы:

1. Выберите AVD в Android Device Manager.

2. В меню дополнительных параметров выберите пункт **Отобразить в проводнике**.

3. Последовательно выберите **snapshots (моментальные снимки) > default_boot**.

4. Удалите файл **snapshot.pb**:

    ![Расположение файла snapshot.pb](troubleshooting-images/win/05-delete-snapshot.png)

5. Перезапустите AVD. 

После внесения этих изменений AVD перезапустится в состоянии, которое позволяет возобновить работу Wi-Fi.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

## <a name="snapshot-disables-wifi-on-android-oreo"></a>При создании моментального снимка в Android Oreo отключается Wi-Fi

Если для Android Oreo настроено виртуальное устройство AVD с имитацией доступа по Wi-Fi, перезапуск AVD после создания моментального снимка может привести к отключению доступа по Wi-Fi.

Действия по решению этой проблемы:

1. Выберите AVD в Android Device Manager.

2. В меню дополнительных параметров выберите пункт **Отобразить в средстве поиска**.

3. Последовательно выберите **snapshots (моментальные снимки) > default_boot**.

4. Удалите файл **snapshot.pb**:

    [![Расположение файла snapshot.pb](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. Перезапустите AVD. 

После внесения этих изменений AVD перезапустится в состоянии, которое позволяет возобновить работу Wi-Fi.


-----

## <a name="generating-a-bug-report"></a>Создание отчета об ошибках

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Если вы обнаружили проблему с Android Device Manager, которую не удается разрешить с помощью приведенных выше советов, отправьте отчет об ошибках. Для этого щелкните правой кнопкой мыши строку заголовка и выберите пункт **Generate Bug Report** (Создать отчет об ошибках):

[![Расположение пункта меню для заполнения отчета об ошибках](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Если вы обнаружили проблему с Android Device Manager, которую не удается решить с помощью приведенных выше советов, отправьте отчет об ошибках. Для этого последовательно выберите **Help > Generate Bug Report** (Справка > Создать отчет об ошибках):

[![Расположение пункта меню для заполнения отчета об ошибках](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)

-----
