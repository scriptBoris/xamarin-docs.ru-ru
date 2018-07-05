---
title: Квалификаторы ресурсов и параметры визуализации
description: В этом разделе объясняется, как определять ресурсы, которые будут использоваться только в том случае, при совпадении некоторые значения квалификатора. Простой пример — это ресурс строки с указанием языка. Строковый ресурс можно определить значение по умолчанию, с помощью других альтернативных ресурсы, определенные для дополнительных языков. Все типы ресурсов могут быть уточнены, включая сам макет.
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: 7bc8c1066e557085c1bf34f77765edbb2259ba7a
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403303"
---
# <a name="resource-qualifiers-and-visualization-options"></a>Квалификаторы ресурсов и параметры визуализации

_В этом разделе объясняется, как определять ресурсы, которые будут использоваться только в том случае, при совпадении некоторые значения квалификатора. Простой пример — это ресурс строки с указанием языка. Строковый ресурс можно определить значение по умолчанию, с помощью других альтернативных ресурсы, определенные для дополнительных языков. Все типы ресурсов могут быть уточнены, включая сам макет._


## <a name="custom-device-configurations"></a>Конфигурации пользовательских устройств

Android можно найти в множество устройств и разрешений экрана.
Для разработки пользовательских интерфейсов, предназначенных для многих устройств, конструкторе есть множество конфигураций устройств, встроенные. Она также поддерживает добавление дополнительных устройств конфигурации; Эти конфигурации основаны на *квалификаторы* , указываемое для различения одну конфигурацию устройства из другого. Существует много различных типов, квалификаторов. Дополнительные сведения об этих типах ресурсов см. в разделе [ресурсы Android](~/android/app-fundamentals/resources-in-android/index.md).

В нижней части селектора устройства — меню **Настройка** параметра, как показано ниже:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Меню селектора устройства](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Меню селектора устройства](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


Выбрав **Настройка** отображает диалоговое окно, который можно использовать для просмотра доступных аппаратных конфигураций. При нажатии кнопки **определения устройств** вкладке представлен список всех определений известных устройств:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Диспетчер AVD](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Диспетчер AVD](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


Устройства, предварительно настроенного в конструкторе нельзя изменить. Тем не менее, можно щелкнуть **создание устройства...**  для определения пользовательского определения устройства. Кроме того, можно выбрать существующее определение и нажмите кнопку **клонирования...**  для использования в качестве отправной точки для нового определения.
Например, нажатие кнопки **Nexus 5** определение и нажав кнопку **клонирования...**  представляет следующее диалоговое окно:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Клонировать устройствами](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Клонировать устройствами](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


На следующем снимке экрана имя изменяется на **Nexus 5 Custom** и параметры устройства также изменяются для создания нового пользовательского определения устройства. В этом примере **книжной** отключена, чтобы определение устройства доступен только для альбомной:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Пользовательское устройство](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Пользовательское устройство](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


При нажатии кнопки **Клонировать устройство** создается новое определение устройства, которое появится в списке **Определения устройств**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Обновление устройства определений](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Обновление устройства определений](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


Обратите внимание на то, что каждое определение устройства, созданные пользователем отображается с зеленым значком, как показано выше. При возврате к **устройства** меню селектора пользовательское определение устройства представлены в верхней области списка (Если вы не видите пользовательские настройки устройства в этом списке, попробуйте перезапустить интегрированной среды разработки):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Пользовательское устройство отображается в списке устройств](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Пользовательское устройство отображается в списке устройств](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


Выбор этой конфигурации устройства изменяет макет в соответствии с настройками, созданную ранее (в этом режиме вариантов, только для альбомной):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Пользовательское устройство используется](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Пользовательское устройство используется](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>Параметры квалификатора ресурса

**Параметры квалификатора ресурса** можно получить, щелкнув многоточие справа от **конфигурации устройства** параметры:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Параметры квалификатора ресурса](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Параметры квалификатора ресурса](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


Это диалоговое окно отображает раскрывающееся меню для следующие квалификаторы ресурсов:

-   **Язык** &ndash; отображает доступные языковые ресурсы и предлагает возможность добавлять новые ресурсы языка и региона.

-   **Режим пользовательского интерфейса** &ndash; режимы отображения списков (таких как **Автомобильная док-станция** и **настольная док-станция**) а также направлениями макета.

Каждый из этих раскрывающееся меню откроется новый диалоговым окнам, где можно выбрать и настроить квалификаторы ресурсов (как описано ниже).



### <a name="language"></a>Язык

**Языка** раскрывающемся списке перечислены только тех языков, содержащих ресурсы, определенные (или **все языки**, который используется по умолчанию). Тем не менее, есть также **добавьте язык и регион...**  параметр, позволяющий добавить новый язык в списке:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Добавьте язык и регион](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Добавьте язык и регион](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


При нажатии кнопки **добавьте язык и регион...** , **Выбор языка** откроется диалоговое окно, отображающее список раскрывающегося списка доступных языков и регионов:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Список языков](resource-qualifiers-images/vs/10-languages.png "список языков")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Список языков](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


В этом примере мы выбрали **fr (французский)** для языка и **BE** (Бельгия) для региональных диалект французского языка. Обратите внимание, что **регион** поле является необязательным, так как многие языки можно указывать без учета для конкретных регионов. Когда **языка** раскрывающееся меню открывается снова, она отображает добавленные языка или региона ресурса:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Язык и регион,](resource-qualifiers-images/vs/11-language-region-added.png "выбранного языка и региона")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Язык и регион выбранных](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


Обратите внимание, что если вы добавляете новый язык, но не создавать новые ресурсы для его, больше не будет добавлен язык будет показан в следующий раз вы откроете проект.



### <a name="ui-mode"></a>Режим пользовательского интерфейса

При нажатии кнопки **режим пользовательского интерфейса** отображается раскрывающееся меню, список режимов, таких как **обычный**, **Автомобильная док-станция**, **настольная док-станция**, **Телевизора**, **устройство**, и **Watch**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Меню режим пользовательского интерфейса](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

Ниже этот список — режимы ночь **не ночь** и **ночь**, а затем направлениями макета **слева направо** и **справа налево** (для сведения о **слева направо** и **справа налево** параметры, см. в разделе [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).
Последние элементы на **параметры квалификатора ресурса** диалоговое окно, **округления экраны** (для использования с Android Wear) или **округляет экраны** (сведения о цикле и не циклический экраны, см. в разделе [макеты](https://developer.android.com/training/wearables/ui/layouts.html)).
Дополнительные сведения о режимах Android пользовательского интерфейса см. в разделе [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Меню режим пользовательского интерфейса](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

Ниже этот список — режимы ночь **не ночь** и **ночь**, а затем направлениями макета **слева направо** и **справа налево**. Дополнительные сведения о режимах Android пользовательского интерфейса см. в разделе [UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/).
Сведения о **слева направо** и **справа налево** параметры, см. в разделе [LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/).

### <a name="round-screen"></a>Круглый экран

Последний элемент в **параметры квалификатора ресурса** диалоговое окно является **Round экрана** меню. Это меню позволяет выбрать либо **округления экраны** (для использования с Android Wear) или **прямоугольный экраны**:

[![Круглый экран меню](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>Параметры панели действий

**Параметры панели действий** доступен значок слева от значок кисти (редактор темы):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Параметры панели действий](resource-qualifiers-images/vs/14-action-bar.png "параметры панели действий")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Параметры панели действий](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


Этот значок открывает контекстном диалоговое окно, которое позволяет выбрать один из трех режимов панели действий:

-   **Стандартный** &ndash; состоит из эмблемы или значок и заголовок текста с необязательным подзаголовок.

-   **Список** &ndash; режим списка переходов. Вместо текста статического заголовка, этот режим предоставляет меню списка для навигации в пределах действия (то есть можно было представить пользователю как раскрывающийся список).

-   **Вкладки** &ndash; режим вкладки навигации. Вместо текста статического заголовка этот режим представляет собой серию вкладки для навигации в пределах действия.



## <a name="themes"></a>Темы

**Темы** раскрывающееся меню отображаются все темы, определенных в проекте. Выбрав **более темы** открывает диалоговое окно со списком всех темы установленный пакет SDK Android, как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Дополнительные темы списка тем](resource-qualifiers-images/vs/15-theme-menu-sml.png "более темы списка тем")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Дополнительные темы списка тем](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


При выборе темы области конструктора обновляется, отображая эффект применения новой темы. Обратите внимание, что это изменение будет сделано только если **ОК** кнопки в **темы** диалоговое окно. После выбора темы, она будет включаться в **темы** раскрывающееся меню как показано ниже:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Теперь доступна "светлой" теме](resource-qualifiers-images/vs/16-light-theme.png "теперь доступна \"светлой\" теме")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Теперь доступна "светлой" теме](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Версия Android

Android **версии** селектор задает версии Android, который используется для подготовки к просмотру макет в конструкторе. Селектор содержит все версии, которые совместимы с версией целевой платформы проекта:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Список версий Android](resource-qualifiers-images/vs/17-android-version.png "списка Android версий")

Можно задать версию целевой платформы в параметрах проекта в разделе **свойства > приложение > компиляция с помощью версии Android**. Дополнительные сведения о целевой версии платформы, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).

Набор мини-приложения на панели элементов определяется версию целевой платформы проекта. Это также справедливо для свойств, доступных в **окно "Свойства"**. Список доступных мини-приложения является *не* определяются значения, выбранного в **версии** выделения для панели инструментов. Например, при выборе целевой версии проекта Android 4.4, Android 6.0 по-прежнему можно выбрать в селекторе версии инструментов, чтобы увидеть, как выглядит проекта в Android 6.0, но вы не сможете добавить мини-приложения, относящиеся к Android 6.0 &ndash;  Вы по-прежнему возможен в мини-приложения, доступные в Android 4.4.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Список версий Android](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

Версию целевой платформы, которые можно задать в параметрах проекта в разделе **параметры проекта > Создать > Общие** раздел. Дополнительные сведения о целевой версии платформы, см. в разделе [уровнях API Android понимание](~/android/app-fundamentals/android-api-levels.md).

Набор мини-приложения на панели элементов определяется версию целевой платформы проекта. Это также справедливо для свойств, доступных в **панели свойство**. Список доступных мини-приложения является *не* определяются значения, выбранного в **версии** выделения для панели инструментов. Например, при выборе целевой версии проекта Android 4.4, Android 6.0 по-прежнему можно выбрать в селекторе версии инструментов, чтобы увидеть, как выглядит проекта в Android 6.0, но вы не сможете добавить мини-приложения, относящиеся к Android 6.0 &ndash;  Вы по-прежнему возможен в мини-приложения, доступные в Android 4.4.

-----
