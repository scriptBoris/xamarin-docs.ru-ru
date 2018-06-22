---
title: Возможности разработки материала
description: В этом разделе описаны возможности конструктор, которые облегчают разработчикам создавать макеты материала конструктора с. В этом разделе вводятся и описываются способы использования сетки материалы, материалы цветовую палитру, типографском шкалы и темы редактора.
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: a764efe7f2cadd8c777f8427c0220e45eec662c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773635"
---
# <a name="material-design-features"></a>Возможности разработки материала

_В этом разделе описаны возможности конструктор, которые облегчают разработчикам создавать макеты материала конструктора с. В этом разделе вводятся и описываются способы использования сетки материалы, материалы цветовую палитру, типографском шкалы и темы редактора._


> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Развивать 2016: Все можно создать отличных приложений с материала разработки**

## <a name="overview"></a>Обзор

Конструктор Xamarin.Android включает функции, которые упрощают процесс создания макетов материал конструктора совместимое с. Если вы не знакомы с разработки материал, см. раздел [введение разработки материал](https://www.google.com/design/spec/material-design/introduction.html).

В этом руководстве мы еще рассмотрим следующие возможности конструктора:

-   *Существенные сетки* &ndash; наложенной на рабочей области конструирования, показывающий сетки, пробелы и контуров, помогающие поместить графические элементы макета согласно правилам разработки материал.

-   *Существенные цветовую палитру* &ndash; панель свойств диалоговое окно, которое помогает в выборе цвета из палитры официальный материал конструктора.

-   *Условные обозначения шкалы* &ndash; панель свойств диалоговое окно, которое предоставляет возможность выбора материала совместимое конструктора параметров для `textAppearance` свойства текстовых полей.

-   *Редактор* &ndash; небольшие цветные редактор ресурсов, можно задать сведения о подмножестве цвет темы. Например, можно просмотреть и изменить цвета материалов, таких как `colorPrimary`, `colorPrimaryDark`, и `colorAccent`.

Мы рассмотрим каждую из этих функций имеют и приводятся примеры их использования.



## <a name="material-design-grid"></a>Сетка конструктора материала

Сетка конструктора материал меню доступно из панели инструментов в верхней части конструктора:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Существенные бланк](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Существенные бланк](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

-----

Если щелкнуть значок бланк материал, конструктор отображаются в области конструктора, который включает следующие элементы:

-   Контуры (оранжевый строки)

-   Интервал (зеленым)

-   Сетка (синие линии)

Эти элементы можно увидеть в следующий снимок экрана:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Рисунок, пробелы и сетки](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Рисунок, пробелы и сетки](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Каждый из этих элементов наложения является настраиваемым. При нажатии кнопки с многоточием рядом с меню бланк материал, popover диалоговое окно открывается, можно включить или отключить сетку, настройте местоположение контуров и задать расстояние между. Обратите внимание, что все значения выражаются в `dp` (плотность независимых пикселях):

![Сетка, рисунок и интервалы конфигурации](material-design-features-images/vs/03-grid-configuration.png)

Чтобы добавить новый рисунок, введите новое значение смещения в **смещение** выберите расположение (**левой**, **верхней**, **правой**, или  **нижней**) и щелкните значок, чтобы добавить новый рисунок +.

Аналогичным образом, чтобы добавить новое значение интервала, введите размер и смещение (в точки распространения) в **размер** и **смещение** соответственно. Выберите расположение (**левой**, **верхней**, **правой**, или **нижней**) и нажмите кнопку + значок, чтобы добавить новое значение интервала.

При изменении этих значений конфигурации, они сохраняются в XML-файле макета и используются всякий раз при открытии макета.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Каждый из этих элементов наложения является настраиваемым. При нажатии кнопки вниз треугольник рядом с меню бланк материал, popover диалоговое окно открывается, можно включить или отключить сетку, настройте местоположение контуров и задать расстояние между. Обратите внимание, что все значения выражаются в `dp` (плотность независимых пикселях):

[![Сетка, рисунок и интервалы конфигурации](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

Чтобы добавить новый рисунок, введите новое значение смещения в **смещение** выберите расположение (**левой**, **верхней**, **правой**, или  **нижней**) и щелкните значок, чтобы добавить новый рисунок +.

Аналогичным образом, чтобы добавить новое значение интервала, введите размер и смещение (в точки распространения) в **размер** и **смещение** соответственно. Выберите расположение (**левой**, **верхней**, **правой**, или **нижней**) и нажмите кнопку + значок, чтобы добавить новое значение интервала.

При изменении этих значений конфигурации, они сохраняются в XML-файле макета и используются всякий раз при открытии макета.


## <a name="material-design-color-palette"></a>Существенные разработки цветовой палитры

Каждый элемент панели свойств, который теперь принимает цвет имеет дополнительный значок, можно использовать для откройте цветовой палитре материал конструктора, как показано на этом снимке экрана:

[![Значок цвета](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

Если щелкнуть этот значок, popover диалоговое окно открывается, позволяет настроить цвет этого свойства из цветовой палитры разработки материалы:

[![Существенные разработки цветовой палитры](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

Вверху цветовую палитру отображаются первичного конструктора материал цветов, а в нижней части палитру диапазон оттенки для выбранного основного цвета. Например, при выборе **Indigo**, коллекцию **Indigo** оттенки отображается в нижней части диалогового окна.
При выборе цветового тона цвета свойства меняется на выбранной цветовой тон. В следующем примере `Background Tint` кнопки меняется на *Indigo 500*:

[![Выберите Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` задан код цвета для *Indigo 500* (`#ff3f51b5`), и конструктор обновляет цвет фона кнопки, чтобы отразить это изменение:

[![Изменения оттенок фона](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Дополнительные сведения о цветовой палитры материал конструктора. в разделе разработки материал [каталог палитры цветов](http://www.google.com/design/spec/style/color.html#color-color-palette).

## <a name="typographic-scale"></a>Условные обозначения шкалы

**Внешний вид текста** раздел **свойство** pad **стиль** вкладка содержит значок, который позволяет выберите один из `TextAppearance` стиль, который соответствует дизайну материалы Спецификация:

[![Вкладка стиля](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

Если щелкнуть этот значок, откроется **типографском шкалы** popover диалоговое окно, в которой представлен список предварительно настроенных текста стили, которые можно выбрать из:

[![Выбор стиля текста](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

В следующем примере, щелкнув **отображения 1** примет большего размера шрифта для текста на кнопке **отображения 1**:

[![Стиль отображения 1](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

Стиль текста в **типографском шкалы** диалог следует **темы** параметр. Например если **свет** темы выбирается в конструкторе список стилей зеркал имеющийся текст **свет** темы:

[!["Светлой" теме](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

-----



## <a name="theme-editor"></a>Редактор

**Редактор** позволяет настраивать сведения о подмножестве цвет темы атрибутов. Чтобы открыть **редактор**, щелкните значок кисти на панели инструментов:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Значок редактора темы](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Значок редактора темы](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

-----

Несмотря на то что **редактор** доступен на панели инструментов для всех целевых версий Android и уровни API только подмножество возможностей, описанных ниже доступны, если уровень целевой API более ранняя, чем API 21 (Android 5.0 Без описания операций).

На левой панели **редактор** отображает список цветов, которые составляют выбранного темы (в этом примере мы используем `Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Редактор](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Редактор](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

-----

При выборе цвета в левой части правой панели содержит следующие вкладки для облегчения редактирования цвета:

-   **Наследовать** &ndash; отображает схему наследования стиля для выбранного цвета и список разрешенных цвет и цвет код, присвоенный цвета темы.

-   **Палитра цветов** &ndash; позволяет изменить выбранный цвет в произвольное значение.

-   **Палитра материала** &ndash; позволяет изменять выбранный цвет для значения, соответствующего конструктора материал.

-   **Ресурсы** &ndash; позволяет изменять выбранный цвет к одному из других существующий ресурс в теме цвета.

Давайте рассмотрим из этих вкладок подробное описание каждого из них.



### <a name="inherit-tab"></a>Наследовать вкладки

Как показано в следующем примере **наследовать** вкладке перечисляются наследования стиля для **фона** цвет **темы по умолчанию**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Наследовать вкладки](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Наследовать вкладки](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

-----

В этом примере **темы по умолчанию** наследует стиль, который использует `@color/background_material_dark` , но переопределяет его с `color/material_grey_850`, имеющий значение кода цвета `#ff303030`.
Дополнительные сведения о наследовании стилей см. в разделе [стили и темы](http://developer.android.com/guide/topics/ui/themes.html#Inheritance).



### <a name="color-picker"></a>Палитра

Показано на следующем снимке экрана **палитра**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Палитра цветов](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Палитра цветов](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

-----

В этом примере **фона** цвета можно изменить любое значение различными способами:

-   Щелкните нужный цвет напрямую.
-   Ввод значений цветового тона, насыщенности и яркости.
-   Ввод значений RGB (красный, зеленый, синий) в десятичном формате.
-   Настройка альфа (прозрачности) для выбранного цвета.
-   Непосредственно введя код шестнадцатеричный код цвета.

Цвет, выберите в палитре цветов, *не* ограничен рекомендации по разработке материалы или набор доступных ресурсов.


### <a name="resources"></a>Ресурсы

**Ресурсов** вкладка содержит список цветовые ресурсы, которые уже присутствуют в теме:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ресурсы](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Ресурсы](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

-----

С помощью **ресурсов** вкладку ограничивает выбранных параметров в этот список цветов. Помните, если ресурс цвета, который уже назначен другой части темы, двух соседних элементов пользовательского интерфейса может «выполнять вместе» (поскольку они имеют одинаковый цвет) и будет затруднено для пользователя различать.



### <a name="material-palette"></a>Существенные палитры

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Палитры материал** откроется вкладка **материал разработки цветовую палитру**. Выбрав значение цвета в этой палитре ограничивает выбранный цвет, чтобы он был совместим с рекомендации по проектированию материала.

[![Существенные палитры](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png#lightbox)

Вверху цветовую палитру отображаются первичного конструктора материал цветов, а в нижней части палитру диапазон оттенки для выбранного основного цвета. Например, при выборе **Indigo**, коллекцию **Indigo** оттенки отображается в нижней части диалогового окна.
При выборе цветового тона цвета свойства меняется на выбранной цветовой тон. В следующем примере `Background Tint` кнопки меняется на *Indigo 500*:

![Выберите Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` задан код цвета для *Indigo 500* (`#ff3f51b5`), и конструктор обновляет цвет фона, чтобы отразить это изменение:

[![Изменить оттенок фона](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png#lightbox)

Дополнительные сведения о цветовой палитры материал конструктора. в разделе разработки материал [каталог палитры цветов](http://www.google.com/design/spec/style/color.html#color-color-palette).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

**Палитры материал** откроется вкладка **материал разработки цветовую палитру** описано [предыдущих](#material_palette). Выбрав значение цвета в этой палитре ограничивает выбранный цвет, чтобы он был совместим с рекомендации по проектированию материала.

[![Существенные палитры](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

-----



### <a name="creating-a-new-theme"></a>Создание новой темы

В следующем примере мы будем использовать палитру материал для создания новой пользовательской темы. Во-первых, мы изменим **фона** цвет *синий 900*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Изменение фона до 900 синий](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Изменение фона до 900 синий](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

-----


При изменении ресурс цвета, сообщение появится окно с сообщением, *текущей темы содержит несохраненные изменения*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Предупреждение несохраненные изменения](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Предупреждение несохраненные изменения](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

-----


**Фона** на новый цвет изменился цвет в конструкторе, но это изменение еще не были сохранены. Инструкции по обработке изменения предлагаются два варианта:

-   **Отменить изменения** &ndash; отбрасывает новый выбранный цвет (или параметров), и возвращается в исходное состояние темы.

-   **Создать новую тему** &ndash; создает новую тему, который является производным от выбранной темы и использует цвет переопределения, внесенные в **редактор**.

При нажатии кнопки **создать новую тему**, происходит одно из следующих действий, в зависимости от выбранной темы:

-   Если темы, выбранных в конструкторе не темы проект, предоставляется параметр, чтобы создать новый файл ресурсов с пользовательской темой, (с использованием выбранной темой в качестве родительской настраиваемые темы). Конструктор переключается в пользовательской темой, после его создания.

-   Если в настоящее время выбранную тему темы проекта, определенного в одном месте, появится **обновления темы** параметр; Установка этого флажка обновляет выбранного темы, а не создавать новый.

-   Выбранного темы в случае темы проекта, который определен в нескольких местах (например, в по-разному-добавляется суффикс **значения** папок, таких как **v21 значения**), появится диалоговое окно с вопросом новое расположение для сохранения пользовательской темой.

Продолжение предыдущего примера, щелкнув **создать новую тему** вызывается приводит к созданию новой темы **настраиваемый**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Добавить пользовательские темы](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Добавить пользовательские темы](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png#lightbox)

-----


Поскольку выбранной темы не темы проекта, нет ни одно диалоговое окно для обновления с выбранной темой или укажите новое расположение.


## <a name="summary"></a>Сводка

В этом разделе описываются функции материал разработки, доступных в конструкторе Xamarin.Android. Оно описано, как включить и настроить бланке материал, как использовать материал разработки цветовую палитру для редактирования свойств цвета и использование селектор типографском шкалы для настройки свойства текстового. Он также демонстрируется использование редактора темы для создания нового пользовательские темы, которые соответствуют правилам разработки материал. Дополнительные сведения о поддержке Xamarin.Android материал конструктора. в разделе [темы материал](~/android/user-interface/material-theme.md).



## <a name="related-links"></a>Связанные ссылки

- [Тема материала](~/android/user-interface/material-theme.md)
- [Введение материала разработки](https://www.google.com/design/spec/material-design/introduction.html)
