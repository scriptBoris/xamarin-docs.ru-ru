---
title: 'Пошаговое руководство: Привязка библиотеки Objective-C iOS'
description: В этой статье предоставляет практические руководство по созданию Xamarin.iOS привязку для существующей библиотеки Objective-C, InfColorPicker. Она охватывает подразделы, такие как компиляция статической библиотеки Objective-C, привязав его и использование привязки в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 15e1f53d053046b4a51666647ac846366d3c858b
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2018
ms.locfileid: "34335220"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Пошаговое руководство: Привязка библиотеки Objective-C iOS

_В этой статье предоставляет практические руководство по созданию Xamarin.iOS привязку для существующей библиотеки Objective-C, InfColorPicker. Она охватывает подразделы, такие как компиляция статической библиотеки Objective-C, привязав его и использование привязки в приложении Xamarin.iOS._

При работе на iOS, могут возникнуть случаи, где требуется использовать библиотеку Objective-C сторонних разработчиков. В такой ситуации можно использовать Xamarin.iOS _привязки проекта_ для создания [C# привязки](~/cross-platform/macios/binding/overview.md) , позволит использовать библиотеки, в приложениях Xamarin.iOS.

Обычно в экосистеме iOS можно найти библиотеки 3 видов:

* Как файл предкомпилированного статической библиотеки с `.a` расширения вместе с его заголовков (h-файлы). Например [библиотеки Google Analytics](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Как предкомпилированного Framework. Это просто папка, содержащая статической библиотеки, заголовки и иногда дополнительные ресурсы с `.framework` расширения. Например [Google AdMob библиотеки](https://developers.google.com/admob/ios/download).
* Как только файлы исходного кода. Например, библиотеку, содержащую только что `.m` и `.h` Objective C-файлов.

В случае первого и второго уже будет предкомпилированного CocoaTouch статической библиотеки, в этой статье основное внимание уделяется третий сценарий. Помните, что прежде чем создать привязку, всегда проверяйте лицензии, предоставляемый библиотекой, чтобы обеспечить бесплатно привязать его.

В этой статье приводятся пошаговые указания создания привязки проекта, используя открытый исходный код [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C проект в качестве примера, но все сведения в этом руководстве можно адаптировать для использования с любым Библиотека Objective-C сторонних разработчиков. Библиотека InfColorPicker предоставляет контроллер для повторного использования представления, позволяющий пользователю выбирать цвета в его представление HSB выбора цвета более удобной для пользователей.

[![](walkthrough-images/run01.png "Пример библиотеки InfColorPicker под управлением iOS")](walkthrough-images/run01.png#lightbox)

Мы рассматриваются все шаги, необходимые для использования этого конкретного API Objective-C в Xamarin.iOS следующее.

- Во-первых мы создадим статической библиотеки Objective-C, с помощью Xcode.
- Затем мы привязать этот статической библиотеки с Xamarin.iOS.
- Теперь Показать как Sharpie цель может снизить рабочую нагрузку путем автоматического создания некоторые (но не все) необходимые определений API, необходимые для привязки Xamarin.iOS.
- Наконец мы создадим Xamarin.iOS приложение, которое использует привязку.

Образец приложения демонстрируют использование строгого делегат для взаимодействия между InfColorPicker API и код C#. После мы рассмотрели использование строгого делегата, мы обсудим способов использования слабых делегатах для выполнения тех же задач.

## <a name="requirements"></a>Требования

В этой статье предполагается, что вы имеете общее представление о Xcode и языка C цель, и вы прочитали нашей [привязки Objective-C](~/cross-platform/macios/binding/index.md) документации. Кроме того следующие необходимые для выполнения действия, которые отображаются:

-  **Xcode и пакет SDK для iOS** -Apple Xcode и последнюю API iOS необходимо установить и настроить на компьютере разработчика.
-  **[Средства командной строки Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -командной строки Xcode необходимо установить для текущей установленной версии Xcode (см. ниже дополнительные сведения об установке).
-  **Visual Studio для Mac или Visual Studio** -последнюю версию Visual Studio для Mac или Visual Studio должен быть установлен и настроен на компьютере разработчика. Apple Mac необходим для разработки приложения Xamarin.iOS и при использовании Visual Studio необходимо соединение с [создавайте Xamarin.iOS узла](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **Последнюю версию цель Sharpie** -текущая копия средство Sharpie цель загружаются из [здесь](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Если уже установлена Sharpie цель, можно обновить его до последней версии с помощью `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Установка средств Xcode командной строки

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)


Как уже говорилось выше, мы будем использовать средства командной строки Xcode (в частности `make` и `lipo`) в этом пошаговом руководстве. `make` Команда — это очень распространенный Unix программа, которая будет автоматически компиляции исполняемых программ и библиотек с помощью _makefile_ , указывающий, построение программы. `lipo` Команда является OS X служебной программы командной строки для создания файлов с несколькими архитектуры, он будет объединять несколько `.a` файлов в один файл, который может использоваться во всех архитектур оборудования.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Как уже говорилось выше, мы будем использовать средства командной строки Xcode на **узла построения Mac** (в частности `make` и `lipo`) в этом пошаговом руководстве. `make` Команда — это очень распространенный Unix программа, которая будет автоматически компиляции исполняемых программ и библиотек с помощью _makefile_ определяет способ построения программы. `lipo` Команда является OS X служебной программы командной строки для создания файлов с несколькими архитектуры, он будет объединять несколько `.a` файлов в один файл, который может использоваться во всех архитектур оборудования.


-----

В соответствии с Apple [построение из командной строки с Xcode часто задаваемые вопросы о](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) документации в OS X 10.9 и более поздней версии, **загружает** области Xcode **предпочтения** диалоговое окно не Теперь поддерживаются загрузка программы командной строки.

Необходимо использовать один из следующих методов для установки средств:

- **Установите Xcode** — при установке Xcode, они поступают распространение с помощью программы командной строки. В OS X 10.9 оболочки (установлен в `/usr/bin`), можно сопоставить средство, включенное в `/usr/bin` соответствующий инструмент в Xcode. Например `xcrun` команду, которая позволяет найти или запустить из командной строки любого средства в Xcode.
- **Приложение терминалов** -терминала, в приложении можно установить программы командной строки, запустив `xcode-select --install` команды:
    - Запуск приложения терминала.
    - Тип `xcode-select --install` и нажмите клавишу **ввод**, например:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Вам будет предложено установить программы командной строки, нажмите кнопку **установить** кнопка: [ ![ ] (walkthrough-images/xcode01.png "установке средств командной строки")](walkthrough-images/xcode01.png#lightbox)

    - Средства будет загружено и установлено из серверы Apple: [ ![ ] (walkthrough-images/xcode02.png "загрузка средств")](walkthrough-images/xcode02.png#lightbox)

- **Загружаемые файлы для разработчиков Apple** -доступен пакет средств командной строки [загрузки для разработчиков Apple]() веб-страницы. Войти с использованием своего идентификатора Apple, а затем найти и загрузить средства командной строки: [ ![ ] (walkthrough-images/xcode03.png "поиск программы командной строки")](walkthrough-images/xcode03.png#lightbox)

С установленными средствами командной строки мы готовы продолжить пошагового руководства.

## <a name="walkthrough"></a>Пошаговое руководство

В этом пошаговом руководстве будет обсуждаться следующие действия:

- **[Создание статической библиотеки](#Creating_A_Static_Library)**  -этот шаг включает в себя создание статической библиотеки из **InfColorPicker** код Objective-C. Статическая библиотека будет иметь `.a` расширение имени файла и будут включены в сборку .NET библиотеки проекта.
- **[Создайте проект привязки Xamarin.iOS](#Create_a_Xamarin.iOS_Binding_Project)**  -после получения статической библиотеки, оно будет использовано для создания проекта Xamarin.iOS привязки. Привязка проекта состоит из статической библиотеки, которую мы только что создали и метаданные в форме кода C#, объясняется, как можно использовать API Objective-C. Эти метаданные часто называют определения API. Мы будем использовать **[Sharpie цель](#Using_Objective_Sharpie)** помощь с создавать определения API.
- **[Нормализовать определения интерфейсов API](#Normalize_the_API_Definitions)**  — Sharpie цель проделали большую работу из помощь, но не все. Мы рассмотрим некоторые изменения, которые необходимо внести для определения интерфейсов API, прежде чем можно будет использовать.
- **[Используйте библиотеку привязки](#Using_the_Binding)**  -и, наконец, мы создадим приложение Xamarin.iOS показано, как использовать созданный привязки проекта.

Теперь, чтобы понять, какие шаги, давайте перейдем к остальной части пошагового руководства.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Создание статической библиотеки

Если мы проверить код для InfColorPicker в Github:

[![](walkthrough-images/image02.png "Проверьте код для InfColorPicker в Github")](walkthrough-images/image02.png#lightbox)

Появятся следующие три каталоги в проекте:

- **InfColorPicker** -этот каталог содержит код Objective-C для проекта.
- **PickerSamplePad** -этот каталог содержит пример проекта iPad.
- **PickerSamplePhone** -этот каталог содержит пример проекта iPhone.

Давайте загрузить проект InfColorPicker из [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) и распакуйте его в каталоге выбор. Открыв целевой Xcode для `PickerSamplePhone` проекта, мы видим следующую структуру проекта в навигаторе Xcode:

[![](walkthrough-images/image03.png "Структура проекта в навигаторе Xcode")](walkthrough-images/image03.png#lightbox)

Этот проект обеспечивает повторное использование кода Непосредственное добавление InfColorPicker исходный код (в окне «красный») в каждый проект примера. Код для примера проекта находится внутри синей рамки. Поскольку данному проекту не предоставить нам статической библиотеки, это необходимо для нас создайте проект Xcode для компиляции статической библиотеки.

Первый шаг — добавить InfoColorPicker исходного кода в статическую библиотеку. Чтобы добиться этого, давайте выполните следующие действия:

1. Запустите Xcode.
2. Из **файл** меню выберите **New** > **проекта...** :

    [![](walkthrough-images/image04.png "Запуск нового проекта")](walkthrough-images/image04.png#lightbox)
3. Выберите **Framework & Библиотека**, **Cocoa Touch статическая библиотека** шаблона и нажмите кнопку **Далее** кнопки:

    [![](walkthrough-images/image05.png "Выберите шаблон Cocoa Touch статической библиотеки")](walkthrough-images/image05.png#lightbox)

4. Введите `InfColorPicker` для **имя проекта** и нажмите кнопку **Далее** кнопки:

    [![](walkthrough-images/image06.png "Введите имя проекта InfColorPicker")](walkthrough-images/image06.png#lightbox)
5. Выберите расположение для сохранения проекта и нажмите кнопку **ОК** кнопки.
6. Теперь нужно добавить источник из проекта InfColorPicker нашей проекта статической библиотеки. Поскольку **InfColorPicker.h** файл уже существует в нашем статическая библиотека (по умолчанию), Xcode не позволит перезаписать его. Из **Finder**перейдите к исходному коду InfColorPicker в исходном проекте, который мы распаковал из GitHub, скопируйте все файлы InfColorPicker и вставьте их в нашем проекта статической библиотеки:

    [![](walkthrough-images/image12.png "Скопируйте все файлы InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Вернуться к Xcode, щелкните правой кнопкой мыши **InfColorPicker** папку и выберите **добавить файлы в «InfColorPicker...»**:

    [![](walkthrough-images/image08.png "Добавление файлов")](walkthrough-images/image08.png#lightbox)

8. В диалоговом окне Добавление файлов перейдите InfColorPicker файлов исходного кода, которые были скопированы, выделите их все и нажмите кнопку **добавить** кнопки:

    [![](walkthrough-images/image09.png "Выберите все и нажмите кнопку \"Добавить\"")](walkthrough-images/image09.png#lightbox)

9. Исходный код не будет скопирован в данном проекте:

    [![](walkthrough-images/image10.png "Исходный код не будет скопирован в проект")](walkthrough-images/image10.png#lightbox)

10. В навигаторе проекта Xcode, выберите **InfColorPicker.m** файла и закомментируйте две последние строки (из-за способа, эта библиотека была написана, этот файл не используется):

    [![](walkthrough-images/image14.png "Изменение файла InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Теперь нам нужно проверить, есть ли какие-либо платформы, требуемые библиотекой. Эти сведения можно найти в файле README или открыть один из предоставленных образцов проектов. В этом примере используется `Foundation.framework`, `UIKit.framework`, и `CoreGraphics.framework` давайте добавьте их.

12. Выберите **InfColorPicker целевой > этапов построения** и разверните **ссылку двоичного файла с библиотеками** раздела:

    [![](walkthrough-images/image16b.png "Разверните раздел ссылку двоичного файла с библиотеками")](walkthrough-images/image16b.png#lightbox)

13. Используйте **+** кнопку, чтобы открыть диалоговое окно, позволяя добавлять необходимые кадры платформы перечисленных выше:

    [![](walkthrough-images/image16c.png "Добавьте необходимые кадры платформы перечисленных выше")](walkthrough-images/image16c.png#lightbox)

14. **Ссылку двоичного файла с библиотеками** раздел должен выглядеть как на рисунке ниже:

    [![](walkthrough-images/image16d.png "В разделе ссылок двоичного файла с библиотеками")](walkthrough-images/image16d.png#lightbox)

На этом этапе мы закрыть, но не совсем Готово. Будет создана статическая библиотека, но нужно создать его, чтобы создать двоичный, который включает все необходимые архитектуры для устройства iOS и симулятора iOS Fat.

### <a name="creating-a-fat-binary"></a>Создание Fat двоичного файла

Все устройства iOS имеют процессоры на базе архитектуры ARM, разработанные с течением времени. Каждая новая архитектура добавить новых команд и других усовершенствований, поддерживая обратную совместимость. На устройствах iOS у нас есть armv6, armv7, armv7s, наборы инструкций arm64 — несмотря на то что [больше не используется armv6](~/ios/deploy-test/compiling-for-different-devices.md). Имитатор iOS работает не на основе ARM и вместо x86 и симулятора x86_64 на платформе. Мы, значит, нам будет, должен предоставить библиотеки для каждой инструкции задает.

Библиотека Fat является `.a` файл, содержащий все поддерживаемые архитектуры.

Создание двоичного fat — в три этапа:

- Скомпилируйте ARM 7 и ARM64 версия статической библиотеки.
- Скомпилируйте более x86 и x84_64 версией статической библиотеки.
- Используйте `lipo` средство командной строки, чтобы объединить две статические библиотеки в одну.

Хотя эти три шага довольно просты, и может оказаться необходимым повторять их в будущем когда цель библиотека получает обновления, или если мы требуем исправления ошибок. Если принято решение автоматизации этих шагов упростит будущих обслуживание и поддержку привязки проекта iOS.

Можно использовать множество инструментов, можно автоматизировать такие задачи - сценарий оболочки [rake](http://rake.rubyforge.org/), [xbuild](http://www.mono-project.com/docs/tools+libraries/tools/xbuild/), и [сделать](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Когда мы установлены средства командной строки Xcode, мы также установлены убедитесь, чтобы он система сборки, которая будет использоваться в этом пошаговом руководстве. Вот **Makefile** , можно использовать для создания нескольких архитектура общей библиотеки, будут работать на устройстве iOS и симулятора для всех библиотек:

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

Введите **Makefile** команд в редактор обычного текста по своему выбору и обновить разделы с **YOUR ИМЯ_ПРОЕКТА** с именем проекта. Также важно убедиться, что мы приведенным выше инструкциям, сохранение вкладок в инструкции вставки.

Сохраните файл с именем **Makefile** в то же расположение, как статическая библиотека Xcode InfColorPicker, созданной ранее:

[![](walkthrough-images/lib00.png "Сохраните файл с именем файла Makefile")](walkthrough-images/lib00.png#lightbox)

Откройте приложение терминалов на компьютере Mac и перейдите к расположению файла Makefile. Тип `make` в терминалов, нажмите клавишу **ввод** и **Makefile** выполняется:

[![](walkthrough-images/lib01.png "Пример результатов выполнения файла makefile")](walkthrough-images/lib01.png#lightbox)

При запуске убедитесь, вы увидите массу прокрутки по текста. Если все работает правильно, вы увидите слова **сборки успешно** и `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` и `libInfColorPickerSDK.a` файлы будут скопированы в то же расположение, как **Makefile**:

[![](walkthrough-images/lib02.png "LibInfColorPicker armv7.a, libInfColorPicker i386.a и libInfColorPickerSDK.a файлы, созданные в файл Makefile")](walkthrough-images/lib02.png#lightbox)

Вы можете подтвердить архитектур в файловой системе Fat двоичный файл, используя следующую команду:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

После этого должен отобразиться следующее:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

На этом этапе, после завершения первого шага по нашей привязкой iOS путем создания статической библиотеки с помощью Xcode и программы командной строки Xcode `make` и `lipo`. Давайте перемещение к следующему шагу и использовать **Sharpie цель** для автоматизации создания привязок API для нас.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Создание Xamarin.iOS привязки проекта

Прежде чем мы можем использовать **цель Sharpie** для автоматизации процесса привязки, нам нужно создать проект привязки Xamarin.iOS для определения интерфейсов API размещения (, мы будем использовать **Sharpie цель** помощь Построение) и создать привязку для нас.

Давайте выполните следующие действия.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Запустите Visual Studio для Mac.
1. Из **файл** последовательно выберите пункты **New** > **решения...** :

    ![](walkthrough-images/bind01.png "Запуск нового решения")

1. В диалоговом окне нового решения, выберите **библиотеки** > **iOS привязки проекта**:

    ![](walkthrough-images/bind02.png "Выберите iOS привязки проекта")

1. Нажмите кнопку **Далее** кнопки.

1. Введите «InfColorPickerBinding» в качестве **имя проекта** и нажмите кнопку **создать** кнопку, чтобы создать решения:

    ![](walkthrough-images/bind02a.png "Введите InfColorPickerBinding в качестве имени проекта")

Решение будет создано и два файла по умолчанию будут включены:

![](walkthrough-images/bind03.png "Структура решения в обозревателе решений")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. Запустите Visual Studio.

1. Из **файл** последовательно выберите пункты **New** > **проекта...** :

    ![Запуск нового проекта](walkthrough-images/bind01vs.png "запуске нового проекта")

1. В диалоговом окне нового проекта выберите **Visual C# > iPhone & iPad > iOS привязки библиотеки (Xamarin)**:

    [![Выберите привязки библиотеке iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Введите «InfColorPickerBinding» в качестве **имя** и нажмите кнопку **ОК** кнопку, чтобы создать решения.

Решение будет создано и два файла по умолчанию будут включены:

![](walkthrough-images/bind03vs.png "Структура решения в обозревателе решений")

-----

- **ApiDefinition.cs** -этот файл содержит контракты, которые определяют, как будет вставлено Objective-C API в C#.
- **Structs.cs** — этот файл будет содержать все структуры или значения перечисления, которые требуются интерфейсы и делегаты.

Мы будем работать с эти файлы позже в этом пошаговом руководстве. Во-первых необходимо добавить в библиотеку InfColorPicker проект привязки.

### <a name="including-the-static-library-in-the-binding-project"></a>Включая статической библиотеки в проект привязки

У нас есть наш базовый привязки проект готов, то необходимо добавить Fat двоичных библиотеки, созданной ранее для **InfColorPicker** библиотеки.

Выполните следующие действия для добавления в библиотеку.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. Щелкните правой кнопкой мыши **собственных ссылок** папки в панель решения и выберите **добавить собственные ссылки**:

    ![](walkthrough-images/bind04a.png "Добавление ссылок в машинном коде")

1. Перейдите к Fat двоичных мы внесли ранее (`libInfColorPickerSDK.a`) и нажмите клавишу **откройте** кнопки:

    ![](walkthrough-images/bind05.png "Выберите файл libInfColorPickerSDK.a")
1. Файл будет включен в проект:

    ![](walkthrough-images/bind04.png "Файл кода")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Копировать `libInfColorPickerSDK.a` из вашего **узла построения Mac** и вставьте его в проект привязки.

1. Щелкните правой кнопкой мыши проект и выберите **Добавить > существующий элемент...** :

    ![](walkthrough-images/bind04vs.png "Добавление существующего файла")

1. Перейдите к `libInfColorPickerSDK.a` и нажмите клавишу **добавить** кнопки:

    ![](walkthrough-images/bind05vs.png "Добавление libInfColorPickerSDK.a")

1. Файл будет включен в проект.

-----

При **.a** файл добавляется в проект автоматически установит Xamarin.iOS **действие при построении** файла **ObjcBindingNativeLibrary**и создать особый файл с расширением вызывается `libInfColorPickerSDK.linkwith.cs`.


Этот файл содержит `LinkWith` атрибут, который сообщает Xamarin.iOS как дескриптор статической библиотеки, который мы только что добавлен. Содержимое этого файла показано в следующем фрагменте кода:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

`LinkWith` Атрибут определяет статическую библиотеку для проекта, а также некоторые важные компоновщика флаги.


Далее, что необходимо сделать — Создание определения интерфейсов API для проекта InfColorPicker. В целях данного пошагового руководства мы будем использовать Sharpie цель для создания файла **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>С помощью цели Sharpie

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)


Цели Sharpie является командной строки (предоставляемое Xamarin) средство, которое может помочь в создании определений, необходимый для привязки библиотеку Objective-C сторонних производителей для C#. В этом разделе мы будем использовать Sharpie цель для создания начального **ApiDefinition.cs** InfColorPicker проекта.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Цели Sharpie является командной строки (предоставляемое Xamarin) средство, которое может помочь в создании определений, необходимый для привязки библиотеку Objective-C сторонних производителей для C#. В этом разделе мы будем использовать Sharpie цель на нашем **узла построения Mac** для создания начального **ApiDefinition.cs** InfColorPicker проекта.


-----

Чтобы приступить к работе, давайте загрузки файла установщика Sharpie цели, как описано в этом [руководство](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Запустите программу установки и выполните все запросы, на экране с помощью мастера установки для установки Sharpie цель на нашем компьютере разработчика.

После того как мы Sharpie цель успешно установлен, давайте запустить приложение "Терминал" и введите следующую команду, чтобы получить справку по все средства, предоставляемые для помощи в привязке.

```bash
sharpie -help
```

Если мы выполнить команду выше, будут создаваться следующие данные:

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

- **xcode** — это средства дает нам сведения о нашем текущая установка Xcode и версии iOS и Mac API, которые мы установили. Мы используем эти сведения позже при мы создаем нашей привязок.
- **привязать** -мы будем использовать этот инструмент для анализа **.h** файлы в проекте InfColorPicker в исходный **ApiDefinition.cs** и **StructsAndEnums.cs** файлов.

Чтобы получить справку по определенного инструмента Sharpie цель, введите имя средства и `-help` параметр. Например `sharpie xcode -help` возвращает следующие результаты:

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

Перед началом процесса привязки, нам нужно получить сведения о нашей текущей пакетов SDK, введя следующую команду в окне терминала `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Из примера выше, мы видим, что у нас есть `iphoneos9.3` пакет SDK установлен на компьютере. С этими данными мы готовы выполнить синтаксический анализ проекта InfColorPicker `.h` файлы в исходный **ApiDefinition.cs** и `StructsAndEnums.cs` InfColorPicker проекта.

Введите следующую команду в приложении Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Где `[full-path-to-project]` — это полный путь к каталогу где **InfColorPicker** на нашем компьютере находится файл проекта Xcode и [iphone ОС] — с помощью пакета SDK, мы были установлены, как уже отмечалось `sharpie xcode -sdks` команды. Обратите внимание, что в этом примере мы передали  **\*.h** как параметр, который включает *все* файлы заголовков в этом каталоге - обычно вам следует не сделать, однако вместо внимательно прочтите файлы заголовков для поиска верхнего уровня **.h** файл, который ссылается на все другие соответствующие файлы и просто передать Sharpie цель.

Следующие [вывода](walkthrough-images/os05.png) создается в окне терминала:

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

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)


Откройте оба файла в проекте привязки, созданной ранее. Скопируйте содержимое **InfColorPicker.cs** файл и вставьте его в **ApiDefinition.cs** файла, заменяя существующие `namespace ...` блок кода с содержимым  **InfColorPicker.cs** файла (Если оставить `using` операторы без изменения):

![](walkthrough-images/os07.png "Файл InfColorPickerControllerDelegate")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Откройте оба файла в проекте привязки, созданной ранее. Скопируйте содержимое **InfColorPicker.cs** файла (из **узла построения Mac**) и вставьте его в **ApiDefinition.cs** файла, заменяя существующие `namespace ...` блок кода с содержимым **InfColorPicker.cs** файла (Если оставить `using` операторы без изменения).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Нормализовать определения интерфейсов API

Цели Sharpie иногда возникают проблемы при преобразовании `Delegates`, поэтому нам потребуется изменить определение `InfColorPickerControllerDelegate` интерфейса и замените `[Protocol, Model]` строки со следующими:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Чтобы определение выглядит следующим образом.

[![](walkthrough-images/os11.png "Определение")](walkthrough-images/os11.png#lightbox)

Далее мы сделать то же самое с содержимым `InfColorPicker.enums.cs` файла, копирование и вставка их в `StructsAndEnums.cs` файл оставляя `using` инструкции без изменений:

[![](walkthrough-images/os09.png "Содержимое StructsAndEnums.cs файла ")](walkthrough-images/os09.png#lightbox)

Кроме того, возможно, цель Sharpie снабжены привязка, с помощью `[Verify]` атрибуты. Эти атрибуты указывают, что следует проверить, что цель Sharpie выполнялось правильнее сравнение привязка, с помощью исходного объявления C, Objective-C (который будет предоставляться в комментарии перед объявлением связанных). После проверки привязок следует удалить атрибут проверки. Дополнительные сведения см. в [проверьте](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) руководства.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)


На этом этапе привязки проекта должно быть полным и готов к построению. Создадим нашего привязки проекта и убедитесь, что в итоге мы без ошибок:

[Постройте проект привязки и убедитесь, что отсутствуют ошибки](walkthrough-images/os12.png)


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


На этом этапе привязки проекта должно быть полным и готов к построению. Создадим нашего привязки проекта и убедитесь, что в итоге мы без ошибок.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>С помощью привязки

Выполните следующие шаги для создания примера приложения iPhone для использования операций ввода-вывода, созданная выше, привязка библиотеки.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

1. **Создание проекта Xamarin.iOS** — добавить новый проект с именем Xamarin.iOS **InfColorPickerSample** в решение, как показано на следующем снимке экрана:

    ![](walkthrough-images/use01.png "Добавление приложения единое представление")

    ![](walkthrough-images/use01a.png "Задание идентификатора")

1. **Добавьте ссылку на проект привязки** -обновление **InfColorPickerSample** проекта, чтобы он содержит ссылку на **InfColorPickerBinding** проекта:

    ![](walkthrough-images/use02.png "Добавление ссылки на проект привязки")

1. **Создание пользовательского интерфейса iPhone** -дважды щелкните **MainStoryboard.storyboard** файла в **InfColorPickerSample** проекта для редактирования в конструкторе iOS. Добавить **кнопку** к представлению и вызывать его `ChangeColorButton`, как показано ниже:

    ![](walkthrough-images/use03.png "Добавление кнопки в представлении")

1. **Добавить InfColorPickerView.xib** -InfColorPicker Objective-C-библиотека содержит **.xib** файла. Xamarin.iOS не будет содержать это **.xib** в проекте привязки, что вызовет ошибки во время выполнения в приложении образца. Решением этой проблемы является добавление **.xib** файл наш Xamarin.iOS проект. Выберите проект Xamarin.iOS, щелкните правой кнопкой мыши и выберите **Добавить > Добавить файлы**и добавьте **.xib** файла, как показано на следующем снимке экрана:

    ![](walkthrough-images/use04.png "Добавить InfColorPickerView.xib")

1. При появлении запроса, скопируйте **.xib** файл в проект.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. **Создание проекта Xamarin.iOS** -добавить новый проект Xamarin.iOS, с именем **InfColorPickerSample** с помощью **одного представления приложения** шаблона:

    [![проект приложения (Xamarin) iOS](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Выберите шаблон](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Добавьте ссылку на проект привязки** -обновление **InfColorPickerSample** проекта, чтобы он содержит ссылку на **InfColorPickerBinding** проекта:

    ![](walkthrough-images/use02vs.png "Добавьте ссылку на проект привязки")

1. **Создание пользовательского интерфейса iPhone** -дважды щелкните **MainStoryboard.storyboard** файла в **InfColorPickerSample** проекта для редактирования в конструкторе iOS. Добавить **кнопку** к представлению и вызывать его `ChangeColorButton`, как показано ниже:

    ![](walkthrough-images/use03vs.png "Создание пользовательского интерфейса iPhone")

1. **Добавить InfColorPickerView.xib** -InfColorPicker Objective-C-библиотека содержит **.xib** файла. Xamarin.iOS не будет содержать это **.xib** в проекте привязки, что вызовет ошибки во время выполнения в приложении образца. Решением этой проблемы является добавление **.xib** файл к проекту нашей Xamarin.iOS из наших **узла построения Mac**. Выберите проект Xamarin.iOS, щелкните правой кнопкой мыши и выберите **добавить** > **существующий элемент...** и добавьте **.xib** файла.

-----

Теперь давайте кратко рассмотрим протоколов в Objective-C и как мы их обработки в привязке и код C#.

### <a name="protocols-and-xamarinios"></a>Протоколы и Xamarin.iOS

В Objective-C определяет протокол методы (или сообщения), можно использовать в определенных обстоятельствах. По существу они очень похожи на интерфейсы в C#. Одно из основных отличий между протоколом Objective-C и C#-интерфейсом: протоколы могут иметь необязательные методы - методы, которые не требуется реализовать класс. Использует Objective-C @optional используется ключевое слово, чтобы указать, какие методы являются необязательными. Дополнительные сведения о протоколах см [события, протоколы и делегаты](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** имеет один такой протокол, показано в следующем фрагменте кода:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Этот протокол используется **InfColorPickerController** чтобы проинформировать клиентов о том, что пользователь выбран новый цвет и что **InfColorPickerController** завершения работы. Цели Sharpie сопоставить этот протокол, как показано в следующем фрагменте кода:

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

При компиляции библиотеки привязки Xamarin.iOS создаст абстрактный базовый класс с именем `InfColorPickerControllerDelegate`, который реализует этот интерфейс с виртуальными методами.

Что можно реализовать этот интерфейс в приложении Xamarin.iOS двумя способами:

- **Делегировать строгих** -с помощью строгого делегата предполагает создание класса C#, который наследуется от класса `InfColorPickerControllerDelegate` и переопределяет соответствующие методы. **InfColorPickerController** будет использовать экземпляр этого класса для связи с своих клиентов.
- **Слабый делегат** -слабый делегат — это немного другой способ, который включает создание открытого метода в один из классов (такие как `InfColorPickerSampleViewController`) и затем предоставление доступа к этому методу для `InfColorPickerDelegate` протокола `Export` атрибута.

Надежный делегаты позволяют Intellisense, улучшенную инкапсуляцию и безопасность типов. По этим причинам следует использовать надежные делегаты где это возможно, вместо слабый делегат.

В этом пошаговом руководстве мы обсудим обоих методов: сначала обеспечение строгого делегата, а затем о том, как реализовать слабый делегат.

### <a name="implementing-a-strong-delegate"></a>Реализация строгого делегата

Готово Xamarin.iOS приложения с помощью строгого делегата реагировать на `colorPickerControllerDidFinish:` сообщение:

**Подкласс InfColorPickerControllerDelegate** -добавьте новый класс в проект с именем `ColorSelectedDelegate`. Измените класс, чтобы он имел следующий код:

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

Xamarin.iOS будет привязан делегат Objective-C, создав абстрактный базовый класс с именем `InfColorPickerControllerDelegate`. Подкласс этого типа и переопределение `ColorPickerControllerDidFinish` метод для доступа к значению `ResultColor` свойство `InfColorPickerController`.

**Создайте экземпляр ColorSelectedDelegate** -обработчиком событий потребуется экземпляр `ColorSelectedDelegate` типа, созданную на предыдущем шаге. Измените класс `InfColorPickerSampleViewController` и добавьте следующую переменную экземпляра класса:

```csharp
ColorSelectedDelegate selector;
```

**Инициализируйте переменную ColorSelectedDelegate** — чтобы убедиться, что `selector` является допустимым, экземпляр обновить метод `ViewDidLoad` в `ViewController` для сопоставления в следующем фрагменте кода:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Реализуйте метод HandleTouchUpInsideWithStrongDelegate** -Далее реализовать обработчик событий для, когда пользователь касается **ColorChangeButton**. Изменить `ViewController`и добавьте следующий метод:

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

Сначала мы получаем экземпляр `InfColorPickerController` через статический метод и убедитесь, что экземпляр помнить о наших строгого делегат через свойство `InfColorPickerController.Delegate`. Это свойство автоматически созданное нам Sharpie к цели деятельности организации. Затем мы называем `PresentModallyOverViewController` для отображения представления `InfColorPickerSampleViewController.xib` , в котором пользователь может выбрать цвет.

**Запустите приложение** — на этом этапе мы закончили со всеми нашего кода. При запуске приложения, можно изменять цвет фона `InfColorColorPickerSampleView` как показано на следующем снимке экрана:

[![](walkthrough-images/run01.png "Запуск приложения")](walkthrough-images/run01.png#lightbox)

Поздравляем! На этом этапе вы успешно создан и привязать библиотеку Objective-C для использования в приложении Xamarin.iOS. Теперь давайте познакомимся с использованием слабого делегатов.

### <a name="implementing-a-weak-delegate"></a>Реализация слабый делегат

Вместо создания подклассов класса, привязанного к протоколу Objective-C для определенного делегата, Xamarin.iOS также позволяет реализовать методы протокол в любой класс, производный от `NSObject`, декорирования методов с `ExportAttribute`, а затем указав соответствующие селекторов. При использовании этого подхода можно назначить экземпляр класса для `WeakDelegate` свойства вместо `Delegate` свойств. Слабый делегат обеспечивает гибкость класса делегата вниз по иерархии наследования различных вступили в силу. Давайте посмотрим, как реализовать и использовать в приложении Xamarin.iOS слабый делегат.

**Создайте обработчик событий для TouchUpInside** -давайте создадим новый обработчик событий для `TouchUpInside` события Изменение цвета фона кнопки. Этот обработчик заполнит той же роли, как `HandleTouchUpInsideWithStrongDelegate` обработчик мы создан в предыдущем разделе, но будет использовать слабый делегат вместо строгого делегата. Измените класс `ViewController`и добавьте следующий метод:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Обновление ViewDidLoad** -нам нужно изменить `ViewDidLoad` , чтобы он использовал только что созданный обработчик события. Изменить `ViewController` и измените `ViewDidLoad` выглядеть в следующем фрагменте кода:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Обрабатывать colorPickerControllerDidFinish: сообщение** — Если `ViewController` — по завершении операций ввода-вывода будет отправлять сообщения `colorPickerControllerDidFinish:` для `WeakDelegate`. Необходимо создать метод C#, который может обработать это сообщение. Чтобы сделать это, мы создать метод C# и затем оформления его с `ExportAttribute`. Изменить `ViewController`и добавьте в класс следующий метод:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Запустите приложение. Он будет работать точно так, как и раньше, но он использует слабый делегат вместо строгого делегата. На этом этапе вы успешно завершили в данном пошаговом руководстве. Теперь в макете узнаете, как создавать и использовать Xamarin.iOS привязки проекта.

## <a name="summary"></a>Сводка

В этой статье рассмотрены процесс создания и использования Xamarin.iOS привязки проекта. Сначала мы рассмотрели, как скомпилировать существующую библиотеку Objective-C в статическую библиотеку. Затем мы узнали, как создать проект Xamarin.iOS привязки и способ использования Sharpie цель для создания определений API для библиотеки Objective-C. Мы рассмотрели, как обновить и изменить созданный определения API, чтобы сделать их пригодными для общедоступного потребления. После завершения проекта привязки Xamarin.iOS, мы перешли использование этой привязки в приложении Xamarin.iOS фокус на использование делегатов надежный и слабых делегатов.

## <a name="related-links"></a>Связанные ссылки

- [Пример привязки (пример)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Привязка библиотек Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Сведения о привязке](~/cross-platform/macios/binding/overview.md)
- [Привязки типов Справочник](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin для разработчиков Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Рекомендации по проектированию на основе Framework](http://msdn.microsoft.com/library/ms229042.aspx)
