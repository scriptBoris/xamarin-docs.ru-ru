---
title: Панели инструментов в Xamarin.Mac
description: В этой статье описывается работа с панелями инструментов в приложении Xamarin.Mac. Здесь рассматривается создание и обслуживание панелей инструментов в Xcode и конструкторе Interface Builder, предоставляя их в код и программным способом работы с ними.
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6cb17ae0f60390564a8aa6bdb64ea612aae51b55
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120259"
---
# <a name="toolbars-in-xamarinmac"></a>Панели инструментов в Xamarin.Mac

_В этой статье описывается работа с панелями инструментов в приложении Xamarin.Mac. Здесь рассматривается создание и обслуживание панелей инструментов в Xcode и конструкторе Interface Builder, предоставляя их в код и программным способом работы с ними._

Разработчиков Xamarin.Mac, работающих с Visual Studio для Mac имеют доступ к же элементов управления пользовательского интерфейса, доступных для разработчиков macOS, работающих с Xcode, включая панели инструментов. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать для создания и обслуживания элементов панели инструментов конструктора Interface Builder. Эти элементы панели инструментов также могут быть созданы в C#.

Панели инструментов в macOS добавляются в верхней части окна и обеспечивают легкий доступ к командам, связанных с его функциональностью. Панели инструментов можно скрывать, показано или настройки конечным пользователем приложения, и они могут предоставлять элементы панели инструментов по-разному.

В этой статье рассматриваются основы работы с панелями инструментов и элементы панели инструментов в приложении Xamarin.Mac. 

Прежде чем продолжить, прочтите [Привет, Mac](~/mac/get-started/hello-mac.md) статьи — в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы — так как он рассматриваются основные понятия и методы, которые будут использоваться в этом руководстве.

Также взгляните на [предоставление C# классы и методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документа. Здесь объясняется `Register` и `Export` атрибуты, используемые для подключения к классам Objective-C классов C#.

## <a name="introduction-to-toolbars"></a>Общие сведения о панели инструментов

Любое окно в приложении macOS можно включить панель инструментов:

![Пример окна с панели инструментов](toolbar-images/info01.png "пример окна с панели инструментов")

Панели инструментов предоставляют простой способ для пользователей вашего приложения для быстрого доступа к важным или часто используемых функций. Например приложения, редактирование документов предоставит элементов панели инструментов для задания цвета текста, изменение шрифта или печать текущего документа.

Панели инструментов можно отобразить элементы тремя способами:

1. **Значок и текст** 

     ![Панель инструментов с значки и текст](toolbar-images/info02.png "панели инструментов с значки и текст")

2. **Значок текста** 

     ![Только для значок панели инструментов](toolbar-images/info03.png "только для значок панели инструментов")

3. **Только текст** 

     ![Панель инструментов текстовые](toolbar-images/info04.png "панели инструментов только текст")

Переключаться между этих режимов, щелкнув правой кнопкой мыши на панели инструментов и выбрав режим отображения в контекстном меню:

![Контекстное меню для панели инструментов](toolbar-images/info05.png "контекстное меню для панели инструментов")

Используйте это меню для отображения панели инструментов с меньшим размером.

![Панель инструментов с помощью небольших значков](toolbar-images/info06.png "панели инструментов с мелкие значки")

Меню позволяет также для настройки панели инструментов:

![Диалоговое окно, используемое для настройки панели инструментов](toolbar-images/info07.png "диалогового окна, используемый для настройки панели инструментов")

При настройке в панели инструментов конструктора Interface Builder, разработчик может предоставить дополнительные панели инструментов элементы, которые не являются частью конфигурации по умолчанию. Пользователи приложения можно настроить панели инструментов, добавление и удаление этих предварительно определенных элементов, при необходимости. Само собой панели инструментов можно сбросить конфигурацию по умолчанию.

Панель инструментов автоматически подключается к **представление** меню, которое позволяет скрыть его, покажите ее, а также настройте его:

![Элементы панели инструментов в меню "Вид"](toolbar-images/info08.png "элементам, связанным с панели инструментов, в меню \"Вид\"")

См. в разделе [встроенные функции меню](~/mac/user-interface/menu.md) документации для получения дополнительных сведений.

Кроме того при правильной настройке панели инструментов в конструктор Interface Builder приложения будут автоматически сохраняться Настройка панели инструментов между несколькими запуски приложения.

В следующих разделах этого руководства описываются способы создания и настройки панелей инструментов с помощью конструктора Interface Builder и способы работы с ними в коде.

## <a name="setting-a-custom-main-window-controller"></a>Установка контроллера пользовательских главное окно

Чтобы предоставить элементы пользовательского интерфейса для кода C# с помощью переменных экземпляров и действий, приложения Xamarin.Mac необходимо использовать контроллер пользовательских окон:

1. Откройте приложения раскадровку Interface Builder в Xcode.
2. Выберите контроллер окна в рабочей области конструирования.
3. Переключиться в режим **инспектор удостоверений** и введите «WindowController» в качестве **имя класса**: 

    [![Настройка имени пользовательского класса для контроллера окно](toolbar-images/windowcontroller01.png "задание имени пользовательского класса для контроллер окна")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации.
5. Объект **WindowController.cs** файл будет добавлен в проект в **панели решения** в Visual Studio для Mac: 

    ![Выбрав на панели решения WindowController.cs](toolbar-images/windowcontroller02.png "выбора WindowController.cs на панели решения")

6. Снова откройте в раскадровку Interface Builder в Xcode.
7. **WindowController.h** файл будет доступен для использования: 

    [![Файл WindowController.h](toolbar-images/windowcontroller03.png "WindowController.h файла")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>Создание и обслуживание панелей инструментов в Xcode

Панели инструментов создаются и настраиваются с помощью конструктора Interface Builder. Чтобы добавить панель инструментов приложения, измените раскадровки основного приложения (в данном случае **Main.storyboard**), дважды щелкнув его в **панели решения**:

![Открытие панели решения Main.storyboard](toolbar-images/edit01.png "Открытие Main.storyboard на панели решения")

В **инспектор библиотеки**, введите «средства» в **поле поиска** чтобы было проще видеть все доступные элементы панели инструментов:

![В инспекторе библиотеки отфильтрованы, чтобы показать элементы панели инструментов](toolbar-images/edit02.png "инспектор библиотеки, отфильтрованы, чтобы показать элементы панели инструментов")

Перетащите окно в панели инструментов **редактор интерфейса**. С помощью панели инструментов, выбранной, настроить его поведение, задав свойства в **инспектор атрибутов**:

![Инспектор атрибутов для панели инструментов](toolbar-images/edit04.png "инспектор атрибутов для панели инструментов")

Доступны следующие свойства.

1. **Отображение** -управляет ли на панели инструментов отображаются значки и текст
2. **Отображается при запуске** — Если выбран, панели инструментов не отображается по умолчанию.
3. **Настраиваемые** — Если выбран этот параметр, пользователи могут изменять и настраивать панели инструментов.
4. **Разделитель** -Если флажок установлен, тонкой горизонтальной линии разделяет содержимое окна панели инструментов.
5. **Размер** -задает размер панели инструментов
6. **Эта функция** -Если флажок установлен, приложения должны сохраняться пользовательских изменений конфигурации панели инструментов запуска приложения.

Выберите **автосохранения** параметр и оставьте все остальные свойства со значениями по умолчанию. 

После открытия панели инструментов в **иерархия интерфейса**, откроется диалоговое окно настройки, выбрав элемент панели инструментов:

![Настройка панели инструментов](toolbar-images/edit05.png "Настройка панели инструментов")

Используйте это диалоговое окно для задания свойств для элементов, которые уже являются частью панели инструментов для разработки инструментов по умолчанию для приложения, и для предоставления дополнительных инструментов элементов для пользователя для выбора при настройке панели инструментов. Чтобы добавить элементы панели инструментов, перетащите их из **инспектор библиотеки**:

![В инспекторе библиотеки](toolbar-images/edit06.png "в инспекторе библиотеки")

Можно добавить следующие элементы панели инструментов.

- **Изображение элемента панели инструментов** -элемент панели инструментов с помощью пользовательского образа в качестве значка.
- **Гибкий элемент панели инструментов места** -гибкие пространство, используемое для выравнивания элементов последующих панели инструментов. Например один или несколько элементов панели инструментов, следуют элементом панели инструментов гибкие пространства и другого элемента панели инструментов закрепляете последний элемент в правой части панели инструментов.
- **Элемент панели инструментов места** -фиксированной пространство между элементами на панели инструментов
- **Элемент панели инструментов разделитель** -отображается разделитель между двух или более элементов панели инструментов для группирования
- **Настройка панели инструментов элемента** -позволяет пользователям настраивать панели инструментов
- **Печать панели инструментов элемента** -позволяет пользователям печатать открытый документ
- **Отображение цвета элемента панели инструментов** -отображается палитра цветов стандартной системы: 

     ![Системная палитра](toolbar-images/edit07.png "системной палитры цветов")

- **Отображение шрифта элемента панели инструментов** -отображает диалоговое окно стандартный системный шрифт: 

     ![Средство выбора шрифта](toolbar-images/edit08.png "средство выбора шрифта")

> [!IMPORTANT]
> Как будет показано позже, многие стандартные элементы управления пользовательского интерфейса Cocoa поля поиска, Сегментированные элементы управления и по горизонтали ползунки можно также добавить на панель инструментов.

### <a name="adding-an-item-to-a-toolbar"></a>Добавление элемента панели инструментов

Чтобы добавить элемент на панель инструментов, выберите на панели инструментов в **иерархия интерфейса** и выберите один из его элементов, причиной появления диалогового окна настройки. Затем перетащите элемент из **инспектор библиотеки** для **элементы панели инструментов разрешено** области:

![Допускается элементы панели инструментов в окне настройки панели инструментов](toolbar-images/add01.png "допускается элементы панели инструментов в окне настройки панели инструментов")

Чтобы убедиться, что элемент входит в область инструментов по умолчанию, перетащите его на значок **элементы панели инструментов по умолчанию** области: 

![Изменение порядка элементом панели инструментов, перетащив](toolbar-images/add02.png "переупорядочивание, перетащив элемент панели инструментов")

Чтобы изменить порядок элементов панели инструментов по умолчанию, перетащите их влево или вправо.

Затем используйте **инспектор атрибутов** для задания свойств по умолчанию для элемента:

![Настройка элементов панели инструментов, с помощью инспектора атрибуты](toolbar-images/add03.png "Настройка элементов панели инструментов, с помощью инспектора атрибуты")

Доступны следующие свойства.

- **Имя_образа** -образа для использования в качестве значка для элемента
- **Метка** -текст, отображаемый для элемента на панели инструментов
- **Метка палитры** -текст, отображаемый для элемента в **элементы панели инструментов разрешено** области
- **Тег** — необязательный, уникальный идентификатор, который помогает идентифицировать элементу в коде.
- **Идентификатор** -определяет тип элемента панели инструментов. Пользовательское значение, можно выбрать элемент панели инструментов в коде.
- **Можно выбрать** — Если этот флажок установлен, элемент будет действовать как кнопка Вкл/Выкл.

> [!IMPORTANT]
> Добавление элемента в **элементы панели инструментов разрешено** область, но не панели инструментов по умолчанию, чтобы предоставить параметры настройки для пользователей. 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>Добавление других элементов управления пользовательского интерфейса на панель инструментов

Несколько элементов пользовательского интерфейса Cocoa, такие как поиск поля и Сегментированные элементы управления можно также добавить на панель инструментов.

Чтобы попробовать сделать это, откройте на панели инструментов в **иерархия интерфейса** и выбрать элемент панели инструментов, чтобы открыть диалоговое окно настройки. Перетащите **поля поиска** из **инспектор библиотеки** для **элементы панели инструментов разрешено** области:

![С помощью диалогового окна настройки инструментов](toolbar-images/add05.png "с помощью в диалоговом окне настройки панели инструментов")

На этой странице используйте конструктор Interface Builder для настройки поля поиска и предоставления его код через действие или переменной экземпляра.

## <a name="built-in-toolbar-item-support"></a>Поддержка встроенной панели инструментов элемента

Несколько элементов пользовательского интерфейса Cocoa взаимодействия с элементами на стандартной панели инструментов по умолчанию. Например, перетащите **представления текста** на окно приложения и разместите его для заполнения области содержимого:

[![Добавление представления текста в приложение](toolbar-images/edit09.png "Добавление представления текста в приложение")](toolbar-images/edit09-large.png#lightbox)

Сохраните документ, вернитесь в Visual Studio для Mac синхронизировать с Xcode, запустите приложение, ввести некоторый текст, выберите его и нажмите кнопку **цвета** элемент панели инструментов. Обратите внимание, что представление текста автоматически работает с палитра цветов:

![Функциональные возможности встроенной панели инструментов с помощью средства выбора текстового представления и цвет](toolbar-images/edit10.png "функциональных возможностей встроенной панели инструментов с помощью средства выбора представления и цвет текста")

## <a name="using-images-with-toolbar-items"></a>Использование изображений с элементами панели инструментов

С помощью **элемент панели инструментов изображения**, добавляемый растровое изображение **ресурсы** папки (и действием сборки **пакета ресурсов**) могут отображаться на панели инструментов в виде значка:

1. В Visual Studio для Mac в **панели решения**, щелкните правой кнопкой мыши **ресурсы** папку и выберите **добавить** > **добавить файлы** .
2. Из **добавить файлы** диалогового окна поле, перейдите к требуемой образов, выберите их и нажмите кнопку **откройте** кнопки: 

    [![Выбор изображений, чтобы добавить](toolbar-images/edit11.png "выборе образов для добавления")](toolbar-images/edit11-large.png#lightbox)

3. Выберите **копирования**, проверьте **использовать одинаковое действие для всех выбранных файлов**и нажмите кнопку **ОК**:

    ![При выборе действия копирования для добавленных изображений](toolbar-images/edit12.png "при выборе действия копирования для добавленных изображений")

4. В **панели решения**, дважды щелкните **MainWindow.xib** чтобы открыть его в Xcode.

5. Выберите на панели инструментов в **иерархия интерфейса** и выберите один из его элементов, чтобы открыть диалоговое окно настройки.

6. Перетащите **элемент панели инструментов изображения** из **инспектор библиотеки** на панель инструментов **элементы панели инструментов разрешено** области: 

    ![Добавлен элемент панели инструментов изображение в область элементов панели инструментов разрешено](toolbar-images/edit14.png "элемент панели инструментов изображения добавлен в область элементов панели инструментов разрешено")

7. В **инспектор атрибутов**, выберите образ, который был только что добавлен в Visual Studio для Mac: 

    ![Задание пользовательского изображения для элемента панели инструментов](toolbar-images/edit15.png "задание пользовательского изображения для элемента панели инструментов")

8. Задайте **метка** в «Удаленные» и **меток палитры** «Стереть документа»: 

    ![Настройка панели инструментов элемента метку и метка палитры](toolbar-images/edit16.png "Настройка панели инструментов элемента метку и метка палитры")

9. Перетащите **элемент панели инструментов разделитель** из **инспектор библиотеки** на панель инструментов **элементы панели инструментов разрешено** области: 

    [![Элемент панели инструментов разделитель добавлен в область элементов панели инструментов разрешено](toolbar-images/edit17.png "разделителя панели инструментов элемент добавлен в область элементов панели инструментов разрешено")](toolbar-images/edit17-large.png#lightbox)

10. Перетащите элемент разделителя и элемента «Корзина» **элементы панели инструментов по умолчанию** области и порядок панели элементов из набора слева направо, следующим образом (цвета, шрифты, разделитель, корзины, гибкие пространство, печать): 

    ![Указанные элементы панели инструментов по умолчанию](toolbar-images/edit18.png "указанные элементы панели инструментов по умолчанию")

11. Сохраните изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Запустите приложение, чтобы убедиться, что новая панель инструментов отображается по умолчанию:

![Панель инструментов с элементами настроенный по умолчанию](toolbar-images/edit19.png "панель инструментов с элементами настроенный по умолчанию")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>Предоставление элементов панели инструментов с помощью переменных экземпляров и действий

Для доступа к панели инструментов или элемента панели инструментов в коде, он должен быть подключен к переменной экземпляра или действие:

1. В **панели решения**, дважды щелкните **Main.storyboard** чтобы открыть его в Xcode.
2. Убедитесь, что «WindowController» был назначен контроллеру главного окна в пользовательском классе **инспектор удостоверений**:

    [![С помощью инспекторе удостоверения для задания пользовательского класса для контроллера окно](toolbar-images/edit20a.png "использование инспекторе удостоверения для задания пользовательского класса для контроллер окна")](toolbar-images/edit20a-large.png#lightbox)

3. Затем выберите элемент панели инструментов в **иерархия интерфейса**: 

    ![Выбрав элемент панели инструментов в иерархии интерфейсов](toolbar-images/edit20.png "Выбор элемента панели инструментов в иерархии интерфейса")  

4. Откройте **Assistant представление**выберите **WindowController.h** файла и перетащите из элемента панели инструментов **WindowController.h** файла.
5. Задайте **подключения** тип **действие**, введите «trashDocument» для **имя**и нажмите кнопку **Connect** кнопки: 

    [![Настройка действия для элемента панели инструментов](toolbar-images/edit23.png "настройке действия для элемента панели инструментов")](toolbar-images/edit23-large.png#lightbox)

6. Предоставлять **представления текста** как называется «documentEditor» переменной экземпляра **ViewController.h** файла: 

    [![Настройка выхода для представления текста](toolbar-images/edit24.png "Настройка выхода для текстового представления.")](toolbar-images/edit24-large.png#lightbox)

7. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

В Visual Studio для Mac, отредактируйте **ViewController.cs** файл и добавьте следующий код:

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

Далее следует изменить **WindowController.cs** и добавьте следующий код в нижнюю часть `WindowController` класса:

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

При запуске приложения, **корзины** элемент панели инструментов будет всегда активной:

![Панель инструментов с элементом корзину active](toolbar-images/edit25.png "панель инструментов с элементом active корзины")

Обратите внимание, что **корзины** теперь элемент панели инструментов можно использовать для удаления текста.

## <a name="disabling-toolbar-items"></a>Отключение элементов панели инструментов

Чтобы отключить элемент на панели инструментов, создайте пользовательский `NSToolbarItem` класса и переопределить `Validate` метод. Затем в Interface Builder, назначьте для пользовательского типа элемента, который вы хотите включить или отключить.

Можно создавать пользовательские `NSToolbarItem` класса, правой кнопкой мыши проект и выберите **добавить** > **новый файл...** . Выберите **Общие** > **пустой класс**, введите «ActivatableItem» **имя**и нажмите кнопку **New** кнопки: 

![Добавление пустой класс в Visual Studio для Mac](toolbar-images/custom01.png "Добавление пустой класс в Visual Studio для Mac")

Далее следует изменить **ActivatableItem.cs** файл следующим образом:

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

Дважды щелкните **Main.storyboard** чтобы открыть его в Xcode. Выберите **корзины** элемент панели инструментов, созданную выше и изменение класса на «ActivatableItem» в **инспектор удостоверений**:

![Установка пользовательского класса для элемента панели инструментов](toolbar-images/custom02.png "параметр пользовательский класс для элемента панели инструментов")

Создание переменной экземпляра вызывается `trashItem` для **корзины** элемент панели инструментов. Сохраните изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode. Наконец, откройте **MainWindow.cs** и обновить `AwakeFromNib` метод следующим образом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

Запустите приложение и обратите внимание, что **корзины** элемент теперь отключен на панели инструментов:

![Панель инструментов с элементом неактивные корзины](toolbar-images/custom03.png "панель инструментов с элементом неактивные корзины")

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с панелями инструментов и элементы панели инструментов в приложении Xamarin.Mac. Оно описано, как создавать и поддерживать панели инструментов конструктора Interface Builder, как некоторые элементы управления пользовательского интерфейса автоматически работают с элементы панели инструментов, как работать с панелями инструментов в коде C# и как включить и отключить элементы панели инструментов.


## <a name="related-links"></a>Связанные ссылки

- [MacToolbar (пример)](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Рекомендации по работе с человеческим интерфейсом для панелей инструментов](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [Общие сведения о панели инструментов](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)
