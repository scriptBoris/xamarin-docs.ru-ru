---
title: Квалификаторы ресурсов и параметры визуализации
description: В этом разделе объясняется, как определять ресурсы, которые будут использоваться только в том случае, если некоторые значения квалификатора сопоставляются. Простой пример — это ресурс строки доменного языка. По умолчанию, можно определить строковый ресурс с других дополнительных ресурсов, определенных для дополнительных языков. Все типы ресурсов могут иметь полное имя, включая сам макет.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bc9eb145b6d9ed7bd441d625f533c5cbbd87fccd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771896"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Квалификаторы ресурсов и параметры визуализации

_В этом разделе объясняется, как определять ресурсы, которые будут использоваться только в том случае, если некоторые значения квалификатора сопоставляются. Простой пример — это ресурс строки доменного языка. По умолчанию, можно определить строковый ресурс с других дополнительных ресурсов, определенных для дополнительных языков. Все типы ресурсов могут иметь полное имя, включая сам макет._


## <a name="custom-device-configurations"></a>Пользовательские настройки

Android доступен на множество устройств и уровней разрешения экрана.
Для разработки пользовательских интерфейсов, предназначенных для разных устройств, конструктор поставляется с различными конфигурациями устройств, встроенные. Оно также поддерживает добавление дополнительных устройств конфигурации; на основе этих конфигураций *квалификаторы* следует отличать одну конфигурацию устройства из другого. Существует множество различных типов, квалификаторов. Дополнительные сведения об этих типах ресурсов см. в разделе [Android ресурсов](~/android/app-fundamentals/resources-in-android/index.md).

В нижней части селектора устройства меню — **Настройка** параметра, как показано ниже:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Меню селектора устройства](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Меню селектора устройства](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


При выборе **Настройка** отображает диалоговое окно, который можно использовать для просмотра конфигураций доступных устройств. При нажатии кнопки **устройств** вкладке появится список всех определений известных устройств:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Диспетчер AVD](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Диспетчер AVD](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


Не удается изменить устройства, предварительно настроен в конструкторе. Однако можно нажать кнопку **создание устройства...**  для определения пользовательских устройств. Кроме того, можно выбрать существующее определение и нажмите кнопку **клон...**  ее использование в качестве отправной точки для нового определения.
Например, при выборе **Nexus 5** определения и щелкнув **клон...**  представляет следующее диалоговое окно:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Клонирование устройства](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Клонирование устройства](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


На следующем снимке экрана присвоено имя **Nexus 5 пользовательских** и изменяются параметры устройства, чтобы создать новое определение пользовательских устройств. В этом примере **Книжная** отключена, чтобы определение устройства доступен только для альбомная:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Настраиваемые параметры устройства](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Настраиваемые параметры устройства](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


При нажатии кнопки **Клонировать устройство** создается новое определение устройства, которое появится в списке **Определения устройств**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Обновленные определения](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Обновленные определения](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Обратите внимание, что каждое определение устройства, созданные пользователем отображается с зеленым значком как показано выше. При возвращении к **устройства** меню селектора новое определение пользовательских устройств представлены в верхнем разделе списка (Если вы не видите конфигурации пользовательских устройств в этом списке, попробуйте перезапустить IDE):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Настраиваемые параметры устройства отображается в списке устройств](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Настраиваемые параметры устройства отображается в списке устройств](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


При выборе этой конфигурации устройства позволяет изменить макет для обеспечения соответствия настройки, созданные ранее (в этот режим вариантов, доступный только для альбомная).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Настраиваемые параметры устройства используется](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Настраиваемые параметры устройства используется](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Параметры классификатора ресурсов

**Параметры классификатора ресурсов** можно получить, щелкнув значок с треугольником вниз справа от **конфигурации устройства** параметры:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Параметры классификатора ресурсов](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Параметры классификатора ресурсов](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Это диалоговое окно предоставляет раскрывающиеся списки для следующих квалификаторов ресурсов:

-   **Язык** &ndash; отображает доступные языковые ресурсы и предлагает возможность добавить новые ресурсы для языка или региона.

-   **Режим пользовательского интерфейса** &ndash; режимы отображения списков (такие как **Car Dock** и **Dock поддержки**) и направлениями макета.

Каждый из этих в раскрывающемся меню открывается новый диалоговым окнам, где можно выбрать и настроить квалификаторами ресурсов (как описано далее).



### <a name="language"></a>Язык

**Язык** выпадающем списке перечислены только тех языков, которые имеют определенные ресурсы (или **все языки**, используемого по умолчанию). Однако есть также **добавить язык и регион...**  вариант, позволяющий добавить новый язык в списке:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Добавление языка или региона](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Добавление языка или региона](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


При нажатии кнопки **добавить язык и регион...** , **Выбор языка** откроется диалоговое окно для отображения раскрывающихся списков доступных языков и регионов:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Список языков](resource-qualifiers-images/vs/10-languages.png "список языков")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Список языков](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


В этом примере мы выбрали **fr (французский)** для языка и **BE** (Бельгия) для французского языка региональных диалект. Обратите внимание, что **область** поле является обязательным, потому что многие языки указываются без учета для конкретных регионов. Когда **языка** выпадающем списке будет открыто снова, он отображает ресурсов добавляются языка или региона:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Язык и регион,](resource-qualifiers-images/vs/11-language-region-added.png "зависимости от выбранного языка и региона")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Язык и регион выбранных](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Обратите внимание, что если вы добавляете новый язык, но не создаются новые ресурсы для он больше не будет добавлен язык отображаться при очередном открытии проекта.



### <a name="ui-mode"></a>Режим пользовательского интерфейса

При нажатии кнопки **режиме пользовательского интерфейса** отображаются в раскрывающемся меню, список режимов, такие как **обычный**, **автомобиль Dock**, **поддержки Dock**, **Телевизора**, **устройство**, и **Контрольные значения**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Меню в режиме пользовательского интерфейса](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Ниже этого списка перечислены режимы ночь **не ночь** и **ночь**, а затем направлениями макета **слева направо** и **справа налево** (для сведения о **слева направо** и **справа налево** , обнаружить [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
Последние элементы в **параметры классификатора ресурсов** диалогового окна, **округления экраны** (для использования с Android носят) или **округляет экраны** (сведения о цикле и экраны не циклическим. в разделе [макеты](https://developer.android.com/training/wearables/ui/layouts.html)).
Дополнительные сведения о режимах Android пользовательского интерфейса см. в разделе [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Меню в режиме пользовательского интерфейса](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

Ниже этого списка перечислены режимы ночь **не ночь** и **ночь**, а затем направлениями макета **слева направо** и **справа налево**. Дополнительные сведения о режимах Android пользовательского интерфейса см. в разделе [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Сведения о **слева направо** и **справа налево** , обнаружить [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Round экрана

Последний элемент в **параметры классификатора ресурсов** диалоговое окно **Round экрана** меню. В этом меню можно выбрать либо **округления экраны** (для использования с Android носят) или **прямоугольный экраны**:

[![Round меню экрана](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Параметры панели действий

**Параметры панели действий** доступен значок слева от значок кисти (редактор):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Параметры панели действий](resource-qualifiers-images/vs/14-action-bar.png "параметры панели действий")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Параметры панели действий](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Этот значок открывает popover диалогового окна, который предоставляет способ выбрать один из трех режимов панели действий:

-   **Стандартная** &ndash; состоит из эмблемы или значок и заголовок текста с необязательным подзаголовок.

-   **Список** &ndash; режим списка навигации. Вместо статических заголовок текста, этот режим предоставляет меню списка для навигации в действии (то есть оно может быть представлено для пользователя как раскрывающийся список).

-   **Вкладки** &ndash; режима навигации перехода. Вместо текста заголовка статическим этот режим представляет ряд вкладок для навигации в действии.



## <a name="themes"></a>Темы

**Темы** раскрывающееся меню отображает все темы, определенные в проекте. При выборе **более темы** открывает диалоговое окно со списком всех темы установленный пакет SDK Android, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Дополнительные темы списка](resource-qualifiers-images/vs/15-theme-menu-sml.png "более темы списка")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Дополнительные темы списка](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


При выборе темы рабочей области конструктора обновляется, чтобы показать эффект новой темы. Обратите внимание, что это изменение будет сделано только тогда, когда **ОК** кнопки в **темы** диалогового окна. После выбора темы, он будет включен в **темы** раскрывающегося меню, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Светлая тема теперь доступна](resource-qualifiers-images/vs/16-light-theme.png "теперь доступен \"светлой\" теме")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Теперь доступна "светлой" теме](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Версия Android

Android **версии** селектор задает Android версию, используемую для отрисовки макета в конструкторе. Селектор содержит все версии, которые совместимы с версию целевой платформы проекта:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Список версий Android](resource-qualifiers-images/vs/17-android-version.png "версии список Android")

Требуемой версии .NET framework, которые можно задать в параметрах проекта в разделе **свойства > приложения > компиляция с помощью версии Android**. Дополнительные сведения о целевой версии платформы см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).

Набор мини-приложения, доступные на панели инструментов определяется версию целевой платформы проекта. Это также верно для свойств, доступных в **окно свойств**. Список доступных мини-приложения — *не* определяется значение, выбранное в **версии** селектор на панели инструментов. Например, при выборе целевой версии проекта Android 4.4 Android 6.0 по-прежнему можно выбрать в селекторе версии инструментов, чтобы увидеть, как выглядит проекта в Android 6.0, но его нельзя добавить мини-приложения, которые относятся к Android 6.0 &ndash;  Вы по-прежнему будет сокращено до мини-приложений, доступных в Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Список версий Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

Требуемой версии .NET framework, которые можно задать в параметрах проекта в разделе **параметры проекта > сборки > Общие** раздела. Дополнительные сведения о целевой версии платформы см. в разделе [основные сведения об уровнях Android API](~/android/app-fundamentals/android-api-levels.md).

Набор мини-приложения, доступные на панели инструментов определяется версию целевой платформы проекта. Это также верно для свойств, доступных в **Pad свойство**. Список доступных мини-приложения — *не* определяется значение, выбранное в **версии** селектор на панели инструментов. Например, при выборе целевой версии проекта Android 4.4 Android 6.0 по-прежнему можно выбрать в селекторе версии инструментов, чтобы увидеть, как выглядит проекта в Android 6.0, но его нельзя добавить мини-приложения, которые относятся к Android 6.0 &ndash;  Вы по-прежнему будет сокращено до мини-приложений, доступных в Android 4.4.

-----
