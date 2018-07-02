---
title: Удаление Xamarin
description: Этот документ описывает удаление Xamarin на компьютерах Windows и Mac. Он содержит отдельные инструкции по удалению Mono, Xamarin.Android, Xamarin.iOS и других средств.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 444559672f25b13b7d3a769d6de4bd6384174965
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268892"
---
# <a name="uninstalling-xamarin"></a>Удаление Xamarin

Это руководство поясняет, как удалить Xamarin из macOS или из Visual Studio для Windows.

Если вам нужно переустановить Xamarin с помощью универсального установщика, всегда рекомендуем сначала перезагрузить компьютер.

## <a name="uninstalling-xamarin-on-macos"></a>Удаление Xamarin из macOS

Это руководство можно использовать при раздельном удалении продуктов, перейдя в соответствующий раздел. Выполните все указания этого руководства, если хотите полностью удалить набор инструментов Xamarin, включающий следующие продукты.

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [Workbooks](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Установщик](#uninstallinstaller)

> [!TIP]
> Для удаления Xamarin с компьютера macOS воспользуйтесь нашим [скриптом удаления](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh). Дополнительные сведения об использовании скрипта см. в разделе [Использование скрипта удаления](#uninstallscript) этого руководства.

### <a name="uninstalling-visual-studio-for-mac"></a>Удаление Visual Studio для Mac

Первый шаг при удалении Xamarin с компьютера Mac — это найти файл **Visual Studio.app** в каталоге **/Applications** (Приложения) и перетащить его в **Корзину**. Можно также щелкнуть правой кнопкой мыши и выбрать пункт **Переместить в удаленные**, как показано на следующем рисунке:

![Перемещение приложения Visual Studio в корзину](uninstalling-xamarin-images/uninstall-image1.png)

Удаление этого пакета приложений приведет к удалению Visual Studio для Mac, хотя в файловой системе могут остаться другие связанные с Xamarin файлы.

Чтобы удалить все следы Visual Studio для Mac, выполните в терминале следующие команды.

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> Сведения об удалении Visual Studio для Mac см. в руководстве по [удалению](https://docs.microsoft.com/visualstudio/mac/uninstall) на веб-сайте docs.microsoft.com.

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Удаление Mono SDK (MDK)

Mono представляет собой реализацию .NET Framework с открытым исходным кодом и используется всеми продуктами Xamarin (Xamarin.iOS, Xamarin.Android и Xamarin.Mac) для разработки под соответствующие платформы на языке C#.

> [!WARNING]
> Есть и другие приложения, не входящие в Xamarin, которые также используют Mono, например Unity. Перед удалением Mono убедитесь в отсутствии зависимостей от нее.

Чтобы удалить платформу Mono, выполните в терминале следующие команды.

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Удаление Xamarin.Android

Существует ряд компонентов, необходимых для работы Xamarin.Android, например пакеты SDK для Android и Java, которые нужно удалить вместе с Xamarin.Android. Этот раздел приводит указания по удалению всех необходимых частей.

Чтобы удалить Xamarin.Android, выполните в терминале следующие команды.

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Удаление пакета SDK для Android и пакета SDK для Java

Пакет SDK для Android требуется для разработки приложений Android. Чтобы полностью удалить все части пакета SDK для Android, найдите файл в папке **~/Library/Developer/Xamarin/** и переместите его в **корзину**.

Пакет SDK для Java (JDK) удалять не требуется, так как он уже входит в состав Mac OS X.

#### <a name="uninstall-android-avd"></a>Удаление Android AVD

> [!WARNING]
> Существуют и другие приложения, не входящие в Visual Studio для Mac, которые используют Android AVD и указанные дополнительные компоненты Android, например Android Studio.
> Удаление этого каталога может привести к нарушению работы проектов в Android Studio. 

Для удаления виртуальных устройств (AVD) и дополнительных компонентов Android используйте следующую команду.

```bash
rm -rf ~/.android
```

Для удаления только AVD используйте следующую команду.

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Удаление Xamarin.iOS

Xamarin.iOS позволяет разрабатывать приложения iOS на языках C# и F#. Чтобы удалить Xamarin.iOS с компьютера, выполните указанные ниже действия.

Чтобы удалить все файлы Xamarin.iOS, выполните в терминале следующие команды.

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Удаление Xamarin.Mac

Для удаления продукта Xamarin.Mac и его лицензии с компьютера Mac используйте соответственно следующие две команды.

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>Удаление Workbooks

Чтобы удалить Xamarin Workbooks версии 1.2.2 и выше, выполните в терминале следующие команды:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Сведения о более ранних версиях см. в руководстве по удалению [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>Удаление Xamarin Profiler

Чтобы удалить Xamarin Profiler, выполните в терминале следующие команды.

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>Удаление установщика Xamarin

Чтобы удалить все следы универсального установщика Xamarin, используйте следующие команды.

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Использование скрипта удаления

Скрипт удаления позволяет удалить Visual Studio для Mac и связанные компоненты Xamarin за одно действие.

Этот скрипт содержит большую часть команд, приведенных в этой статье. Вследствие возможных внешних зависимостей в этом скрипте опущено два аспекта:

- Удаление Mono
- Удаление виртуальных устройств Android

Чтобы запустить скрипт, выполните следующее.

1. Щелкните скрипт правой кнопкой мыши и выберите "Сохранить как…", чтобы сохранить файл на Mac.

2.  Откройте **терминал** и измените рабочий каталог на папку, куда был скачан скрипт:

        $ cd /location/of/file

3. Сделайте скрипт исполняемым и запустите его с помощью **sudo**:

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

4. Наконец, удалите этот скрипт удаления.

На этом этапе продукт Xamarin должен быть удален с компьютера.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Удаление Xamarin из Windows

Поддержка Xamarin реализована в следующих продуктах.

- [Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) (**не поддерживается**)
- [Xamarin Studio](#uninstallxamarinstudio) (**не поддерживается**)

<a name="uninstallvs2017" />

### <a name="visual-studio-2017"></a>Visual Studio 2017

Xamarin можно удалить из Visual Studio 2017 с помощью приложения установщика.

1. Откройте **установщик Visual Studio** из меню **Пуск**.

2. Нажмите кнопку **Изменить**, предварительно выбрав изменяемый экземпляр.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Нажатие кнопки Изменить")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. На вкладке **Рабочие нагрузки** отмените выбор параметра **Разработка мобильных приложений на платформе .NET** (в разделе **Мобильные приложения и игры**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Отмена выбора рабочей нагрузки Разработка мобильных приложений")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Нажмите кнопку **Изменить** в правом нижнем углу окна.

5. Установщик удалит компоненты, выбор которых отменен (прежде чем установщик сможет вносить изменения, необходимо закрыть Visual Studio 2017).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Нажатие кнопки Изменить")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Чтобы удалить отдельные компоненты Xamarin (например, Profiler или Workbooks), на шаге 3 перейдите на вкладку **Отдельные компоненты** и отмените их выбор.

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Удаление отдельных компонентов")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Чтобы удалить Visual Studio 2017 полностью, в трехстрочном меню рядом с кнопкой **Запустить** выберите пункт **Удалить**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Удаление Visual Studio полностью")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> При наличии двух (или более) параллельно установленных (SxS) экземпляров Visual Studio (например, версии выпуска и предварительной версии) удаление одного экземпляра может привести к удалению некоторых компонентов Xamarin из других экземпляров Visual Studio, включая:
>
> - Xamarin Profiler
> - Xamarin Workbooks или Inspector
> - Симулятор iOS удаленной работы для Xamarin
> - Пакет SDK для Apple Bonjour
>
> При определенных условиях удаление одного из параллельно установленных экземпляров может привести к некорректному удалению этих компонентов. В результате может быть снижена производительность платформы Xamarin в экземплярах Visual Studio, оставшихся в системе после удаления экземпляра SxS.
>
>Для решения этой проблемы запустите в установщике Visual Studio функцию **Исправить**, которая повторно установит отсутствующие компоненты.


## <a name="uninstalling-older-and-unsupported-products"></a>Удаление старых и неподдерживаемых продуктов

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 и более ранние версии

Инструкции по полному удалению Visual Studio 2015 см. в [ответе службы поддержки на сайте visualstudio.com](https://www.visualstudio.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Xamarin можно удалить с компьютера Windows через **панель управления**. Перейдите к элементу **Программы и компоненты** или **Программы > Удаление программы**, как показано ниже:

 [![](uninstalling-xamarin-images/image3.png "Перейдите к элементу Программы и компоненты или Программы > Удаление программы, как показано ниже")](uninstalling-xamarin-images/image3.png#lightbox) 

В панели управления удалите любые из следующих имеющихся компонентов.

- Xamarin
- Xamarin для Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin для Visual Studio

В обозревателе удалите все оставшиеся файлы из папок расширения Xamarin для Visual Studio (все версии, включая обе папки — Program Files и Program Files (x86)).

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Удалите каталог кэша компонента MEF для Visual Studio, который должен находиться здесь.

``` 
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Проверьте, содержит ли каталог **VirtualStore** какие-либо сохраненные Windows файлы наложения для каталогов **Extensions\Xamarin** или **ComponentModelCache**:

``` 
%LOCALAPPDATA%\VirtualStore
```

Откройте редактор реестра (regedit) и найдите следующий раздел.

``` 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Найдите и удалите все записи, соответствующие этому шаблону.

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Найдите следующий раздел.

``` 
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Удалите все записи, которые могут иметь отношение к Xamarin. Например, все записи, содержащие термины `mono` или `xamarin`.

Откройте командную строку (cmd.exe) от имени администратора и выполните команды `devenv /setup` и `devenv /updateconfiguration` для каждой установленной версии Visual Studio. Например, для Visual Studio 2015:

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>удаление Xamarin Studio на Windows

Xamarin Studio удаляется с компьютера Windows через **панель управления**. Перейдите к элементу **Программы и компоненты** или **Программы > Удаление программы** 

Чтобы удалить Xamarin Studio, выберите **Xamarin Studio 5.x.x** в списке программ и щелкните **Удалить**. 

### <a name="uninstall-xamarin-studio-on-mac"></a>Удаление Xamarin Studio на Mac

Первый шаг в удалении Xamarin Studio из Mac заключается в поиске файла **Xamarin Studio.app** в каталоге **/Applications** и перетаскивании его в **корзину**. Можно также щелкнуть правой кнопкой мыши и выбрать пункт **Переместить в удаленные**, как показано ниже:

 [![](uninstalling-xamarin-images/image1.png "Можно также щелкнуть правой кнопкой мыши и выбрать пункт Переместить в удаленные, как показано ниже")](uninstalling-xamarin-images/image1.png#lightbox)

Удаление этого набора приложений приведет к удалению Xamarin Studio, однако в файловой системе останутся другие файлы, связанные с Xamarin.

Чтобы удалить все следы Xamarin Studio, нужно выполнить в терминале следующие команды:

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>Сводка

В этой статье приведены указания по полному удалению Xamarin с компьютера Mac при помощи команд терминала. Здесь также представлены действия по удалению Xamarin с компьютера Windows через элемент **Программы и компоненты** (для Visual Studio 2015 и более ранних версий), а также с помощью **установщика Visual Studio** (для Visual Studio 2017).


## <a name="related-links"></a>Связанные ссылки

- [Скрипт удаления (пример](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
