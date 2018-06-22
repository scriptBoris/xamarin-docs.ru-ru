---
title: Макет альтернативные представления
description: В этом разделе объясняется, как макеты могут поддерживать управление версиями с помощью квалификаторов ресурса. Например может существовать версии макет, который используется только в том случае, когда устройство находится в режиме альбомной ориентации и макет версию, которая предназначена только для книжной ориентации.
ms.prod: xamarin
ms.assetid: 5EBF51FC-9048-F0CF-624A-D8782A91C1FD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: d2228169ed5d8575c9e332c85d963fca0400dea8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771165"
---
# <a name="alternative-layout-views"></a>Макет альтернативные представления

_В этом разделе объясняется, как макеты могут поддерживать управление версиями с помощью квалификаторов ресурса. Например может существовать версии макет, который используется только в том случае, когда устройство находится в режиме альбомной ориентации и макет версию, которая предназначена только для книжной ориентации._


## <a name="creating-alternative-layouts"></a>Создание альтернативные структуры

При нажатии кнопки **альтернативное представление макета** значок (слева от **устройства**), в области предварительного просмотра откроется список альтернативные структуры, доступные в проекте. Если нет альтернативные структуры **по умолчанию** выводится представление: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Панель представления альтернативный формат](alternative-layout-views-images/vs/01-alt-layout-view-pane-sml.png "альтернативный макета панели «представление»")](alternative-layout-views-images/vs/01-alt-layout-view-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Панель представления альтернативный формат](alternative-layout-views-images/xs/01-alt-layout-view-pane-sml.png)](alternative-layout-views-images/xs/01-alt-layout-view-pane.png#lightbox)

-----

При нажатии кнопки "плюс", рядом с **новую версию**, **вариант создания макета** откроется диалоговое окно, в котором можно выбрать квалификаторами ресурсов для этих вариантов макета: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Создание макета варианта](alternative-layout-views-images/vs/02-create-layout-variation-sml.png "создать макет вариант")](alternative-layout-views-images/vs/02-create-layout-variation.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Создание макета варианта](alternative-layout-views-images/xs/02-create-layout-variation-sml.png)](alternative-layout-views-images/xs/02-create-layout-variation.png#lightbox)

-----


В следующем примере описатель ресурсов для **ориентации экрана** равно **Альбомная**и **размер экрана** изменяется на **большой**. Это создаст новый макет с именем **больших Земли**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Крупный Земли вариантов](alternative-layout-views-images/vs/03-large-land-sml.png "Земли крупный вариант")](alternative-layout-views-images/vs/03-large-land.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Крупный Земли вариантов](alternative-layout-views-images/xs/03-large-land-sml.png)](alternative-layout-views-images/xs/03-large-land.png#lightbox)

-----


Обратите внимание, что в левую панель предварительного просмотра отображает результат применения выбранных элементов для квалификатора ресурсов. Щелкнув **добавить** создает альтернативных макета и переключается в этот макет конструктора. **Альтернативный режим** область просмотра указывает, какой макет загружается в конструкторе через небольшой правым указателем, как показано на следующем снимке экрана: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Загрузить макет индикатора](alternative-layout-views-images/vs/04-new-layout-sml.png "загрузить макет индикатора")](alternative-layout-views-images/vs/04-new-layout.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Загрузить макет индикатора](alternative-layout-views-images/xs/04-new-layout-sml.png)](alternative-layout-views-images/xs/04-new-layout.png#lightbox)

-----



## <a name="editing-alternative-layouts"></a>Редактирование альтернативные структуры

При создании альтернативные структуры часто желательно внести одно изменение, которое применяется ко всем версиям разветвленного макета. Например можно изменить этот текст кнопки на желтым цветом все макеты. Если имеется большое количество макетов обслуживания может быстро стать громоздким и ошибкам, если вам необходимо распространить одно изменение ко всем версиям. 

Чтобы упростить обслуживание нескольких версий макета, конструктор предоставляет **несколькими редактирования** режиме, распространяющую изменения на один или несколько макетов. Если присутствует несколько макетов **несколькими редактирования** отображается значок: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Значок редактирования несколькими](alternative-layout-views-images/vs/05-multi-layout-icon-sml.png "несколькими Правка")](alternative-layout-views-images/vs/05-multi-layout-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Несколькими Правка](alternative-layout-views-images/xs/05-multi-layout-icon-sml.png)](alternative-layout-views-images/xs/05-multi-layout-icon.png#lightbox)

-----


При нажатии кнопки **несколькими редактирования** значок, появляются линии, которые указывают на то, что макетами связаны (как показано ниже); то есть, при внесении изменений в один макет, изменение распространяется на любой связанный макеты. Можно отсоединить все макеты, щелкнув значок кружке показано на следующем снимке экрана: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Отсоединить все макеты](alternative-layout-views-images/vs/06-multi-linked-sml.png "отсоединить все макеты")](alternative-layout-views-images/vs/06-multi-linked.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Отменить привязку всех макетов](alternative-layout-views-images/xs/06a-linked-sml.png)](alternative-layout-views-images/xs/06a-linked.png#lightbox)

-----


При наличии более двух макеты можно выборочно переключать «изменить» слева от каждого образец, чтобы определить, какие макеты связаны друг с другом. Например, если требуется внести одно изменение, которое распространяется на первый и последний из трех макетов бы сначала разрыва связи средней макета как показано ниже: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Удаление связи средней макета](alternative-layout-views-images/vs/07-unlink-middle-layout-sml.png "отменить связь средней макета")](alternative-layout-views-images/vs/07-unlink-middle-layout.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Удалить связь средней макета](alternative-layout-views-images/xs/06b-multi-linked-sml.png)](alternative-layout-views-images/xs/06b-multi-linked.png#lightbox)
 
-----
 

В этом примере внесены изменения, либо **по умолчанию** или **длинные** макет будет распространяться другой макет, но не к **больших Земли** макета. 



### <a name="multi-edit-example"></a>Пример нескольких редактирования 

Как правило при внесении изменений в один макет этой же изменение распространяется на все остальные связанные макеты. Например, при добавлении нового `TextView` мини-приложение для **по умолчанию** макет и изменение его текста строки `Portrait` приведет к тому же изменение быть выполнены для всех связанных макеты. Вот как выглядит **по умолчанию** макета: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Добавить TextView](alternative-layout-views-images/vs/08-add-textview-sml.png "добавьте TextView")](alternative-layout-views-images/vs/08-add-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Добавить TextView](alternative-layout-views-images/xs/07-add-textview-sml.png)](alternative-layout-views-images/xs/07-add-textview.png#lightbox)
 
-----
 

`TextView` Также добавляется к **больших Земли** макета представления, так как она связана с **по умолчанию** макета: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Альбомная TextView](alternative-layout-views-images/vs/09-landscape-textview-sml.png "альбомную TextView")](alternative-layout-views-images/vs/09-landscape-textview.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Альбомная TextView](alternative-layout-views-images/xs/08-landscape-textview-sml.png)](alternative-layout-views-images/xs/08-landscape-textview.png#lightbox)
 
-----
 

Но что делать, если требуется внести изменение, который является локальным для только один макет (то есть, вы не хотите изменение распространяется на любые другие макетов)? Чтобы сделать это, необходимо удалить связь макет, который требуется изменить, прежде чем изменить его, как описано далее. 



### <a name="making-local-changes"></a>Внесение локальных изменений 

Предположим, что мы хотим, чтобы оба макеты провести дополнительную `TextView`, но нам также нужно изменить строку текста в **больших Земли** макета для `Landscape` вместо `Portrait`. Если мы внести это изменение **больших Земли** хотя оба вида разметки связаны, изменения будут применены к **по умолчанию** макета. Таким образом мы должны сначала разорвать связь двух макеты перед внесением изменений. Когда мы изменить текст в **больших Земли** для `Landscape`, конструктор помечает это изменение Красная рамка для указания, что данное изменение является локальным для **больших Земли** макета и *не* распространяются обратно **по умолчанию** макета: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Локальное изменение](alternative-layout-views-images/vs/10-local-change-sml.png "локальных изменений")](alternative-layout-views-images/vs/10-local-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Локальное изменение](alternative-layout-views-images/xs/09-local-change-sml.png)](alternative-layout-views-images/xs/09-local-change.png#lightbox)
 
-----
 

При нажатии кнопки **по умолчанию** макет для отображения этого `TextView` текстовой строки по-прежнему равно `Portrait`. 



## <a name="handling-conflicts"></a>Обработка конфликтов 

Если вы решите изменить цвет текста в **по умолчанию** макета зеленый, вы увидите значок предупреждения отображаются связанные макета. Этот макет открывают макет для отображения полей в конфликте. Мини-приложение, вызвавшее конфликт будет выделен красным рамка и отображается следующее сообщение: *вызвано недавними изменениями конфликтует с данным макетом альтернативных*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Конфликтующих изменений](alternative-layout-views-images/vs/11-conflicting-change-sml.png "конфликтующих изменений")](alternative-layout-views-images/vs/11-conflicting-change.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Конфликтующее изменение](alternative-layout-views-images/xs/10-conflict-sml.png)](alternative-layout-views-images/xs/10-conflict.png#lightbox)
 
-----
 

Объект *конфликт ''* отображается в правой части мини-приложение для объяснения конфликт: 

[![Предупреждение о конфликте](alternative-layout-views-images/xs/11-warning-sml.png)](alternative-layout-views-images/xs/11-warning.png#lightbox)

Окно конфликтов отображает список свойств, которые были изменены, и перечисляются их значения. Щелкнув **игнорировать конфликт** применяет изменения свойства только для этого мини-приложения. Щелкнув **применить** применяется изменение свойства для этого мини-приложения также относительно аналог мини-приложения в связанных **по умолчанию** макета. Если все изменения свойств применяются, конфликт автоматически удаляются. 


### <a name="view-group-conflicts"></a>Представление группы конфликтов 

Изменения свойств не единственным источником конфликтов. Конфликты можно обнаружить, когда Вставка или удаление мини-приложения. Например, если **больших Земли** макета является отсоединяется от **по умолчанию** макет и `TextView` в **больших Земли** макета является перетаскивать выше `Button`, конструктор помечает перемещенный мини-приложения для указания конфликт:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Просмотр конфликтов группы](alternative-layout-views-images/vs/12-view-group-conflict-sml.png "просмотра конфликтов группы")](alternative-layout-views-images/vs/12-view-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Представление группы конфликтов](alternative-layout-views-images/xs/12-view-group-conflict-sml.png)](alternative-layout-views-images/xs/12-view-group-conflict.png#lightbox)
 
-----
 

Однако есть маркер не на `Button`. Несмотря на то что положение `Button` изменилось, `Button` отображаются не примененные изменения, которые относятся к **больших Земли** конфигурации. 

Если `CheckBox` добавляется **по умолчанию** макета, создается другой конфликтов и значок предупреждения, отображаемого над **больших Земли** макета: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Конфликт флажок](alternative-layout-views-images/vs/13-checkbox-conflict-sml.png "флажок конфликтов")](alternative-layout-views-images/vs/13-checkbox-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Конфликт флажок](alternative-layout-views-images/xs/13-checkbox-conflict-sml.png)](alternative-layout-views-images/xs/13-checkbox-conflict.png#lightbox)
 
-----
 

Щелкнув **больших Земли** макета показывает конфликт. Отображается следующее сообщение: *вызвано недавними изменениями конфликтует с данным макетом альтернативных*. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Конфликт ALT макета](alternative-layout-views-images/vs/14-alt-layout-conflict-sml.png "конфликт Alt макета")](alternative-layout-views-images/vs/14-alt-layout-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Конфликт ALT макета](alternative-layout-views-images/xs/14-alt-layout-conflict-sml.png)](alternative-layout-views-images/xs/14-alt-layout-conflict.png#lightbox)
 
-----
 

Кроме того в поле конфликт отображается следующее сообщение:

[![Сообщение о конфликте](alternative-layout-views-images/xs/15-conflict-message-sml.png)](alternative-layout-views-images/xs/15-conflict-message.png#lightbox)

Добавление `CheckBox` приводит к конфликту, так как **больших Земли** макета в изменилось `LinearLayout` , которая его содержит. Однако в этом случае поле конфликтов отображает мини-приложение, только что вставленного в **по умолчанию** макета ( `CheckBox`).

Если нажать кнопку **игнорировать конфликт**, конструктор устраняет конфликт, позволяя мини-приложения отображается в поле конфликт Чтобы перетащить в макет, в которой отсутствует мини-приложения (в этом случае **больших Земли**  макета): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Разрешенный конфликт группы](alternative-layout-views-images/vs/15-resolved-group-conflict-sml.png "разрешен конфликт группы")](alternative-layout-views-images/vs/15-resolved-group-conflict.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Разрешенный конфликт группы](alternative-layout-views-images/xs/16-resolved-group-conflict-sml.png)](alternative-layout-views-images/xs/16-resolved-group-conflict.png#lightbox)
 
-----
 

Как показано в предыдущем примере с `Button`, `CheckBox` не имеет маркер красный изменений, поскольку только `LinearLayout` содержит изменения, которые были применены в **больших Земли** макета.



### <a name="conflict-persistence"></a>Конфликт сохраняемости

Конфликты сохраняются в файле разметки как комментарии XML, как показано ниже:

```xml
<!-- Widget Inserted Conflict | id:__root__ | @+id/checkBox1 -->
```

Таким образом, когда закрывается и снова открыть проект, все конфликты будут по-прежнему присутствовать &ndash; даже те, которые были пропущены.

