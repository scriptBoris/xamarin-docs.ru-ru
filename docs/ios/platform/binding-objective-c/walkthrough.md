---
title: 'Пошаговое руководство: Привязка библиотек Objective-C iOS'
description: Эта статья содержит практические руководство по созданию привязку Xamarin.iOS для существующей библиотеки Objective-C, InfColorPicker. Здесь рассматриваются таким темам, как компиляция статической библиотеки Objective-C, привязки и использование привязки в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: a4cdb76ac1ecea3ee21e7b74314b6d3bfae09719
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118998"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Пошаговое руководство: Привязка библиотек Objective-C iOS

_Эта статья содержит практические руководство по созданию привязку Xamarin.iOS для существующей библиотеки Objective-C, InfColorPicker. Здесь рассматриваются таким темам, как компиляция статической библиотеки Objective-C, привязки и использование привязки в приложении Xamarin.iOS._

При работе на iOS, возможны случаи, где вы хотите использовать сторонние библиотеки Objective-C. В такой ситуации можно использовать Xamarin.iOS _проект привязки_ для создания [ C# привязки](~/cross-platform/macios/binding/overview.md) , позволит вам использовать библиотеки в приложениях Xamarin.iOS.

Обычно в экосистеме iOS библиотеки можно найти в версии 3:

* Как файл предкомпилированного статическую библиотеку с `.a` расширения вместе с его заголовков (h-файлы). Например [библиотеки Google Analytics](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Как предкомпилированного платформу. Это просто папка, содержащая статической библиотеки, заголовки и иногда дополнительные ресурсы с помощью `.framework` расширения. Например [библиотеки AdMob от Google](https://developers.google.com/admob/ios/download).
* Как только файлы исходного кода. Например, библиотеку, содержащую только что `.m` и `.h` Objective C-файлов.

В случае первого и второго уже будет предкомпилированного cocoatouch, облегченную статической библиотеки, в этой статье мы сосредоточимся на третий сценарий. Помните, что прежде чем создавать привязку, всегда проверяйте лицензия, предоставляемый библиотекой, чтобы убедиться, что Вы свободны в его привязке.

Статья содержит пошаговое руководство по созданию проекта привязки с помощью с открытым исходным кодом [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C проекта в качестве примера, однако все сведения в этом руководстве можно адаптировать для использования с любым сторонние библиотеки Objective-C. Библиотека InfColorPicker предоставляет контроллер для повторного использования представления, позволяющий пользователю выбрать цветом, основанным на его представление HSB, выбора цвета более понятные.

[![](walkthrough-images/run01.png "Пример библиотеки InfColorPicker, работающих под управлением iOS")](walkthrough-images/run01.png#lightbox)

Мы обсудим все необходимые действия для использования этого конкретного API Objective-C в Xamarin.iOS:

- Во-первых мы создадим статическая библиотека Objective-C, с помощью Xcode.
- Затем мы привязать этот статическую библиотеку с помощью Xamarin.iOS.
- Далее показано, как цель Sharpie может снизить рабочую нагрузку путем автоматического создания некоторые (но не все) из необходимые определения API, необходимые в привязке Xamarin.iOS.
- Наконец мы создадим приложение Xamarin.iOS, которое использует эту привязку.

Пример приложения демонстрирует использование строгого делегата для обмена данными между InfColorPicker API и нашей C# кода. После мы узнали, как использовать надежный делегат, мы обсудим способы использования слабых делегатах для выполнять те же задачи.

## <a name="requirements"></a>Требования

В этой статье предполагается, что у вас есть некоторый опыт работы с Xcode и язык Objective-C и прочитав наши [привязке Objective-C](~/cross-platform/macios/binding/index.md) документации. Кроме того ниже приведен необходимо выполнить шаги, которые отображаются.

-  **Xcode и пакет SDK iOS** -Apple Xcode и последние API iOS необходимо установить и настроить на компьютере разработчика.
-  **[Средства командной строки Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -командной строки Xcode должна быть установлена установленную версию Xcode (ниже приведены сведения об установке).
-  **Visual Studio для Mac или Visual Studio** -последнюю версию Visual Studio для Mac или Visual Studio должны быть установлены и настроен на компьютере разработки. Apple Mac необходим для разработки приложения Xamarin.iOS, и при использовании Visual Studio вы должны быть подключены к [узла сборки Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **Последнюю версию цели Sharpie** -текущее средство Sharpie цели загружена из [здесь](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Если уже установлена Sharpie цели, можно обновить его до последней версии с помощью `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Установка средства Xcode командной строки

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)


Как уже говорилось выше, мы будем использовать средства командной строки Xcode (в частности `make` и `lipo`) в этом пошаговом руководстве. `make` Команда — это очень распространенный программа Unix, которая автоматизирует компиляции исполняемые программы и библиотеки с помощью _makefile_ , указывающий, построение программы. `lipo` Команда является служебной программой командной строки OS X для создания нескольких архитектура файлов, он будет объединять несколько `.a` файлов в один файл, который может использоваться с всех архитектур оборудования.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Как уже говорилось выше, мы будем использовать средства командной строки Xcode на **узел сборки Mac** (в частности `make` и `lipo`) в этом пошаговом руководстве. `make` Команда — это очень распространенный программа Unix, которая автоматизирует компиляции исполняемые программы и библиотеки с помощью _makefile_ определяет способ построения программы. `lipo` Команда является служебной программой командной строки OS X для создания нескольких архитектура файлов, он будет объединять несколько `.a` файлов в один файл, который может использоваться с всех архитектур оборудования.


-----

В соответствии с Apple [построение из командной строки с вопросами и Ответами Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) документации, в OS X 10.9 и более поздних, **Скачивает** области Xcode **предпочтения** диалоговое окно не больше поддерживает загрузку программ командной строки.

Необходимо использовать один из следующих методов для установки инструментов:

- **Установить Xcode** — при установке Xcode, он были включены с помощью любых средств командной строки. В OS X 10.9 оболочки (установлен в `/usr/bin`), можно сопоставить любое средство, включенное в `/usr/bin` для соответствующего средства в Xcode. Например `xcrun` команду, которая позволяет найти или запустить любое средство внутри Xcode из командной строки.
- **Приложение терминалов** -терминала, в приложении можно установить программы командной строки, запустив `xcode-select --install` команды:
    - Запустите приложение Terminal.
    - Тип `xcode-select --install` и нажмите клавишу **ввод**, например:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Вам будет предложено установить программы командной строки, нажмите кнопку **установить** кнопки: [ ![](walkthrough-images/xcode01.png "Установка программ командной строки")](walkthrough-images/xcode01.png#lightbox)

    - Средства будет загрузить и установить на серверах компании Apple: [ ![](walkthrough-images/xcode02.png "загрузку набора средств")](walkthrough-images/xcode02.png#lightbox)

- **Загружаемые ресурсы для разработчиков Apple** -доступен пакет средств командной строки [загрузки для разработчиков Apple]() веб-страницы. Войдите с помощью вашего идентификатора Apple, а затем найдите и Загрузите средства командной строки: [ ![](walkthrough-images/xcode03.png "поиск программы командной строки")](walkthrough-images/xcode03.png#lightbox)

С установленными средствами командной строки мы готовы, перейдите к выполнению пошагового руководства.

## <a name="walkthrough"></a>Пошаговое руководство

В этом пошаговом руководстве мы обсудим следующие действия:

- **[Создание статической библиотеки](#Creating_A_Static_Library)**  -этот шаг включает в себя создание статической библиотеки из **InfColorPicker** код Objective-C. Статическая библиотека будет иметь `.a` расширение файла, а также будут внедрены в сборку проекта библиотеки .NET.
- **[Создание привязки проекта Xamarin.iOS](#Create_a_Xamarin.iOS_Binding_Project)**  -когда статической библиотеки, мы будем использовать для создания привязки проекта Xamarin.iOS. Проект привязки состоит из статической библиотеки, которые мы только что создали и метаданные в виде C# код, чтобы понять, как можно использовать в Objective-C API. Эти метаданные часто называют определений API. Мы будем использовать **[Objective Sharpie](#Using_Objective_Sharpie)** помощь с создания определений API.
- **[Нормализовать определений API](#Normalize_the_API_Definitions)**  - Sharpie цели не помогает нам большую работу, однако она не поддерживает все. Мы обсудим некоторые изменения, которые необходимо внести для определения API, прежде чем они могут использоваться.
- **[Использование библиотеки привязки](#Using_the_Binding)**  -и, наконец, мы создадим приложение Xamarin.iOS, чтобы показать, как использовать наш проект только что созданный привязки.

Теперь, когда мы понимаем, что шагах, давайте перейдем к остальной части пошагового руководства.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Создание статической библиотеки

Если мы проверить код для InfColorPicker в Github:

[![](walkthrough-images/image02.png "Проверьте код для InfColorPicker в Github")](walkthrough-images/image02.png#lightbox)

Мы видим три дополнительных каталога в проекте:

- **InfColorPicker** -этот каталог содержит код Objective-C для проекта.
- **PickerSamplePad** -этот каталог содержит пример проекта iPad.
- **PickerSamplePhone** -этот каталог содержит пример проекта iPhone.

Давайте загрузим проект InfColorPicker из [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) и распакуйте его в каталоге на выбор. Открыв целевой Xcode для `PickerSamplePhone` проекта, мы видим следующую структуру проекта в навигаторе Xcode:

[![](walkthrough-images/image03.png "Структура проекта в навигаторе Xcode")](walkthrough-images/image03.png#lightbox)

Этот проект реализует повторное использование кода за счет непосредственного добавления InfColorPicker исходный код (в красной рамкой) в каждый проект примера. Код для примера проекта находится внутри синей рамкой. Так как для этого проекта не поддерживает нас к статической библиотеке, это необходимо для нам создать проект Xcode для Скомпилируйте статическую библиотеку.

Первым шагом является добавление InfoColorPicker исходного кода в статическую библиотеку. Для этого давайте выполните следующие действия:

1. Запустите Xcode.
2. Из **файл** меню выберите **New** > **проекта...** :

    [![](walkthrough-images/image04.png "Создании нового проекта")](walkthrough-images/image04.png#lightbox)
3. Выберите **Framework & Библиотека**, **Cocoa Touch статическая библиотека** шаблона и нажмите кнопку **Далее** кнопки:

    [![](walkthrough-images/image05.png "Выберите шаблон Cocoa Touch статическую библиотеку")](walkthrough-images/image05.png#lightbox)

4. Введите `InfColorPicker` для **имя_проекта** и нажмите кнопку **Далее** кнопки:

    [![](walkthrough-images/image06.png "Введите имя проекта InfColorPicker")](walkthrough-images/image06.png#lightbox)
5. Выберите расположение для сохранения проекта и нажмите кнопку **ОК** кнопки.
6. Теперь нам нужно добавить источник из проекта InfColorPicker наш проект статической библиотеки. Так как **InfColorPicker.h** файл уже существует в нашей статической библиотеки (по умолчанию), Xcode не позволяет нам перезаписать его. Из **Finder**, перейдите к исходному коду InfColorPicker в исходном проекте, который мы распакован из GitHub, скопируйте все файлы InfColorPicker и вставьте их в наш новый проект статической библиотеки:

    [![](walkthrough-images/image12.png "Скопируйте все файлы InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Вернуться к Xcode, щелкните правой кнопкой мыши **InfColorPicker** папку и выберите **добавить файлы «InfColorPicker...»**:

    [![](walkthrough-images/image08.png "Добавление файлов")](walkthrough-images/image08.png#lightbox)

8. В диалоговом окне Добавление файлов, перейдите к InfColorPicker файлы исходного кода, которые мы просто скопировать, выделите их все и нажмите кнопку **добавить** кнопки:

    [![](walkthrough-images/image09.png "Выберите все и нажмите кнопку \"Добавить\"")](walkthrough-images/image09.png#lightbox)

9. Исходный код будут скопированы в наш проект:

    [![](walkthrough-images/image10.png "Исходный код копируется в проект")](walkthrough-images/image10.png#lightbox)

10. Навигатор проекта Xcode, выберите **InfColorPicker.m** файл и закомментируйте две последние строки (из-за того, эта библиотека была написана, этот файл не используется):

    [![](walkthrough-images/image14.png "Редактирование файла InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Теперь нам нужно проверить, если есть любой платформы, требуемые библиотекой. Эти сведения можно найти в файле README, или путем открытия одного из образцов проектов, которые предоставляются. В этом примере используется `Foundation.framework`, `UIKit.framework`, и `CoreGraphics.framework` так что давайте добавим их.

12. Выберите **InfColorPicker целевой > этапы сборки** и разверните **ссылку двоичного файла с библиотеками** раздел:

    [![](walkthrough-images/image16b.png "Разверните раздел \"ссылки двоичного файла с библиотеками\"")](walkthrough-images/image16b.png#lightbox)

13. Используйте **+** кнопку, чтобы открыть диалоговое окно, позволяя добавлять платформ требуется кадров, перечисленных выше:

    [![](walkthrough-images/image16c.png "Добавление платформы требуется кадров, перечисленных выше")](walkthrough-images/image16c.png#lightbox)

14. **Ссылку двоичного файла с библиотеками** раздел теперь должен выглядеть как на следующем рисунке:

    [![](walkthrough-images/image16d.png "В разделе ссылку двоичного файла с библиотеками")](walkthrough-images/image16d.png#lightbox)

На этом этапе мы можем закрыть, но мы все еще не готово. Будет создана статическая библиотека, но нужно создать его, чтобы создать двоичный, который включает в себя все необходимые архитектуры для устройств iOS и симулятор iOS Fat.

### <a name="creating-a-fat-binary"></a>Создание толстого двоичного файла

Все устройства iOS процессорами на базе архитектуры ARM, которые разработали со временем. Каждая новая архитектура добавлены инструкции по созданию и другие усовершенствования, поддерживая обратную совместимость. На устройствах iOS у нас есть armv6, armv7, armv7s, наборы инструкций arm64 — несмотря на то что [мы больше не использовать armv6](~/ios/deploy-test/compiling-for-different-devices.md). Симулятор iOS не реализована на базе ARM и является istead x86 и x86_64 на базе симулятора. Мы, означает, что для нас — что мы предоставляем библиотеки на каждая инструкция задает.

Fat библиотека является `.a` файл, содержащий все поддерживаемые архитектуры.

Fat двоичных создается в три этапа:

- Скомпилируйте ARM 7 и ARM64 версия статической библиотеки.
- Скомпилируйте x86 и x84_64 версия статической библиотеки.
- Используйте `lipo` средство командной строки для объединения двух статических библиотек в одну.

Следующие три действия являются довольно простыми, а часто бывает необходимо повторять их в будущем при библиотеки Objective-C получает обновления или если это требуется исправления ошибок. Если принято решение для автоматизации этих шагов, оно будет упростить последующее обслуживание и поддержку привязки проекта iOS.

Существует множество средств, позволяющих автоматизировать такие задачи - сценарий оболочки, [rake](http://rake.rubyforge.org/), [xbuild](http://www.mono-project.com/docs/tools+libraries/tools/xbuild/), и [сделать](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). При установке программ командной строки Xcode, мы также установили make, система сборки, который будет использоваться в этом пошаговом руководстве. Вот **Makefile** , можно использовать для создания нескольких архитектура общая библиотека, будут работать на устройстве iOS и симулятору для любой библиотеки:

```bash
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```

Введите **Makefile** команды в текстовом редакторе по своему выбору и обновить разделы с **YOUR ИМЯ_ПРОЕКТА** с именем проекта. Также важно убедиться, что мы вставьте инструкциям выше, сохранности вкладках внутри инструкции.

Сохраните файл с именем **Makefile** в том же расположении, как статическая библиотека Xcode InfColorPicker, мы создали выше:

[![](walkthrough-images/lib00.png "Сохраните файл с именем Makefile")](walkthrough-images/lib00.png#lightbox)

Откройте приложение Terminal на компьютере Mac и перейдите к расположению файла Makefile. Тип `make` в окне терминала нажмите **ввод** и **Makefile** выполняется:

[![](walkthrough-images/lib01.png "Пример выходных данных файла makefile")](walkthrough-images/lib01.png#lightbox)

При запуске убедитесь, вы увидите массу прокрутку на текст. Если все работает правильно, вы увидите слова **сборка успешно ЗАВЕРШЕНА** и `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` и `libInfColorPickerSDK.a` файлы будут скопированы в то же расположение, что **Makefile**:

[![](walkthrough-images/lib02.png "LibInfColorPicker armv7.a, libInfColorPicker i386.a и libInfColorPickerSDK.a файлы, созданные в файл Makefile")](walkthrough-images/lib02.png#lightbox)

Архитектуры можно проверить в рамках вашей толстого двоичного файла с помощью следующей команды:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Там отобразится следующее:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

На этом этапе, мы завершили первый шаг привязки с iOS путем создания статической библиотеки с помощью Xcode и программы командной строки Xcode `make` и `lipo`. Давайте перемещение к следующему шагу и использовать **Sharpie цели** для автоматизации создания привязок API для нас.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Создайте привязка проекта Xamarin.iOS

Прежде чем мы можем использовать **Sharpie цели** для автоматизации процесса привязки, нам необходимо создать проект привязку Xamarin.iOS к лабораториям определения API (, мы будем использовать **Sharpie цели** помощь сборка) и создайте C# привязки для нас.

Давайте, сделайте следующее:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Запустите Visual Studio для Mac.
1. Из **файл** меню, выберите **New** > **решения...** :

    ![](walkthrough-images/bind01.png "Запуск нового решения")

1. В диалоговом окне нового решения выберите **библиотеки** > **привязки проект iOS**:

    ![](walkthrough-images/bind02.png "Выберите привязку проект iOS")

1. Нажмите кнопку **Далее** кнопки.

1. Введите «InfColorPickerBinding» в качестве **имя_проекта** и нажмите кнопку **создать** кнопку, чтобы создать решение:

    ![](walkthrough-images/bind02a.png "Введите InfColorPickerBinding в качестве имени проекта")

Создается решение, и два файла по умолчанию будут включены:

![](walkthrough-images/bind03.png "Структура решения в обозревателе решений")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Запустите Visual Studio.

1. Из **файл** меню, выберите **New** > **проекта...** :

    ![Создании нового проекта](walkthrough-images/bind01vs.png "создании нового проекта")

1. В диалоговом окне нового проекта выберите **Visual C# > iPhone & iPad > привязки библиотека iOS (Xamarin)**:

    [![Выберите Библиотека привязок iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Введите «InfColorPickerBinding» в качестве **имя** и нажмите кнопку **ОК** кнопку, чтобы создать решение.

Создается решение, и два файла по умолчанию будут включены:

![](walkthrough-images/bind03vs.png "Структура решения в обозревателе решений")

-----

- **ApiDefinition.cs** -этот файл будет содержать контракты, которые определяют, каким образом Objective-C API будет вставлено в C#.
- **Structs.cs** - этот файл будет содержать все структуры или значения перечисления, необходимые интерфейсы и делегаты.

Мы будем работать с эти два файла, далее в этом пошаговом руководстве. Во-первых необходимо добавить в библиотеку InfColorPicker проект привязки.

### <a name="including-the-static-library-in-the-binding-project"></a>Включая статическую библиотеку в проект привязки

Теперь у нас есть наш базовый привязки проект готов, нам нужно добавить Fat двоичных библиотеки, созданных ранее для **InfColorPicker** библиотеки.

Выполните следующие действия, чтобы добавить в библиотеку.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Щелкните правой кнопкой мыши **собственные ссылки** папки в панели решения и выберите **добавить собственные ссылки**:

    ![](walkthrough-images/bind04a.png "Добавить собственные ссылки")

1. Перейдите к Fat двоичных мы добавили ранее (`libInfColorPickerSDK.a`) и нажмите клавишу **откройте** кнопки:

    ![](walkthrough-images/bind05.png "Выберите файл libInfColorPickerSDK.a")
1. Файл будет включен в проект:

    ![](walkthrough-images/bind04.png "Включая файл")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Копировать `libInfColorPickerSDK.a` из вашей **узел сборки Mac** и вставьте его в проект привязки.

1. Правой кнопкой мыши проект и выберите **Добавить > существующий элемент...** :

    ![](walkthrough-images/bind04vs.png "Добавляет существующий файл")

1. Перейдите к `libInfColorPickerSDK.a` и нажмите клавишу **добавить** кнопки:

    ![](walkthrough-images/bind05vs.png "Добавление libInfColorPickerSDK.a")

1. Файл будет включен в проект.

-----

Когда **.a** файл добавляется в проект Xamarin.iOS автоматически установит **действие при построении** файла **ObjcBindingNativeLibrary**и создайте специальный файл вызывается `libInfColorPickerSDK.linkwith.cs`.


Этот файл содержит `LinkWith` атрибут, который сообщает Xamarin.iOS как дескриптор статической библиотеки, который мы только что добавлен. Содержимое этого файла показано в следующем фрагменте кода:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith` Атрибут определяет статическую библиотеку для проекта, а также некоторые важные компоновщика флаги.


Следующее, что нам нужно сделать — создать определения API для InfColorPicker проекта. В целях этого пошагового руководства мы будем использовать Sharpie цели для создания файла **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>С помощью цели Sharpie

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)


Цели Sharpie является командной строки средства (предоставляемое Xamarin), который может помочь в создании определений, необходимый для привязки библиотека стороннего производителя Objective-C для C#. В этом разделе мы будем использовать Sharpie цели, чтобы создать первичный **ApiDefinition.cs** InfColorPicker проекта.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Цели Sharpie является командной строки средства (предоставляемое Xamarin), который может помочь в создании определений, необходимый для привязки библиотека стороннего производителя Objective-C для C#. В этом разделе мы будем использовать Sharpie цели на наших **узел сборки Mac** для создания начального **ApiDefinition.cs** InfColorPicker проекта.


-----

Чтобы начать работу, давайте загрузим файл установщика Sharpie цели, как описано в этом [руководство](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Запустите установщик и выполните все запросы на экране с помощью мастера установки для установки Sharpie цели на компьютере разработки.

После того как мы Sharpie цели успешно установлен, начнем приложение "Терминал" и введите следующую команду, чтобы получить справку по всем средствам, которые она предоставляет для помощи в привязке:

```bash
sharpie -help
```

Если мы выполнить команду выше, будут создаваться следующие выходные данные:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

В данном пошаговом руководстве мы будем использовать следующие средства Sharpie цели:

- **xcode** : Эта средства дает нам сведения о нашей текущей установки Xcode и версиях iOS и Mac API, которые мы установили. Мы будем использовать в дальнейшем при создании наших привязки.
- **привязать** -мы будем использовать это средство для анализа **.h** файлы в проекте InfColorPicker в исходный **ApiDefinition.cs** и **StructsAndEnums.cs** файлов.

Чтобы получить справку по определенное средство Sharpie цели, введите имя средства и `-help` параметр. Например `sharpie xcode -help` возвращает следующие выходные данные:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Прежде чем приступать процесса привязки, нам нужно получить сведения о нашей текущей установленных SDK, введя следующую команду в окне терминала `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Приведенные выше, мы видим, что у нас есть `iphoneos9.3` пакет SDK установлен на компьютере. Располагая этой информацией, мы готовы выполнить синтаксический анализ проекта InfColorPicker `.h` файлы в исходный **ApiDefinition.cs** и `StructsAndEnums.cs` InfColorPicker проекта.

В приложении Terminal, введите следующую команду:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Где `[full-path-to-project]` — это полный путь к каталогу где **InfColorPicker** файл проекта Xcode находится на компьютере, а [iphone-os] является iOS SDK, который мы установили, как было отмечено `sharpie xcode -sdks` команды. Обратите внимание, что в этом примере мы передали  **\*.h** как параметр, который включает *все* файлы заголовков в этом каталоге - обычно вы должны не сделать, но вместо внимательно прочтите файлы заголовков, чтобы найти верхнего уровня **.h** файл ссылается на все соответствующие файлы, а просто передать его Sharpie цели.

Следующие [вывода](walkthrough-images/os05.png) будет создаваться в окне терминала:

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

И **InfColorPicker.enums.cs** и **InfColorPicker.cs** будут созданы файлы в каталог:

[![](walkthrough-images/os06.png "Файлы InfColorPicker.enums.cs и InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)


Откройте оба файла в проект привязки, созданные ранее. Скопируйте содержимое **InfColorPicker.cs** файл и вставьте его в **ApiDefinition.cs** файл, заменяя существующие `namespace ...` блок кода с содержимым  **InfColorPicker.cs** файл (оставляя `using` инструкции без изменений):

![](walkthrough-images/os07.png "Файл InfColorPickerControllerDelegate")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Откройте оба файла в проект привязки, созданные ранее. Скопируйте содержимое **InfColorPicker.cs** файла (из **узел сборки Mac**) и вставьте его в **ApiDefinition.cs** файл, заменяя существующие `namespace ...` блок кода с содержимым **InfColorPicker.cs** файл (оставляя `using` инструкции без изменений).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Нормализовать определения API

Цели Sharpie иногда возникают проблемы при переводе `Delegates`, поэтому нам потребуется изменить определение `InfColorPickerControllerDelegate` интерфейс и замените `[Protocol, Model]` строки со следующими:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Таким образом, чтобы определение выглядит так:

[![](walkthrough-images/os11.png "Определение")](walkthrough-images/os11.png#lightbox)

Далее мы делаем то же самое с содержимым `InfColorPicker.enums.cs` файл, скопировав и вставив их в `StructsAndEnums.cs` файл оставляя `using` инструкции без изменений:

[![](walkthrough-images/os09.png "StructsAndEnums.cs содержимое файла ")](walkthrough-images/os09.png#lightbox)

Кроме того, возможно, цель Sharpie снабжены привязка, с помощью `[Verify]` атрибуты. Эти атрибуты указывают, что следует проверить, что цель Sharpie было правильным действием, сравнивая привязка, с помощью исходного объявления C/Objective-C (которые будут предоставляться в комментарии перед объявлением привязанного). После проверки привязок должно удалять атрибут проверки. Дополнительные сведения см. [Verify](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) руководства.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)


На этом этапе наш проект привязки должен быть завершен и готов для построения. Создадим наш проект привязки и убедитесь, что в итоге мы без ошибок:

[Постройте проект привязки и убедитесь, что ошибок нет](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


На этом этапе наш проект привязки должен быть завершен и готов для построения. Создадим наш проект привязки и убедитесь, что в итоге мы без ошибок.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>С помощью привязки

Выполните следующие действия для создания примера приложения iPhone для IOS в привязка библиотеки, созданной выше.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. **Создание проекта Xamarin.iOS** -добавить новый проект Xamarin.iOS с именем **InfColorPickerSample** в решение, как показано на следующем снимке экрана:

    ![](walkthrough-images/use01.png "Добавление приложения с одним представлением")

    ![](walkthrough-images/use01a.png "Задание идентификатора")

1. **Добавьте ссылку на проект привязки** -обновление **InfColorPickerSample** проекта, чтобы он включал ссылку на **InfColorPickerBinding** проекта:

    ![](walkthrough-images/use02.png "Добавление ссылки на проект привязки")

1. **Создание пользовательского интерфейса iPhone** -дважды щелкните **MainStoryboard.storyboard** файл **InfColorPickerSample** проект для редактирования в конструкторе iOS. Добавить **кнопку** к представлению и назовите его `ChangeColorButton`, как показано ниже:

    ![](walkthrough-images/use03.png "Добавление кнопки к представлению")

1. **Добавить InfColorPickerView.xib** -библиотека Objective-C InfColorPicker включает **.xib** файла. Xamarin.iOS не будет содержать это **.xib** в проекте привязки, которое может вызвать ошибки времени выполнения в нашем примере приложения. Решением этой проблемы является добавление **.xib** файл, чтобы наш проект Xamarin.iOS. Выберите проект Xamarin.iOS, щелкните правой кнопкой мыши и выберите **Добавить > Добавить файлы**и добавьте **.xib** файл, как показано на следующем снимке экрана:

    ![](walkthrough-images/use04.png "Добавить InfColorPickerView.xib")

1. При появлении запроса, скопируйте **.xib** файл в проект.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Создание проекта Xamarin.iOS** — Добавление нового проекта Xamarin.iOS с именем **InfColorPickerSample** с помощью **приложение с одним представлением** шаблона:

    [![проект iOS приложения (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Выберите шаблон](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Добавьте ссылку на проект привязки** -обновление **InfColorPickerSample** проекта, чтобы он включал ссылку на **InfColorPickerBinding** проекта:

    ![](walkthrough-images/use02vs.png "Добавьте ссылку на проект привязки")

1. **Создание пользовательского интерфейса iPhone** -дважды щелкните **MainStoryboard.storyboard** файл **InfColorPickerSample** проект для редактирования в конструкторе iOS. Добавить **кнопку** к представлению и назовите его `ChangeColorButton`, как показано ниже:

    ![](walkthrough-images/use03vs.png "Создание пользовательского интерфейса iPhone")

1. **Добавить InfColorPickerView.xib** -библиотека Objective-C InfColorPicker включает **.xib** файла. Xamarin.iOS не будет содержать это **.xib** в проекте привязки, которое может вызвать ошибки времени выполнения в нашем примере приложения. Решением этой проблемы является добавление **.xib** файл, чтобы наш проект Xamarin.iOS из наших **узел сборки Mac**. Выберите проект Xamarin.iOS, щелкните правой кнопкой мыши и выберите **добавить** > **существующий элемент...** и добавьте **.xib** файла.

-----

Теперь давайте кратко рассмотрим протоколов в Objective-C и как мы обрабатываем их в привязке и C# кода.

### <a name="protocols-and-xamarinios"></a>Протоколы и Xamarin.iOS

В Objective-C, протокол определяет методы (или сообщения), можно использовать в определенных обстоятельствах. По существу, они очень похожи на интерфейсы в C#. Крупным различием между протокола Objective-C и C# интерфейс является то, что протоколы могут иметь необязательные методы - методы, которые класс должен реализовывать. Objective-C использует @optional ключевое слово используется для указания, какие методы являются необязательными. Дополнительные сведения о протоколах см. в разделе [события, протоколы и делегаты](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** имеет один такой протокол, показано в следующем фрагменте кода:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Этот протокол используется **InfColorPickerController** для информирования клиентов о том, что пользователь выбрал новый цвет и что **InfColorPickerController** завершения работы. Цели Sharpie сопоставлены этого протокола, как показано в следующем фрагменте кода:

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

При компиляции библиотеки привязки, Xamarin.iOS создает абстрактный базовый класс с именем `InfColorPickerControllerDelegate`, который реализует этот интерфейс с помощью виртуальных методов.

Существует два способа, что мы можем реализовать этот интерфейс в приложении Xamarin.iOS:

- **Делегировать строгих** -с помощью строгого делегата включает в себя создание C# класса, который наследуется от класса `InfColorPickerControllerDelegate` и переопределяет соответствующие методы. **InfColorPickerController** будет использовать экземпляр этого класса для связи с ее клиентами.
- **Слабый делегат** -слабый делегат — это немного другой метод, который включает в себя создание открытого метода на один из классов (таких как `InfColorPickerSampleViewController`) и затем предоставление метода `InfColorPickerDelegate` протокола с помощью `Export` атрибута.

Надежный делегаты предоставляют Intellisense, безопасность типа и эффективной инкапсуляции. По этим причинам следует использовать строгое делегаты здесь можно, вместо слабый делегат.

В этом пошаговом руководстве мы рассмотрим оба метода: сначала реализации строгого делегата и затем объясняя способ внедрения слабый делегат.

### <a name="implementing-a-strong-delegate"></a>Реализация строгого делегата

Готово приложение Xamarin.iOS с помощью строгого делегата реагировать на `colorPickerControllerDidFinish:` сообщение:

**Подкласс InfColorPickerControllerDelegate** -добавьте новый класс в проект под названием `ColorSelectedDelegate`. Измените класс таким образом, чтобы он содержал следующий код:

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
    public class ColorSelectedDelegate:InfColorPickerControllerDelegate
    {
        readonly UIViewController parent;

        public ColorSelectedDelegate (UIViewController parent)
        {
            this.parent = parent;
        }

        public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
        {
            parent.View.BackgroundColor = controller.ResultColor;
            parent.DismissViewController (false, null);
        }
    }
}
```

Xamarin.iOS будет привязан делегат Objective-C путем создания абстрактного базового класса вызывается `InfColorPickerControllerDelegate`. Подкласс этого типа и переопределение `ColorPickerControllerDidFinish` метод для доступа к значению `ResultColor` свойство `InfColorPickerController`.

**Создайте экземпляр ColorSelectedDelegate** -наш обработчик событий будет иметь экземпляр `ColorSelectedDelegate` тип, который мы создали на предыдущем шаге. Отредактируйте класс `InfColorPickerSampleViewController` и добавьте следующую переменную экземпляра в класс:

```csharp
ColorSelectedDelegate selector;
```

**Инициализируйте переменную ColorSelectedDelegate** — чтобы убедиться, что `selector` является допустимым экземпляра, обновите метод `ViewDidLoad` в `ViewController` для сопоставления в следующем фрагменте кода:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Реализуйте метод HandleTouchUpInsideWithStrongDelegate** -Далее реализовать обработчик события, когда пользователь касается **ColorChangeButton**. Изменить `ViewController`и добавьте следующий метод:

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

Во-первых, мы получаем экземпляр `InfColorPickerController` через статический метод и убедитесь, что экземпляр о наших строгого делегат через свойство `InfColorPickerController.Delegate`. Это свойство автоматически созданное для нас Sharpie цели. Наконец мы называем `PresentModallyOverViewController` для отображения представления `InfColorPickerSampleViewController.xib` , в котором пользователь может выбрать цвет.

**Запустите приложение** — на этом этапе мы закончили со всеми часть нашего кода. При запуске приложения, вы сможете изменить цвет фона `InfColorColorPickerSampleView` как показано на следующем снимке экрана:

[![](walkthrough-images/run01.png "Запуск приложения")](walkthrough-images/run01.png#lightbox)

Поздравляем! На этом этапе вы успешно создать и привязать библиотеку Objective-C для использования в приложении Xamarin.iOS. Теперь давайте познакомимся с помощью слабых делегатах.

### <a name="implementing-a-weak-delegate"></a>Реализация слабый делегат

Вместо создания подклассов класс привязан к протоколу Objective-C для определенного делегата, Xamarin.iOS также позволяет реализовать методы протокола в любой класс, производный от `NSObject`, обрамления методов с `ExportAttribute`, а затем указав соответствующие селекторы. При использовании этого подхода можно назначить экземпляр класса могли `WeakDelegate` свойство вместо `Delegate` свойство. Слабый делегат предлагает гибкие возможности для класса делегата вниз по иерархии наследования разных. Давайте посмотрим, как реализовать и использовать слабый делегат в нашем приложении Xamarin.iOS.

**Создайте обработчик событий для TouchUpInside** -давайте создадим новый обработчик событий для `TouchUpInside` события изменить цвет фона кнопки. Этот обработчик будет заполнять ту же роль `HandleTouchUpInsideWithStrongDelegate` обработчик мы создан в предыдущем разделе, что будет использовать слабый делегат вместо строгого делегат. Отредактируйте класс `ViewController`и добавьте следующий метод:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Обновить ViewDidLoad** -нам нужно изменить `ViewDidLoad` , чтобы он использовал обработчик событий, который мы только что создали. Изменить `ViewController` и измените `ViewDidLoad` будет похожа на работу в следующем фрагменте кода:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Обрабатывать colorPickerControllerDidFinish: сообщение** — Если `ViewController` — закончилась, iOS будет отправлять сообщение `colorPickerControllerDidFinish:` для `WeakDelegate`. Нам необходимо создать C# метод, который может обработать это сообщение. Чтобы сделать это, мы создадим C# метода и затем создавать его с `ExportAttribute`. Изменить `ViewController`и добавьте в класс следующий метод:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Запустите приложение. Он будет работать так же, как и раньше, но он использует слабый делегат вместо строгого делегата. На этом этапе вы успешно выполнили это пошаговое руководство. Теперь вы должны понимать, как создавать и использовать привязки проект Xamarin.iOS.

## <a name="summary"></a>Сводка

В этой статье приведены пошаговые инструкции по процесс создания и использования привязки проект Xamarin.iOS. Сначала мы рассмотрели, как скомпилировать существующей библиотеки Objective-C в статическую библиотеку. Затем мы узнали, как создать привязки проекта Xamarin.iOS и как использовать Sharpie цели для создания определений API для библиотеки Objective-C. Мы рассмотрели, как обновить и настроить созданный определений API, чтобы сделать их пригодными для предоставления широкой публике. После завершения привязки проекта Xamarin.iOS, мы перешли использование этой привязки в приложении Xamarin.iOS, уделив особое внимание использовать надежный делегаты и слабых делегатах.

## <a name="related-links"></a>Связанные ссылки

- [Пример привязки (пример)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Привязка библиотек Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Сведения о привязке](~/cross-platform/macios/binding/overview.md)
- [Привязки типов справочное руководство](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin для разработчиков Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Рекомендации по проектированию на основе Framework](http://msdn.microsoft.com/library/ms229042.aspx)
- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
