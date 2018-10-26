---
title: Основные сведения о конструкторе iOS
description: Это руководство описывает конструктор Xamarin для iOS. Он демонстрирует, как использовать конструктор iOS для визуального размещения элементов управления, как получить доступ к эти элементы управления в коде и изменение свойств.
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/31/2018
ms.openlocfilehash: e9c2a42b9108c04f18252a410d40dbc03013f6dd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123756"
---
# <a name="ios-designer-basics"></a>Основные сведения о конструкторе iOS

_Это руководство описывает конструктор Xamarin для iOS. Он демонстрирует, как использовать конструктор iOS для визуального размещения элементов управления, как получить доступ к эти элементы управления в коде и изменение свойств._

Xamarin для iOS используется конструктор визуальный интерфейс, аналогичную Interface Builder в Xcode и конструкторе Android. Ниже перечислены некоторые из многих функций прозрачную интеграцию с Visual Studio для Mac и Visual Studio 2015 и 2017, редактирования и перетаскивать, интерфейс для настройки обработчики событий и возможность отображения пользовательских элементов управления.

## <a name="requirements"></a>Требования

Конструктор iOS — доступно в Visual Studio для Mac и Visual Studio 2015 и 2017 на Windows. В Visual Studio 2015 или 2017 конструктор iOS требуется подключение к правильно настроенные узлу сборки Mac, на то, что нет нужды выполняться Xcode.

В этом руководстве предполагается Знакомство с содержимым, охваченных [руководства по началу работы](~/ios/get-started/index.md).

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>Как работает конструктор iOS

В этом разделе описывается, как конструктор iOS упрощает создание пользовательского интерфейса и привязать его к коду.

Конструктор iOS позволяет разработчикам визуальное проектирование пользовательского интерфейса приложения. Как описано в [введение в раскадровки](~/ios/user-interface/storyboards/index.md) руководства вы узнаете раскадровки экраны (контроллеров представления), составляющие приложение, элементы интерфейса (представления), размещенные на этих контроллеров представлений и Общая последовательность навигации приложения . 

Контроллер представления состоит из двух частей: визуальное представление в конструкторе iOS, а связанный класс C#:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Контроллер представления в конструкторе iOS](introduction-images/1-storyboardwithviewcontroller-vsmac.png "контроллер представления в конструкторе iOS")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![Код для контроллера представления](introduction-images/2-viewcontrollercode-vsmac.png "код для контроллера представления")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Контроллер представления в конструкторе iOS](introduction-images/1-storyboardwithviewcontroller-vs.png "контроллер представления в конструкторе iOS")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![Код для контроллера представления](introduction-images/2-viewcontrollercode-vs.png "код для контроллера представления")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

В состоянии по умолчанию контроллер представления не предоставляет никакой функциональности; он должен заполняться с помощью элементов управления. Эти элементы управления помещаются в представление контроллера представления, прямоугольной области, которая содержит все содержимое экрана. Большинство контроллеров представления содержат стандартные элементы управления, такие как кнопки, метки и текстовые поля, как показано на следующем снимке экрана, показывающий контроллер представления, содержащую кнопку: 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Контроллер представления, содержащую кнопку](introduction-images/3-viewcontrollerwithbutton-vsmac.png "контроллер представления, содержащую кнопку")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Контроллер представления, содержащую кнопку](introduction-images/3-viewcontrollerwithbutton-vs.png "контроллер представления, содержащую кнопку")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

Некоторые элементы управления, такие как метки, содержащий статический текст, можно добавить в контроллер представлений и больше не трогать. Тем не менее чаще, элементы управления должны настраиваться программно. Например кнопки, добавленных выше выполнения определенного действия шифрованию, поэтому необходимо добавить обработчик событий в коде.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Для получения доступа и управления кнопки в коде, он должен иметь уникальный идентификатор. Укажите уникальный идентификатор, нажав кнопку, открыв **панели свойств**и его **имя** на значение, например «SubmitButton»:

[![Задание имени кнопки на панели свойств](introduction-images/4-settingbuttonname-vsmac.png "задание имени кнопки на панели свойств")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Для получения доступа и управления кнопки в коде, он должен иметь уникальный идентификатор. Укажите уникальный идентификатор, нажав кнопку, открыв **окно "Свойства"** и его **имя** на значение, например «SubmitButton»:

[![Задание имени кнопки в окне свойств](introduction-images/4-settingbuttonname-vs.png "кнопки имя параметра в окне «Свойства»")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

Теперь, когда кнопка имеет имя, он может осуществляться в коде. Но как это работает?

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

В **панели решения**, переходе по адресу **ViewController.cs** и щелчке индикатора раскрытие показывают, что контроллер представления `ViewController` диапазонов определение класса двух файлов, каждый из которых содержит [разделяемый класс](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) определение:

[![Двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

В **обозревателе решений**, переходе по адресу **ViewController.cs** и щелчке индикатора раскрытие показывают, что контроллер представления `ViewController` определение класса охватывает два файла, каждый из содержащее [разделяемый класс](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) определение:

[![Двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "двух файлов, составляющих класс ViewController: ViewController.cs и ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** должны устанавливаться с пользовательским кодом, связанные с `ViewController` класса. В этом файле `ViewController` класс может реагировать на различные iOS методы жизненного цикла контроллера представления, настроить пользовательский Интерфейс и отвечать на пользовательский ввод, например нажимает кнопку.

- **ViewController.designer.cs** — это созданный файл, созданный конструктор для сопоставления интерфейс визуально сконструированный кода iOS. Так как изменения в этот файл будут перезаписаны, его не следует изменять. Объявления свойств в этом файле делают возможным для кода в `ViewController` класса к доступу по **имя**, набор элементов управления вверх в конструкторе iOS. Открытие **ViewController.designer.cs** показывает следующий код:

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

`SubmitButton` Объявление свойства подключается всего `ViewController` класса — не просто **ViewController.designer.cs** файл — для кнопки, определенных в раскадровке. Так как **ViewController.cs** определяет часть `ViewController` класс, он имеет доступ к `SubmitButton`.

На следующем снимке экрана показано, что IntelliSense теперь распознает `SubmitButton` в ссылку на **ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![IntelliSense, распознавая ссылки SubmitButton](introduction-images/6-submitbuttonintellisense-vsmac.png "IntelliSense, распознавая SubmitButton ссылки")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IntelliSense, распознавая ссылки SubmitButton](introduction-images/6-submitbuttonintellisense-vs.png "IntelliSense, распознавая SubmitButton ссылки")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

В этом разделе показано, как создать кнопку в конструкторе iOS и получить доступ к этой кнопки в коде.

В оставшейся части этого документа предоставляет дополнительные общие сведения о конструкторе iOS.

## <a name="ios-designer-basics"></a>Основные сведения о конструкторе iOS

В этом разделе представлены части конструктора iOS и предоставляет обзор его возможностей.

### <a name="launching-the-ios-designer"></a>Запуск конструктора iOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Проекты Xamarin.iOS, созданных с помощью Visual Studio для Mac включают в себя раскадровки. Чтобы просмотреть содержимое раскадровки, дважды щелкните файл .storyboard в **панели решения**:

[![Раскадровка открыта в конструкторе iOS](introduction-images/7-storyboardopen-vsmac.png "раскадровка открыта в конструкторе iOS")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Большинство проектов Xamarin.iOS, созданные в Visual Studio 2015 или 2017 включают раскадровки. Чтобы просмотреть содержимое раскадровки, дважды щелкните файл .storyboard в **обозревателе решений**:

[![Раскадровка открыта в конструкторе iOS](introduction-images/7-storyboardopen-vs.png "раскадровка открыта в конструкторе iOS")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>функции конструктора iOS

Конструктор iOS имеет шесть основных раздела:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Части конструктора iOS](introduction-images/8-sixpartsofiosdesigner-vsmac.png "части конструктора iOS")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Область конструктора** — основной рабочей областью конструктора iOS. Отображается в области документа, он позволяет визуального создания пользовательских интерфейсов.
2. **Панель инструментов ограничений** — позволяет переключения между различными фрейма редактирования режим и режим редактирования ограничение, два разных способа для размещения элементов в пользовательском интерфейсе.
3. **Панель элементов** — перечислены контроллеры, объектов, элементы управления, представления данных, средства распознавания жестов, windows и полосы, можно перетащить в область конструктора и добавить к пользовательскому интерфейсу.
4. **Панель свойств** — отображаются свойства выбранного элемента управления, включая удостоверения, визуальные стили, специальных возможностей, макет и поведение.
5. **Структура документа** — показано дерево элементов управления, составляющих макета для редактируемого интерфейса. Щелкнув элемент в дереве выбирает его в конструкторе iOS и показывает его свойства в **панели свойств**. Это удобно для выбора конкретного элемента управления в интерфейсе пользователя с глубоким вложением.
6. **Нижняя панель инструментов** — содержит параметры для изменения, как конструктор iOS отображает .storyboard или .xib файла, включая устройства, ориентации и масштаба.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Части конструктора iOS](introduction-images/8-sixpartsofiosdesigner-vs.png "части конструктора iOS")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Область конструктора** — основной рабочей областью конструктора iOS. Отображается в области документа, он позволяет визуального создания пользовательских интерфейсов.
2. **Панель инструментов ограничений** — позволяет переключения между различными фрейма редактирования режим и режим редактирования ограничение, два разных способа для размещения элементов в пользовательском интерфейсе.
3. **Панель элементов** — перечислены контроллеры, объектов, элементы управления, представления данных, средства распознавания жестов, windows и полосы, можно перетащить в область конструктора и добавить к пользовательскому интерфейсу.
4. **Окно "Свойства"** — отображаются свойства выбранного элемента управления, включая удостоверения, визуальные стили, специальных возможностей, макет и поведение.
5. **Структура документа** — показано дерево элементов управления, составляющих макета для редактируемого интерфейса. Щелкнув элемент в дереве выбирает его в конструкторе iOS и показывает его свойства в **окно "Свойства"**. Это удобно для выбора конкретного элемента управления в интерфейсе пользователя с глубоким вложением.
6. **Нижняя панель инструментов** — содержит параметры для изменения, как конструктор iOS отображает .storyboard или .xib файла, включая устройства, ориентации и масштаба.

-----

### <a name="design-workflow"></a>Рабочий процесс разработки

#### <a name="adding-a-control-to-the-interface"></a>Добавление элемента управления интерфейс

Чтобы добавить элемент управления интерфейса, перетащите его из **элементов** и поместите его в рабочей области конструирования. При добавлении или элементов управления, рекомендации по вертикали и горизонтали выделите позиции часто используемых макета, например по вертикали по центру, в середине и поля:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
 
![В области конструктора рекомендации выделите позиции макета, часто используемых](introduction-images/9-layoutguides-vsmac.png "в области конструктора рекомендации выделите позиции часто используемых макета")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![В области конструктора рекомендации выделите позиции макета, часто используемых](introduction-images/9-layoutguides-vs.png "в области конструктора рекомендации выделите позиции часто используемых макета")

-----

Синяя линия пунктирная, в примере выше рекомендации, в середине визуальную компоновку для облегчения размещение кнопки.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

#### <a name="context-menu-commands"></a>Команды контекстного меню

Контекстное меню доступна как в рабочей области конструирования, так и в **Структура документа**. В этом меню доступны команды для выбранного элемента управления и его родительский элемент, что полезно при работе с представлениями во вложенной иерархии:

[![В контекстном меню в области конструктора](introduction-images/10-contextmenudesignsurface-vsmac.png "контекстное меню в области конструктора")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>Панель инструментов ограничений

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
 
[![На панели инструментов ограничения](introduction-images/11-constraintstoolbar-vsmac.png "панель инструментов ограничений")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![На панели инструментов ограничения](introduction-images/11-constraintstoolbar-vs.png "панель инструментов ограничений")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

Панель инструментов ограничений был обновлен и теперь состоит из двух элементов управления: кадра, в режиме редактирования / ограничение редактирования переключиться в режим и ограничения обновления / обновить кадры кнопки.

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>Режим редактирования кадров / переключиться в режим редактирования ограничение

В предыдущих версиях конструктора iOS щелкните уже выбранный представление на поверхности разработки переключаться между фрейма редактирования и режим редактирования ограничение. Теперь элемент управления "выключатель" на панели инструментов ограничений переключается между режимами редактирования.

- Режим редактирования кадров:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Кадр, кнопку режима изменения](introduction-images/12a-frameeditingmode-vsmac.png "фрейма редактирования кнопки режима")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Кадр, кнопку режима изменения](introduction-images/12a-frameeditingmode-vs.png "фрейма редактирования кнопки режима")

-----

- Режим редактирования ограничение:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

![Ограничение, кнопку режима изменения](introduction-images/12b-constrainteditingmode-vsmac.png "кнопка редактирования режима ограничений")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Ограничение, кнопку режима изменения](introduction-images/12b-constrainteditingmode-vs.png "кнопка редактирования режима ограничений")

-----

#### <a name="update-constraints--update-frames-button"></a>Обновить ограничения / обновить кадры кнопки

Ограничения обновления и обновления кадров находится кнопка справа от фрейма, в режиме редактирования / переключиться в режим редактирования ограничение.

- В режим редактирования кадров при нажатии этой кнопки корректирует кадры любых выбранных элементов в соответствии с их ограничений.
- В режиме редактирования ограничение при нажатии этой кнопки корректирует ограничения любых выбранных элементов в соответствии с их кадры.

### <a name="bottom-toolbar"></a>Нижняя панель инструментов

Нижняя панель инструментов предоставляет возможность выбора устройства, ориентации и масштабирования, используемый для просмотра файла storyboard "или".xib в конструкторе iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Нижняя панель инструментов, используемый для выбора устройства и ориентации поверхности разработки](introduction-images/13-bottomtoolbar-vsmac.png "нижней панели инструментов, используемый для выбора устройства и ориентации поверхности разработки")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Нижняя панель инструментов, используемый для выбора устройства и ориентации поверхности разработки](introduction-images/13-bottomtoolbar-vs.png "нижней панели инструментов, используемый для выбора устройства и ориентации поверхности разработки")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>Устройства и ориентация

При развертывании в нижней панели инструментов отображает все устройства, ориентации или адаптации, применимые к текущему документу. Щелкнув их изменяет представление, отображаемое в области конструктора. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Нижняя панель инструментов, развернуть в том, чтобы отобразить устройств и ориентации](introduction-images/14-bottomtoolbarexpanded-vsmac.png "нижней панели инструментов, развернуть, чтобы отобразить устройств и ориентации")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Нижняя панель инструментов, развернуть в том, чтобы отобразить устройств и ориентации](introduction-images/14-bottomtoolbarexpanded-vs.png "нижней панели инструментов, развернуть, чтобы отобразить устройств и ориентации")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

Обратите внимание на то, что при выборе устройства и ориентация меняется только как конструктор iOS выполняет предварительный просмотр конструктора. Независимо от текущего выделения, добавляемых в ограничения применяются на всех устройствах и ориентации, если не **изменить признаки** кнопку использовался для указано иное.

При [классы размера](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) являются [включена](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes), **изменить признаки** кнопка будет отображаться в развернутой нижней панели инструментов.  Щелкнув **изменить признаки** кнопка отображает параметры для создания интерфейса вариантов, в зависимости от размера класса, представленного на выбранном устройстве и ориентацию. Рассмотрим следующие примеры:

- Если **iPhone SE** / **книжной**, будет выбран, контекстном предложит варианты для создания вариант интерфейса для compact ширину класса размера регулярных высоту. 
- Если **iPad Pro 9,7"** / **Альбомная** / **во весь экран** — флажок установлен, контекстном предоставит возможность создать вариант интерфейса для регулярные width и регулярного высота размер класса.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Нижняя панель инструментов используется для изменения интерфейса класса размера](introduction-images/15-edittraitsbutton-vsmac.png "нижней панели инструментов используется для изменения интерфейса класса размера")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Нижняя панель инструментов используется для изменения интерфейса класса размера](introduction-images/15-edittraitsbutton-vs.png "нижней панели инструментов используется для изменения интерфейса класса размера")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>Элементы управления масштабом

В область конструктора поддерживает масштабирование с помощью нескольких элементов управления:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
 
![Элементы управления масштабом в нижней панели инструментов](introduction-images/16-zoomcontrols-vsmac.png "элементы управления масштабом в нижней панели инструментов")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Элементы управления масштабом в нижней панели инструментов](introduction-images/16-zoomcontrols-vs.png "элементы управления масштабом в нижней панели инструментов")

-----

Элементы управления включают следующее:

1. По размеру окна
2. Мельче
3. Увеличить
4. Фактический размер (размер в пикселях 1:1)

Эти элементы управления увеличьте масштаб поверхности разработки. Они не влияют на пользовательском интерфейсе приложения во время выполнения.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

### <a name="properties-pad"></a>Панель свойств

Используйте **панели свойств** изменение удостоверения, визуальные стили, специальные возможности и поведение элемента управления. На следующем снимке экрана показаны **панели свойств** параметры для кнопки:

[![Панель свойств для кнопки](introduction-images/17-buttonpropertiespad-vsmac.png "панели свойств для кнопки")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Свойства панели разделы

**Панели свойств** имеется три раздела:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>Окно свойств

Используйте **окно "Свойства"** изменение удостоверения, визуальные стили, специальные возможности и поведение элемента управления. На следующем снимке экрана показаны **окно "Свойства"** параметры для кнопки:

[![Окно свойств для кнопки](introduction-images/17-buttonpropertieswindow-vs.png "окно свойств для кнопки")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>Свойства окна разделы

**Окно "Свойства"** имеется три раздела:

-----

1.  **Мини-приложение** — основные свойства элемента управления, такие как имя класса, стиль, свойств и других. Свойства для управления содержимым элемента управления обычно размещаются здесь.
2.  **Макет** — здесь перечислены свойства, позволяющие проследить, положение и размер элемента управления, включая ограничения и кадров,.
3.  **События** — события и обработчики событий указаны здесь. Это удобно для обработки событий, таких как сенсорного ввода, касания, перетаскивания и т. д. Кроме того, события могут обрабатываться непосредственно в коде.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>Изменение свойств в панели свойств

Помимо визуального редактирования в области конструктора, конструктор iOS поддерживает редактирование свойств в **панели свойств**. Доступные свойства изменяются в зависимости от выбранного элемента управления, как показано на снимке экрана ниже:

[![Свойства кнопок](introduction-images/18a-buttonpropertiespad-vsmac.png "свойства кнопок")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![Просмотреть свойства контроллера](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "просмотреть свойства контроллера")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>Изменение свойств в окне «Свойства»

Помимо визуального редактирования в области конструктора, конструктор iOS поддерживает редактирование свойств в **окно "Свойства"**. Доступные свойства изменяются в зависимости от выбранного элемента управления, как показано на снимке экрана ниже:

[![Свойства кнопок](introduction-images/18a-buttonpropertieswindow-vs.png "свойства кнопок")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![Просмотреть свойства контроллера](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "просмотреть свойства контроллера")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> В разделе Identity теперь отображает панель свойств **модуль** поля. Это необходимо для заполнения в этом разделе только в том случае, при взаимодействии с классами Swift. Используйте его для ввода имя модуля для Swift классы, являющиеся пространств имен.

#### <a name="default-values"></a>Значения по умолчанию

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Многие свойства в **панели свойств** Показать нет значения или значение по умолчанию. Тем не менее код приложения может по-прежнему изменять эти значения. **Панели свойств** не содержит значения, заданные в коде.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Многие свойства в **окно "Свойства"** Показать нет значения или значение по умолчанию. Тем не менее код приложения может по-прежнему изменять эти значения. **Окно "Свойства"** не содержит значения, заданные в коде.

-----

#### <a name="event-handlers"></a>Обработчики событий

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Чтобы указать пользовательские обработчики событий для различных событий, используйте **события** вкладке **панели свойств**. Например, на снимке экрана ниже `HandleClick` метод обрабатывает кнопки **Touch вверх внутри** событий:

[![Панель свойств, с помощью обработчика событий для кнопки](introduction-images/19-buttonpropertiespadevents-vsmac.png "панели свойств, с помощью обработчика событий для кнопки")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы указать пользовательские обработчики событий для различных событий, используйте **события** вкладке **окно "Свойства"**. Например, на снимке экрана ниже `HandleClick` метод обрабатывает кнопки **Touch вверх внутри** событий:

[![В окне «Свойства» с помощью обработчика событий для кнопки](introduction-images/19-buttonpropertieswindowevents-vs.png "окно свойств, с помощью обработчика событий для кнопки")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

После указания обработчика событий, метод с тем же именем необходимо добавить соответствующий класс контроллера представления. В противном случае `unrecognized selector` исключение возникает при нажатии кнопки:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Исключение нераспознанный селектор](introduction-images/20-unrecognizedselector-vsmac.png "исключение нераспознанный селектора")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

Обратите внимание, что после обработчика событий был указан в **панели свойств**, конструктор iOS будет немедленно открыть соответствующий файл кода и предложения для вставки объявления метода. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Исключение нераспознанный селектор](introduction-images/20-unrecognizedselector-vs.png "исключение нераспознанный селектора")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

Пример, использующий пользовательские обработчики событий, см. в разделе [Привет, iOS: руководство по началу работы](~/ios/get-started/hello-ios/index.md).

### <a name="outline-view"></a>Представление структуры

Конструктор iOS также можно отобразить иерархии элементов управления интерфейса как структуры. Контур доступна, выбрав **Структура документа** вкладке, как показано ниже:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Структура документа](introduction-images/21-buttonoutlineview-vsmac.png "структуры документа")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Структура документа](introduction-images/21-buttonoutlineview-vs.png "структуры документа")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

Выбранный элемент управления в представлении структуры остается синхронизированной с выбранного элемента управления в рабочей области конструирования.  Эта функция полезна для выбора элемента из иерархии глубоко вложенного интерфейса.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

## <a name="revert-to-xcode"></a>Вернуться к Xcode

Можно использовать конструктор iOS и конструктор Interface Builder в Xcode на равных основаниях. Чтобы открыть раскадровку или файл .xib в построителе интерфейса Xcode, щелкните правой кнопкой мыши файл и выберите **открыть с помощью > конструктора Interface Builder Xcode**, как показано на снимке экрана ниже:

[![Открытие раскадровки в построителе интерфейса Xcode](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Открытие раскадровки в построителе интерфейса Xcode")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

После внесения изменений в построителе интерфейса Xcode, сохраните файл и вернитесь в Visual Studio для Mac. Изменения будут синхронизироваться в проект Xamarin.iOS.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

## <a name="xib-support"></a>Поддержка .xib

Конструктор iOS поддерживает создание, изменение и управление xib-файлов. Они являются файлами XML, представлять замкнутую представления единый, пользовательские, которые могут добавляться к иерархии представления приложения. Файл .xib обычно представляет интерфейс для единое представление или экран в приложении, в то время как раскадровка представляет много экранов и переходами между ними.

Существует много мнений о том, какие решения — xib-файлов раскадровки и кода — лучше всего подходит для создания и обслуживания пользовательского интерфейса. На самом деле существует это не идеальное решение, а это всегда стоит обсудить лучшим инструментом для текущего задания. С другой стороны, xib-файлы обычно самый эффективный используется для представления настраиваемого представления, необходимые в нескольких местах в приложении, такие как ячейки представления пользовательских таблиц. 

Дополнительную документацию об использовании xib-файлы можно найти в следующей инструкции:

- [С помощью шаблона .xib представление](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [Создание пользовательских TableViewCell, с помощью .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [Создание с помощью .xib экрана запуска](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

Дополнительные сведения об использовании раскадровок см [введение в раскадровки](~/ios/user-interface/storyboards/index.md).

Это и другие руководства, связанные с конструктором iOS относятся использования раскадровок как стандартный подход для создания пользовательских интерфейсов, так как новые шаблоны проектов большинство Xamarin.iOS предоставляет раскадровку, по умолчанию.

## <a name="summary"></a>Сводка

В этом руководстве представлены общие сведения о конструкторе, описания его компоненты и средства, предоставляемые для разработки привлекательных пользовательских интерфейсов создания структуры iOS.

## <a name="related-links"></a>Связанные ссылки

- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
- [Проектирование Пошаговое руководство элементы управления iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Привет, iOS](~/ios/get-started/hello-ios/index.md)
- [Привет, iOS (несколько экранов)](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Общие сведения о конструкторе Android](~/android/user-interface/android-designer/index.md)
- [Разделяемые классы и методы](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Углубляться в конструкторе Xamarin для iOS — развиваться 2014 (видео)](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [С помощью iOS Designer для создания экрана запуска (видео)](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
