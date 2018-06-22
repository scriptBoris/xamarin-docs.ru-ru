---
title: Основные сведения о конструкторе iOS
description: В этом руководстве описаны в конструкторе Xamarin iOS. Он демонстрирует, как использовать конструктор iOS для размещения элементов управления визуально, как получить доступ к этих элементов управления в коде и изменение свойств.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: 7e36a402619813214e821f3060e053d76c99cfb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30784262"
---
# <a name="ios-designer-basics"></a>Основные сведения о конструкторе iOS

_В этом руководстве описаны в конструкторе Xamarin iOS. Он демонстрирует, как использовать конструктор iOS для размещения элементов управления визуально, как получить доступ к этих элементов управления в коде и изменение свойств._

В конструкторе Xamarin iOS — аналогично Xcode интерфейс построителя визуальный интерфейс конструктор и конструктор Android. Некоторые из его многие возможности включают интеграция с Visual Studio для Mac и Visual Studio 2015 и 2017 г., редактирования и перетащите, интерфейс для настройки обработчики событий и возможность отображения пользовательских элементов управления.

## <a name="requirements"></a>Требования

IOS предоставляется доступ к конструктору в Visual Studio для Mac и в Visual Studio 2015 и 2017 г. в Windows. В Visual Studio 2015 или 2017 г. конструктор iOS требуется подключение к правильно настроенный Mac узел сборки, на то, что не придется запускать Xcode.

В этом руководстве предполагается Знакомство с содержимым, охваченных [Приступая к работе проводит](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Как работает конструктор iOS

В этом разделе описывается, как конструктор iOS упрощает создание пользовательского интерфейса и привязать его к коду.

Конструктор iOS позволяет разработчикам визуальное проектирование пользовательского интерфейса приложения. Как описано в [введение в раскадровки](~/ios/user-interface/storyboards/index.md) , раскадровки описываются экраны (Просмотр контроллеров), входящие в состав приложения, элементы интерфейса (представления), размещенные на их просмотр контроллеров и общий поток навигации приложения . 

Контроллер представление состоит из двух частей: визуальное представление в конструкторе iOS и связанный класс C#:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Представление-контроллер в конструкторе iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "представление-контроллер в конструкторе iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Код для контроллера представления](introduction-images/2-viewcontrollercode-vsmac.png "кода для контроллера представления")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Представление-контроллер в конструкторе iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "представление-контроллер в конструкторе iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Код для контроллера представления](introduction-images/2-viewcontrollercode-vs.png "кода для контроллера представления")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

В состоянии по умолчанию представление контроллер не предоставляет функциональные возможности; должно быть заполнено с элементами управления. Эти элементы управления располагаются в представлении представление контроллер прямоугольной области, которая содержит все содержимое экрана. Большинство контроллеров представления содержат общие элементы управления, такие как кнопки, метки и текстовые поля, как показано на следующем снимке экрана, показывающий представление-контроллер, содержащую кнопку: 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Представление-контроллер, содержащую кнопку](introduction-images/3-viewcontrollerwithbutton-vsmac.png "представление-контроллер, содержащую кнопку")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Представление-контроллер, содержащую кнопку](introduction-images/3-viewcontrollerwithbutton-vs.png "представление-контроллер, содержащую кнопку")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Некоторые элементы управления, например, метки, содержащие статический текст, могут быть добавлены к представлению контроллеру и оставить отдельно. Однако чаще всего, элементы управления должны иметь непосредственное отношение программными средствами. Например кнопки, добавленных выше выполнения определенного действия при касании, поэтому необходимо добавить обработчик событий в коде.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Для получения доступа и управления кнопки в коде, он должен иметь уникальный идентификатор. Укажите уникальный идентификатор, нажав кнопку, открыв **панель свойств**и параметр его **имя** поле значение, например кнопка '' «Отправить»:

[![Задание имени кнопки в панели свойств](introduction-images/4-settingbuttonname-vsmac.png "кнопки имя параметра в области свойств")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Для получения доступа и управления кнопки в коде, он должен иметь уникальный идентификатор. Укажите уникальный идентификатор, нажав кнопку, открыв **окно свойств**и параметр его **имя** поле значение, например кнопка '' «Отправить»:

[![Задание имени кнопки в окне «Свойства»](introduction-images/4-settingbuttonname-vs.png "кнопки имя параметра в окне «Свойства»")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Теперь, когда кнопка имеет имя, оно может осуществляться в коде. Но, как это работает?

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

В **Pad решения**, перехода по страницам **ViewController.cs** и щелчке индикатора раскрытие показывают, что представление контроллер `ViewController` диапазонов определение класса двух файлов, каждый из которых содержит [разделяемый класс](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) определение:

[![Два файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

В **обозревателе решений**, перехода по страницам **ViewController.cs** и щелчке индикатора раскрытие показывают, что представление контроллер `ViewController` определение класса охватывает двух файлов, каждый из содержащее [разделяемый класс](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) определение:

[![Два файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** должен быть заполнен пользовательского кода, связанные с `ViewController` класса. В этом файле `ViewController` класс может отвечать на iOS различных методов жизненного цикла контроллера представления, настройки пользовательского интерфейса и реагирования на пользовательского ввода, такие как нажимает кнопку.

- **ViewController.designer.cs** это сформированный файл, созданный конструктор для сопоставления визуально сконструированный интерфейс с кодом iOS. Так как изменения в этот файл будет перезаписан, его не следует изменять. Объявления свойств в этом файле сделать код в `ViewController` класса к доступу с **имя**, определяет набор вверх в конструкторе iOS. Открытие **ViewController.designer.cs** показывает следующий код:

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

`SubmitButton` Объявление свойства подключается всего `ViewController` класса - не просто **ViewController.designer.cs** файла — к кнопке, определенные в раскадровку. Поскольку **ViewController.cs** определяет часть `ViewController` класс, имеет доступ к `SubmitButton`.

Следующий снимок экрана показано, что IntelliSense теперь распознается `SubmitButton` ссылки в **ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Технология IntelliSense, распознавая кнопка '' Отправить ссылку](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense, распознавая кнопка '' Отправить ссылку")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Технология IntelliSense, распознавая кнопка '' Отправить ссылку](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense, распознавая кнопка '' Отправить ссылку")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

В этом разделе показана как создать кнопку в конструктор iOS и получить доступ к этой кнопки в коде.

Оставшаяся часть этого документа предоставляет дополнительные общие сведения о конструкторе iOS.

## <a name="ios-designer-basics"></a>Основные сведения о конструкторе iOS

В этом разделе представлены части конструктора iOS и предоставляет обзор его возможностей.

### <a name="launching-the-ios-designer"></a>Запуск конструктора iOS

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Xamarin.iOS проекты, созданные с помощью Visual Studio для Mac содержат раскадровки. Чтобы просмотреть содержимое раскадровки, дважды щелкните файл .storyboard в **Pad решение**:

[![Раскадровка открыть в конструкторе iOS](introduction-images/7-storyboardopen-vsmac.png "раскадровки открыть в конструкторе iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Большинство Xamarin.iOS проектов, созданных с помощью Visual Studio 2015 или 2017 г. включает раскадровки. Чтобы просмотреть содержимое раскадровки, дважды щелкните файл .storyboard в **обозревателе решений**:

[![Раскадровка открыть в конструкторе iOS](introduction-images/7-storyboardopen-vs.png "раскадровки открыть в конструкторе iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>iOS возможности конструктора

IOS конструктор имеет шесть основных раздела:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Разделы iOS конструктор](introduction-images/8-sixpartsofiosdesigner-vsmac.png "разделы iOS конструктора")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Область конструктора** — основной рабочей областью конструктора операций ввода-вывода. Показано, в область документа, она позволяет визуального создания пользовательских интерфейсов.
2. **Ограничения инструментов** — позволяет для переключения между фрейма редактирования и ограничение режим редактирования, двумя различными способами для размещения элементов в пользовательском интерфейсе.
3. **Панель элементов** — список контроллеров, объекты, элементы управления, представления данных, распознавателей жестов, windows, а также панелей, можно перетащить в область конструктора и добавить пользовательский интерфейс.
4. **Панель свойств** — отображает свойства выбранного элемента управления, включая удостоверение, визуальные стили, специальные возможности, макет и поведение.
5. **Структура документа** — показано дерево элементов, составляющих макета для редактируемого интерфейса. При щелчке элемента в дереве выбирает его в конструкторе iOS и показывает его свойства в **панель свойств**. Это удобно для выбора конкретного элемента управления в интерфейсе пользователя глубокий уровень вложенности.
6. **Нижняя панель инструментов** — содержит параметры для изменения отображения iOS конструктор .storyboard или .xib файла, включая устройства, ориентацию и масштаб.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Разделы iOS конструктор](introduction-images/8-sixpartsofiosdesigner-vs.png "разделы iOS конструктора")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Область конструктора** — основной рабочей областью конструктора операций ввода-вывода. Показано, в область документа, она позволяет визуального создания пользовательских интерфейсов.
2. **Ограничения инструментов** — позволяет для переключения между фрейма редактирования и ограничение режим редактирования, двумя различными способами для размещения элементов в пользовательском интерфейсе.
3. **Панель элементов** — список контроллеров, объекты, элементы управления, представления данных, распознавателей жестов, windows, а также панелей, можно перетащить в область конструктора и добавить пользовательский интерфейс.
4. **Окно "Свойства"** — отображает свойства выбранного элемента управления, включая удостоверение, визуальные стили, специальные возможности, макет и поведение.
5. **Структура документа** — показано дерево элементов, составляющих макета для редактируемого интерфейса. При щелчке элемента в дереве выбирает его в конструкторе iOS и показывает его свойства в **окно свойств**. Это удобно для выбора конкретного элемента управления в интерфейсе пользователя глубокий уровень вложенности.
6. **Нижняя панель инструментов** — содержит параметры для изменения отображения iOS конструктор .storyboard или .xib файла, включая устройства, ориентацию и масштаб.

-----

### <a name="design-workflow"></a>Создайте рабочий процесс

#### <a name="adding-a-control-to-the-interface"></a>Добавление элемента управления на интерфейс

Чтобы добавить элемент управления к интерфейсу, перетащите его из **элементов** и поместите его в рабочей области конструирования. При добавлении или элементов управления, рекомендации по вертикали и горизонтали выделяет позиции часто используемых макета, например вертикальной center, в середине и поля.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)
 
![В рабочей области конструирования рекомендации выделите позиции макета часто используемых](introduction-images/9-layoutguides-vsmac.png "в рабочей области конструирования рекомендации выделите позиции часто используемых макета")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![В рабочей области конструирования рекомендации выделите позиции макета часто используемых](introduction-images/9-layoutguides-vs.png "в рабочей области конструирования рекомендации выделите позиции часто используемых макета")

-----

Синяя пунктирная линия в приведенном выше примере рекомендации, в середине выравнивание визуального помощи размещение кнопки.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>Команды контекстного меню

Контекстное меню доступен в рабочей области конструирования, а также в **Структура документа**. Это меню содержит команды для выделенного элемента управления и его родителем, это удобно при работе с представлениями во вложенной иерархии:

[![В контекстном меню в области конструктора](introduction-images/10-contextmenudesignsurface-vsmac.png "контекстное меню в области конструктора")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>Ограничения инструментов

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)
 
[![Ограничения инструментов](introduction-images/11-constraintstoolbar-vsmac.png "ограничения инструментов")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ограничения инструментов](introduction-images/11-constraintstoolbar-vs.png "ограничения инструментов")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

Ограничения инструментов уже был обновлен и теперь состоит из двух элементов управления: кадра, в режиме редактирования или ограничение редактирование Переключение режима и ограничения на обновление или обновление кнопка рамки.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Кадр, в режиме редактирования или переключение режима редактирования ограничения

В предыдущих версиях конструктора iOS выбрав в меню Вид уже выбран в области конструктора два значения фрейма редактирования и режим редактирования ограничение. Теперь элемента управления "выключатель" на панели инструментов ограничения для переключения между эти режимы редактирования.

- Режим редактирования кадра:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Фрейм редактирования кнопка режима](introduction-images/12a-frameeditingmode-vsmac.png "фрейма редактирования кнопка режима")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Фрейм редактирования кнопка режима](introduction-images/12a-frameeditingmode-vs.png "фрейма редактирования кнопка режима")

-----

- Режим редактирования ограничение:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![Кнопка режима редактирования ограничение](introduction-images/12b-constrainteditingmode-vsmac.png "кнопка режима редактирования ограничения")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Кнопка режима редактирования ограничение](introduction-images/12b-constrainteditingmode-vs.png "кнопка режима редактирования ограничения")

-----

#### <a name="update-constraints--update-frames-button"></a>Ограничения на обновление или обновление кнопка рамки

Ограничения на обновление и обновление кадры кнопка находится справа от кадра, в режиме редактирования / Переключение режима редактирования ограничение.

- В окне режима редактирования после нажатия этой кнопки корректирует кадров все выбранные элементы в соответствии с их ограничений.
- В режиме редактирования ограничение при нажатии этой кнопки корректирует ограничения все выбранные элементы в соответствии с их кадры.

### <a name="bottom-toolbar"></a>Нижняя панель инструментов

В нижней панели инструментов предоставляет способ для выбора устройства, ориентацию и масштаб, можно просматривать файл раскадровки или .xib в конструкторе iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![В нижней панели инструментов, используемый для выбора устройства и ориентации для рабочей области конструктора](introduction-images/13-bottomtoolbar-vsmac.png "в нижней панели инструментов, используемый для выбора устройства и ориентации для рабочей области конструктора")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![В нижней панели инструментов, используемый для выбора устройства и ориентации для рабочей области конструктора](introduction-images/13-bottomtoolbar-vs.png "в нижней панели инструментов, используемый для выбора устройства и ориентации для рабочей области конструктора")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Устройства и ориентация

При развертывании в нижней панели инструментов отображаются все устройства, ориентацию и/или процесса, применимые к текущему документу. Чтобы изменить представление, отображаемое в области конструктора, щелкните их. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![В нижней панели инструментов разворачивать для просмотра устройств и ориентации](introduction-images/14-bottomtoolbarexpanded-vsmac.png "в нижней панели инструментов, разворачивать для просмотра устройств и ориентации")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![В нижней панели инструментов разворачивать для просмотра устройств и ориентации](introduction-images/14-bottomtoolbarexpanded-vs.png "в нижней панели инструментов, разворачивать для просмотра устройств и ориентации")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Обратите внимание, что при выборе устройства и ориентацию меняется только как iOS конструктор выполняет предварительный просмотр конструктора. Независимо от текущего выделения добавления ограничения применяются для всех устройств и ориентацию, если не **изменить признаки** кнопку использовался для указания, в противном случае.

При [размер классы](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) , [включен](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), **изменить признаки** кнопка будет отображаться в расширенном нижней панели инструментов.  Щелкнув **изменить признаки** кнопки отображаются параметры для создания вариантов интерфейс на основе класса размер представленных выбранного устройства и ориентацию. Рассмотрим следующие примеры:

- Если **iPhone SE** / **Книжная**, будет выбран, popover предложит варианты по ее создать интерфейс вариант compact ширину, высоту регулярного размер класса. 
- Если **iPad Pro 9,7"** / **Альбомная** / **весь экран** — флажок установлен, popover предложит варианты по ее создать интерфейс вариант для регулярные ширины, регулярного высота размер класса.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![В нижней панели инструментов используется для изменения интерфейса класса размер](introduction-images/15-edittraitsbutton-vsmac.png "используется для изменения интерфейса класса размер нижней панели инструментов")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![В нижней панели инструментов используется для изменения интерфейса класса размер](introduction-images/15-edittraitsbutton-vs.png "используется для изменения интерфейса класса размер нижней панели инструментов")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Элементы управления масштабом

Область конструктора поддерживает масштабирование через несколько элементов управления.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)
 
![Элементы управления масштабом в нижней панели инструментов](introduction-images/16-zoomcontrols-vsmac.png "элементы управления масштабом в нижней панели инструментов")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Элементы управления масштабом в нижней панели инструментов](introduction-images/16-zoomcontrols-vs.png "элементы управления масштабом в нижней панели инструментов")

-----

Следующие элементы управления.

1. Вписать
2. Мельче
3. Увеличить
4. Фактический размер (размер в пикселях 1:1)

Эти элементы управления увеличьте масштаб в рабочей области конструирования. Они не влияют на пользовательский интерфейс приложения во время выполнения.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

### <a name="properties-pad"></a>Панель свойств

Используйте **Pad свойства** для изменения удостоверения, визуальные стили, специальные возможности и поведение элемента управления. Показано на следующем снимке экрана **свойства Разредить** параметры для кнопки:

[![Панель свойств для кнопки](introduction-images/17-buttonpropertiespad-vsmac.png "панель свойств для кнопки")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Разделы панель свойств

**Панель свойств** содержит три раздела:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>Окно свойств

Используйте **окно свойств** изменение удостоверения, визуальные стили, специальные возможности и поведение элемента управления. Показано на следующем снимке экрана **окно свойств** параметры для кнопки:

[![Окно свойств для кнопки](introduction-images/17-buttonpropertieswindow-vs.png "окно свойств для кнопки")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Разделы окна свойств

**Окно свойств** содержит три раздела:

-----

1.  **Мини-приложение** — основные свойства элемента управления, таких как имя класса, свойства стиля, и т. д. Свойства для управления содержимым элемента управления обычно размещаются здесь.
2.  **Макет** — здесь перечислены свойства, которые следят за положение и размер элемента управления, включая ограничения и кадров.
3.  **События** — здесь указанного события и обработчики событий. Это полезно для обработки событий, такие как сенсорный ввод, касания, перетаскивания, и т. д. Кроме того, события могут обрабатываться непосредственно в коде.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>Изменение свойств в панели свойств

В дополнение к визуального редактирования в области конструктора, конструктор iOS поддерживает редактирование свойств в **панель свойств**. Доступные свойства изменяется в зависимости от выбранного элемента управления, как показано на снимке экрана ниже:

[![Свойства кнопок](introduction-images/18a-buttonpropertiespad-vsmac.png "кнопку Свойства")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Просмотр свойств контроллера](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "просмотреть свойства контроллера")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>Изменение свойств в окне «Свойства»

В дополнение к визуального редактирования в области конструктора, конструктор iOS поддерживает редактирование свойств в **окно свойств**. Доступные свойства изменяется в зависимости от выбранного элемента управления, как показано на снимке экрана ниже:

[![Свойства кнопок](introduction-images/18a-buttonpropertieswindow-vs.png "кнопку Свойства")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Просмотр свойств контроллера](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "просмотреть свойства контроллера")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> В разделе удостоверений, теперь отображается панель свойств **модуль** поля. Это необходимо для заполнения в этом разделе только в том случае, при взаимодействии с Swift классы. Используйте его для ввода имени модуля для ускорения классы, являющиеся с пространством имен.

#### <a name="default-values"></a>Значения по умолчанию

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Многие свойства в **панель свойств** Показать никакого значения или значения по умолчанию. Однако код приложения по-прежнему могут изменять эти значения. **Панель свойств** не содержит значения, заданные в коде.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Многие свойства в **окно свойств** Показать никакого значения или значения по умолчанию. Однако код приложения по-прежнему могут изменять эти значения. **Окно свойств** не содержит значения, заданные в коде.

-----

#### <a name="event-handlers"></a>Обработчики событий

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Чтобы указать пользовательские обработчики событий для различных событий, используйте **событий** вкладке **панель свойств**. Например, на снимке экрана ниже `HandleClick` метод обрабатывает кнопки **Touch вверх внутри** событий:

[![Панель свойств с обработчиком событий для кнопки](introduction-images/19-buttonpropertiespadevents-vsmac.png "панель свойств, с обработчиком событий для кнопки")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Чтобы указать пользовательские обработчики событий для различных событий, используйте **событий** вкладке **окно свойств**. Например, на снимке экрана ниже `HandleClick` метод обрабатывает кнопки **Touch вверх внутри** событий:

[![В окне «Свойства» с обработчиком событий для кнопки](introduction-images/19-buttonpropertieswindowevents-vs.png "окно свойств, с обработчиком событий для кнопки")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

После обработчика событий, необходимо добавить метод с тем же именем в соответствующий класс контроллера представления. В противном случае `unrecognized selector` исключение происходит при касании кнопки:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Исключение нераспознанный селектор](introduction-images/20-unrecognizedselector-vsmac.png "исключение нераспознанный селектора")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Обратите внимание, что после обработчик события был указан в **панель свойств**, iOS, конструктор немедленно откроет соответствующий файл кода и обеспечивают для вставки объявления метода. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Исключение нераспознанный селектор](introduction-images/20-unrecognizedselector-vs.png "исключение нераспознанный селектора")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Пример, использующий пользовательские обработчики событий, см. в разделе [Привет, iOS руководство по началу работы](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Режим структуры

IOS конструктор также можно отобразить структуры иерархии интерфейсов для элементов управления. Контур доступен, выбрав **Структура документа** вкладки, как показано ниже:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Структура документа](introduction-images/21-buttonoutlineview-vsmac.png "Структура документа")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Структура документа](introduction-images/21-buttonoutlineview-vs.png "Структура документа")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Выбранный элемент управления в представлении структуры синхронизируются с выбранного элемента управления в рабочей области конструирования.  Эта возможность полезна для выбора элемента в иерархии интерфейса большим уровнем вложенности.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>Вернуться к Xcode

Можно использовать на равных основаниях iOS конструктора и Xcode интерфейс построителя. Чтобы открыть раскадровку или файл .xib в построителе интерфейс Xcode, щелкните правой кнопкой мыши файл и выберите пункт **открыть с помощью > Xcode интерфейс построителя**, как показано на снимке экрана ниже:

[![Открытие раскадровки в построителе интерфейс Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Открытие раскадровки в построителе интерфейс Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

После внесения изменений в построителе интерфейс Xcode, сохраните файл и вернуться в Visual Studio для Mac. Изменения будут синхронизироваться с проектом Xamarin.iOS.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>Поддержка .xib

Конструктор iOS поддерживает создание, изменение и управление файлами .xib. Они являются XML-файлами, представлять представления одной, пользовательские, которые могут добавляться в иерархии для представления приложения. Файл .xib обычно представляет интерфейс для представления одного или экрана в приложении, а раскадровка представляет много экранов и переходы между ними.

Существует много мнение о том, какие решения — .xib файлы, раскадровки или код — лучше всего подходит для создания и обслуживания пользовательского интерфейса. На самом деле что это не идеальное решение и было всегда стоит рассмотреть наиболее подходящим средством для работы под рукой. С другой стороны, .xib файлы, обычно самых мощных, при использовании для обозначения пользовательские представления, необходимые в нескольких местах в приложении, например ячейки представления пользовательских таблиц. 

Дополнительную документацию об использовании .xib файлов можно найти в следующем рецепты:

- [С помощью шаблона .xib представление](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [Создание настраиваемых TableViewCell, с помощью .xib](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [Создание экрана запуска с помощью .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

Дополнительные сведения по использованию раскадровки посвящены [введение в раскадровки](~/ios/user-interface/storyboards/index.md).

Это и другие связанные с конструктором направляющие iOS относятся использования раскадровок как стандартный подход для создания пользовательских интерфейсов, поскольку новые шаблоны проектов большинство Xamarin.iOS обеспечивают раскадровки по умолчанию.

## <a name="summary"></a>Сводка

В этом руководстве предоставляются общие сведения о конструкторе описания его возможности и средства, предоставляемые по проектированию красивыми пользовательскими интерфейсами создания структуры iOS.

## <a name="related-links"></a>Связанные ссылки

- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
- [Проектирование пошагового руководства элементы управления iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Привет, iOS](~/ios/get-started/hello-ios/index.md)
- [Привет, iOS (несколько экранов)](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Общие сведения о конструкторе Android](~/android/user-interface/android-designer/index.md)
- [Разделяемые классы и методы](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Углубляться в конструкторе Xamarin для iOS — развивать 2014 (видео)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [С помощью iOS конструктор для создания экрана запуска (видео)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
