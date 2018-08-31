---
title: Windows в Xamarin.Mac
description: В этой статье рассматривается работа с окнами и панелями в приложении Xamarin.Mac. Он описывает создание окон и панелей в Xcode и конструкторе Interface Builder, загружая их из раскадровки и xib-файлов и программным способом работы с ними.
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: b60b8a6a7c56347d6abf71f8c5149ddd556d3da8
ms.sourcegitcommit: ee66db647ae9d94b54b1c5d9093075a620d0c6b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "40251178"
---
# <a name="windows-in-xamarinmac"></a>Windows в Xamarin.Mac

_В этой статье рассматривается работа с окнами и панелями в приложении Xamarin.Mac. Он описывает создание окон и панелей в Xcode и конструкторе Interface Builder, загружая их из раскадровки и xib-файлов и программным способом работы с ними._

При работе с C# и .NET в приложении Xamarin.Mac, имеют доступ к тем же Windows и панелями, работающему *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для создания и поддержки Windows и панелей (или при необходимости создать их непосредственно в коде C#).

Исходя из его назначение, приложения Xamarin.Mac можно представить один или несколько Windows на экране для управления и обмениваются данными, он отображает и работает с. Ниже перечислены функции, основного окна.

1. Предоставляет область, в котором представления и элементы управления можно поместить и управлять.
2. Чтобы принимать и реагировать на события в ответ на взаимодействие пользователя с помощью клавиатуры и мыши.

Windows можно использовать в немодальное состоянии (например, текстовый редактор, который может иметь одновременно открыто несколько документов) или модальное (например, диалоговое окно экспорта, должно быть отброшено, прежде чем приложение может продолжить).

Панели — это особый вид окна (подкласс базового `NSWindow` класса), как правило, которые обслуживают вспомогательные функции в приложении, таких как служебная программа windows как инспекторы формат текста и системы палитра цветов.

[![](window-images/intro01.png "Окно в Xcode")](window-images/intro01.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с Windows и панели в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документов, здесь объясняется `Register` и `Export` команды используется для передачи классов C# в службе Objective-C объекты и элементы пользовательского интерфейса.

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Введение в Windows

Как уже говорилось выше, окно предоставляет область, в котором представления и элементы управления можно поместить и управлять и реагирует на события, на основе взаимодействия пользователя (либо с помощью клавиатуры или мыши).

В соответствии с Apple существует пять основных видов Windows в приложение macOS:

- **Окне документа** -окно документа содержит данные пользователя на основе файлов, такие как электронную таблицу или текстового документа.
- **Окно приложения** -окно приложения является главным окном приложения, не основанные на документе (например, приложение календаря на компьютере Mac).
- **Панель** - панели поверх других окон и предоставляет средства, или открыть элементы управления, которые пользователи могут работать с, а документов. В некоторых случаях может быть прозрачным панели (например, при работе с большой графики).
- **Диалоговое окно** -диалоговое окно отображается в ответ на действия пользователя и обычно представлен как пользователи могут выполнить действие. Диалоговое окно требует реакции от пользователя перед закрытием. (См. в разделе [работа с диалоговыми окнами](~/mac/user-interface/dialog.md))
- **Оповещения** -предупреждение — это особый тип диалогового окна, которое появляется при возникновении серьезной проблемы (например, ошибка) или как предупреждение (например, Подготовка к удалению файла). Поскольку диалоговое окно предупреждения, также требуется ответ от пользователя перед закрытием. (См. в разделе [работа с оповещениями](~/mac/user-interface/alert.md))

Дополнительные сведения см. в разделе [о Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main, ключ и неактивные Windows

Windows в приложении Xamarin.Mac можно выглядят и работают так, основанной на том, как пользователь в настоящее время взаимодействует с ними. Прежде всего документа или окна приложения, который используется в настоящий момент фокус внимания пользователя называется _главного окна_. В большинстве случаев это окно также будет _окно ключ_ (окно, которое в настоящее время принимает ввод данных пользователем). Но это не всегда так, например, средство выбора цвета может быть открыто и окон ключ, который пользователь взаимодействует с для изменения состояния элемента в окне документа (который по-прежнему будет главного окна).

Main и ключей Windows (если они существуют отдельно) всегда активны, _неактивного Windows_ являются открытых окон, которые не являются на переднем плане. Например, приложение текстового редактора может иметь более одного документа открыт одновременно, только для главного окна будет действительным, все остальные будут неактивны. 

Дополнительные сведения см. в разделе [о Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>Именование Windows

Окно может содержать строку заголовка и при этом название отображается, это обычно имя приложения, имя документа, в которой вы работаете или функции окна (например, инспектор). Некоторые приложения не отображать заголовок, так как они не распознаваемым методом видимости и не работать с документами.

Apple предлагает следующие рекомендации:

- Используйте его имя для заголовка окна основной, не является документом. 
- Назовите новое окно документа `untitled`. Для первого нового документа, не добавить номер заголовка (например, `untitled 1`). Если пользователь создает новый документ перед сохранением и титульные буквы первый, вызывать это окно `untitled 2`, `untitled 3`и т. д.

Дополнительные сведения см. в разделе [именования Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>Windows на весь экран

В macOS, окно приложения могут проходить во весь экран скрытие всего в том числе в строке меню приложения (который может быть отображена, наведя курсор в верхней части экрана) для предоставления отвлекаясь на бесплатный взаимодействие с ним содержимого.

Apple рекомендует придерживаться указанных ниже правил:

- Определите, имеет ли смысл для окна перейти во весь экран. Приложения, предоставляют краткое взаимодействия (например, калькулятор) не следует указывать полноэкранного режима.
- Отображение панели инструментов, если весь экран потребуется его. Обычно панель инструментов скрыта в полноэкранном режиме.
- Полноэкранного окна должны иметь все возможности для пользователей необходимо выполнить задачу.
- Если это возможно Избегайте взаимодействия с Finder, хотя пользователь находится в полноэкранном режиме окна.
- Воспользуйтесь преимуществами пространство на экране Повышенная без заниматься не основная задача.

Дополнительные сведения см. в разделе [весь экран Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>Панели

Панель — это вспомогательное окно, который содержит элементы управления и параметры, влияющие на активного документа или выбора (например, в системе палитра цветов):

[![](window-images/panel01.png "Панель цветов")](window-images/panel01.png#lightbox)

Панели может быть либо _конкретного приложения_ или _всей системы_. Панели конкретного приложения поверх верхней части окна документов приложения и исчезают, когда приложение находится в фоновом режиме. Панелей всей системы (такие как **шрифты** панели), число с плавающей запятой на основе всех открытых окон, независимо от того, приложение. 

Apple рекомендует придерживаться указанных ниже правил:

- Как правило, использовать стандартную панель, прозрачные панели должна использоваться только экономно и для насыщенных графикой задач.
- Рассмотрите возможность использования панели для предоставления пользователям удобный доступ к важные элементы управления или сведения, которые напрямую влияет на их задач.
- Скрытие и отображение панелей при необходимости.
- Панели всегда должен включать заголовок.
- Панелей не должно включать как кнопка свертывания active.

#### <a name="inspectors"></a>Инспекторы

Большинство современных приложений macOS представляют вспомогательные элементы управления и параметры, влияющие на активного документа или выбор в качестве _инспекторы_ , которые являются частью главного окна (например **страниц** приложения, показано ниже), Вместо использования Windows панели:

[![](window-images/panel02.png "Пример инспектора")](window-images/panel02.png#lightbox)

Дополнительные сведения см. в разделе [панелей](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1) раздел Apple [рекомендации по человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) и наши [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) образец приложения для является полной реализацией **Интерфейс инспектор** в приложении Xamarin.Mac.

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>Создание и обслуживание Windows в Xcode

При создании нового приложения Cocoa для Xamarin.Mac, вы получаете стандартное окно пустым, по умолчанию. В данном окне определяется в `.storyboard` файл, автоматически включаются в проект. Редактирование своего проекта windows, в **обозревателе решений**, дважды щелкните `Main.storyboard` файла:

[![](window-images/edit01.png "Выбрав основной раскадровки")](window-images/edit01.png#lightbox)

Он откроется окно конструктора Interface Builder в Xcode:

[![](window-images/edit02.png "Изменение пользовательского интерфейса в Xcode")](window-images/edit02.png#lightbox)

В **инспекторе атрибутов**, существует несколько свойств, которые можно использовать для определения и управления окна:

- **Заголовок** — это текст, отображаемый в строке заголовка окна.
- **Эта функция** -это _ключ_ , будет использоваться для идентификатор окна, когда положение и параметры его автоматически сохраняются.
- **Заголовок** -отображать строку заголовка окна.
- **Unified Title и панель инструментов** — Если окно включает в себя панель инструментов, он должен быть частью строку заголовка.
- **Полный размер представления содержимого** -позволяет области содержимого должно находиться в строке заголовка окна.
- **Тень** -окно имеет тень.
- **Текстурой** -Текстурированного windows можно использовать эффекты (например, естественность) и можно перемещать, перетаскивая ее на основного текста.
- **Закройте** -окно имеет кнопку «close».
- **Свести к минимуму** -окно имеет кнопку свертывания.
- **Изменение размера** -окно имеет изменить размер элемента управления.
- **Кнопка панели инструментов** -окно имеет кнопку панели инструментов Скрыть/Показать.
- **Которые могут быть восстановлены** -параметры конфигурации автоматически сохраняется и восстанавливается и положение окна.
- **Отображается в запуска** -окна автоматически отображается, когда `.xib` загружается файл.
- **Скрыть деактивировать** -окно скрывается, когда приложение входит в фоновом режиме.
- **Выпуск, если закрывается** -является окном, удаляются из памяти при его закрытии.
- **Всегда отображения подсказки** -будут постоянно отображаться подсказки.
- **Повторно вычисляет представление цикла** -является Просмотр заказа, пересчитываются перед выводом окна.
- **Пробелы**, **Несуразности** и **циклы** -определяют поведение окна в этих средах macOS. 
- **Полноэкранный режим** -определяет, если это окно можно ввести в полноэкранном режиме. 
- **Анимация** -определяет тип используемой анимации, доступные для окна.
- **Внешний вид** -определяет внешний вид окна. Сейчас есть только один внешний вид, голубой.

См. в разделе Apple [введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) и [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) документации для получения дополнительных сведений.

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>Параметр по умолчанию размер и расположение

Чтобы задать начальное положение окна и контролировать его размер, переключитесь в **инспектор размеров**:

[![](window-images/edit07.png "По умолчанию размер и расположение")](window-images/edit07.png#lightbox)

Здесь можно задать начальный размер окна, присвойте ему минимальный и максимальный размер, задать начальное расположение на экране и контролировать границы вокруг окна.

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>Установка контроллера пользовательских главное окно

Чтобы иметь возможность создать переменные экземпляров и действия, чтобы предоставить элементы пользовательского интерфейса в код C#, приложения Xamarin.Mac необходимо использовать настраиваемый контроллер окна.

Выполните следующие действия:

1. Откройте приложения раскадровку Interface Builder в Xcode.
2. Выберите `NSWindowController` в области конструктора.
3. Переключиться в режим **инспектор удостоверений** Просмотр и ввод `WindowController` как **имя класса**: 

    [![](window-images/windowcontroller01.png "Задание имени класса")](window-images/windowcontroller01.png#lightbox)
4. Сохранить изменения и вернуться в Visual Studio для Mac для синхронизации.
5. Объект `WindowController.cs` файл будет добавлен в проект в **обозревателе решений** в Visual Studio для Mac: 

    [![](window-images/windowcontroller02.png "Выбор контроллера windows")](window-images/windowcontroller02.png#lightbox)
6. Снова откройте в раскадровку Interface Builder в Xcode.
7. `WindowController.h` Файл будет доступен для использования: 

    [![](window-images/windowcontroller03.png "Редактирование файла WindowController.h")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>Добавление элементов пользовательского интерфейса

Чтобы определить содержимое окна, перетащите элементы управления из **инспектор библиотеки** на **редактор интерфейса**. См. в нашем [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) Дополнительные сведения об использовании конструктора Interface Builder для создания и включения элементов управления.

Например, давайте перетащим панель инструментов **инспектор библиотеки** на окно в **редактор интерфейса**:

[![](window-images/edit03.png "Выбрав на панели инструментов из библиотеки")](window-images/edit03.png#lightbox)

Затем перетащите **представления текста** баз данных и его для заполнения области под панелью инструментов:

[![](window-images/edit04.png "Добавление представления текста")](window-images/edit04.png#lightbox)

Поскольку мы хотим **представления текста** сжатия и увеличения при изменении размера окна, перейдем к **Редактор ограничений** и добавьте следующие ограничения:

[![](window-images/edit05.png "Изменение ограничения")](window-images/edit05.png#lightbox)

Щелкнув для **Red I-образных указателей** в верхней части редактора и выбрав **добавить 4 ограничения**, мы указываем придерживаться данного по осям X и Y координаты и расширения или сжатия по горизонтали и вертикали представления текста как размер окна.

И наконец, давайте предоставить **представления текста** для кода, используя **розетки** (и обязательно выберите `ViewController.h` файл):

[![](window-images/edit06.png "Настройка выхода")](window-images/edit06.png#lightbox)

Сохраните изменения и вернитесь в Visual Studio для Mac синхронизировать с Xcode.

Дополнительные сведения о работе с **розеток** и **действия**, см. в разделе наших [розетки и действие](~/mac/get-started/hello-mac.md#outlets-and-actions) документации.

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>Стандартное окно рабочего процесса

Для любого периода, обратитесь в приложении Xamarin.Mac процесс происходит по сути так же, как то, что мы только что выполнили выше.

1. Для новых окон, которые не используются по умолчанию, автоматически добавляется в проект добавьте в проект нового окна определения. Ниже подробно это будет описано.
2. Дважды щелкните `Main.storyboard` файл, чтобы открыть окно конструктора для редактирования в Interface Builder в Xcode.
3. Перетащите новое окно в пользовательский интерфейс конструктора и подключить окна главного окна с помощью _Segues_ (Дополнительные сведения см. в разделе [Segues](~/mac/platform/storyboards/indepth.md#Segues) части нашей [работа с раскадровками](~/mac/platform/storyboards/indepth.md) документации).
3. Задайте свойства требуется окно **инспекторе атрибутов** и **инспектор размеров**.
4. Перетащите в элементах управления, необходимые для создания собственного интерфейса и настроить их в **инспекторе атрибутов**.
5. Используйте **инспектор размеров** обрабатывать изменения размера для элементов пользовательского интерфейса.
6. Предоставляет элементы пользовательского интерфейса окна в код C# с помощью **розеток** и **действия**.
7. Сохраните изменения и вернитесь в Visual Studio для Mac синхронизировать с Xcode.

Теперь, когда у нас есть основные окна создан, мы рассмотрим типичных процессов Xamarin.Mac, приложение выполняет при работе с windows. 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>Отображение окна по умолчанию

По умолчанию новое приложение Xamarin.Mac автоматически отобразит окна, определенного в `MainWindow.xib` файл при его запуске:

[![](window-images/display01.png "Пример окна под управлением")](window-images/display01.png#lightbox)

Так как мы изменили структуру этого окна выше, теперь она включает панель инструментов по умолчанию и **представления текста** элемента управления. В следующем разделе в `Info.plist` файл отвечает за отображение этого окна:

[![](window-images/display00.png "Редактирование Info.plist")](window-images/display00.png#lightbox)

**Главный интерфейс** раскрывающийся список используется для выбора раскадровки, который будет использоваться в качестве основного пользовательского интерфейса приложения (в данном случае `Main.storyboard`).

Контроллер представления автоматически добавляется в проект для управления этой Windows Main, отображается (вместе с его первичное представление). Он определен в `ViewController.cs` файла и присоединяется к **владелец файла** в построителе интерфейса под **инспектор удостоверений**:

[![](window-images/display02.png "Задание владельца файла")](window-images/display02.png#lightbox)

Для нашего окна, хотелось бы, оно должно иметь название `untitled` при его открытии давайте переопределение `ViewWillAppear` метод в `ViewController.cs` чтобы выглядеть следующим образом:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> Мы указываем значение окна `Title` свойство в `ViewWillAppear` вместо метода `ViewDidLoad` метод потому, что хотя представления могут быть загружены в память, он не еще создан полностью. Если мы пытались получить доступ к `Title` свойство в `ViewDidLoad` метод, то получили бы `null` исключение, так как окно еще не был создан и привязав свойству еще.

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>Программное закрытие окна

Возможны ситуации, которые вы хотите программное закрытие окна в приложении Xamarin.Mac, кроме возможности использования, щелкнув окна **закрыть** кнопки или с помощью пункта меню. macOS предусмотрены два способа, чтобы закрыть `NSWindow` программным способом: `PerformClose` и `Close`.

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

Вызов `PerformClose` метод `NSWindow` имитирует нажатие окна пользователем **закрыть** рядом с кратковременно выделенной кнопкой "и затем закрыть окно.

Если приложение реализует `NSWindow` `WillClose` событие, он будет вызываться перед закрытием окна. Возвращает событие `false`, не будут закрыты окна. Если окно имеет **закрыть** кнопку или не могут быть закрыты для какой-либо причине ОС будет выдавать предупреждения звук.

Пример:

```csharp
MyWindow.PerformClose(this);
```

Будет пытаться закрыть `MyWindow` `NSWindow` экземпляра. Если она выполнена успешно, окно будет закрыто, иначе предупреждения звук, будет использовано и будет остается открытым.

<a name="Close" />

### <a name="close"></a>Закрыть

Вызов `Close` метод `NSWindow` не имитирует нажатие окна пользователем **закрыть** кнопку временно выделив кнопки, он просто закрывает окно.

Окно имеет должен отображаться будет закрыта и `NSWindowWillCloseNotification` уведомлений будет опубликовано в центре уведомлений по умолчанию для окна закрывается.

`Close` Метод отличается два важных отличия от `PerformClose` метод:

1. Он не пытается вызвать `WillClose` событий.
2. Он не моделирует пользователем при нажатии **закрыть** рядом с кратковременно выделенной кнопкой ".

Пример:

```csharp
MyWindow.Close();
```

Бы, чтобы закрыть `MyWindow` `NSWindow` экземпляра.

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>Содержимое измененного Windows

В macOS, Apple предоставила способ информирования пользователя, содержимое окна (`NSWindow`) была изменена пользователем и необходимо сохранить. Если это окно содержит измененное содержимое, небольшой черная точка будет отображаться в его **закрыть** мини-приложения:

[![](window-images/close01.png "Окно с измененного маркера")](window-images/close01.png#lightbox)

Если пользователь пытается закрыть окно или выйти из приложения Mac, хотя есть несохраненные изменения содержимого окна, должно представлять [диалоговое окно](~/mac/user-interface/dialog.md) или [модальное лист](~/mac/user-interface/dialog.md) и разрешает пользователю сохранять изменения Первый:

[![](window-images/close02.png "Сохранение таблицы стилей, отображаемая при закрытии окна")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>Пометка окно как измененный

Чтобы пометить окна по мере внесения изменений содержимого, используйте следующий код:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

И после сохранения изменений, очистка измененного флага с помощью:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>Сохранение изменений перед закрытием окна

Чтобы отслеживать пользователю закрытие окна и позволяет им сохранить измененное содержимое заранее, необходимо создать подкласс `NSWindowDelegate` и переопределите его `WindowShouldClose` метод. Пример:

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on resu;t
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

Чтобы подключить экземпляр этого делегата к окну, используйте следующий код:

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>Сохранение изменений перед закрытием приложения

Наконец приложение Xamarin.Mac должен проверить Если любой из его Windows содержат измененное содержимое и позволяют пользователю сохранить изменения перед выходом. Чтобы сделать это, измените вашей `AppDelegate.cs` файл, переопределить `ApplicationShouldTerminate` метода и это должно выглядеть следующим образом:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>Работа с несколькими Windows

Большинство приложений документа, основанного на Mac можно изменить несколько документов, в то же время. Например текстовый редактор может быть открыт для редактирования, в то же время несколько текстовых файлов. По умолчанию имеет новые приложения Xamarin.Mac **файл** меню с **New** элемента автоматически привязав к `newDocument:` **действие**.

Мы собираемся активировать этот новый элемент и разрешить пользователю открыть несколько копий наших главного окна, чтобы изменить сразу несколько документов.

Изменим наш `AppDelegate.cs` файл и добавьте следующие вычисляемые свойства:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

Мы будем использовать для отслеживания числа несохраненных файлов, поэтому мы можете отправить отзыв для пользователя (согласно правилам Apple, как описано выше).

Добавьте следующий метод:

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Этот код создает новую версию нашего окна контроллера, загружает новое окно, Main и ключ окна и задает его заголовок. Теперь, если мы запустите наше приложение и выберите **New** из **файл** меню будет открыт и отображается новое окно редактора:

[![](window-images/display04.png "Было добавлено новое окно без названия")](window-images/display04.png#lightbox)

Если мы откроем **Windows** меню, вы увидите приложение автоматически отслеживания и обработки наших открытых окон:

[![](window-images/display05.png "В меню widows")](window-images/display05.png#lightbox)

Дополнительные сведения о работе с меню в приложении Xamarin.Mac, см. в разделе наших [работа с меню](~/mac/user-interface/menu.md) документации.

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>Начало активного окна

В приложении Xamarin.Mac, которое можно открыть несколько окон (документы) бывают случаи, когда необходимо получить текущего, верхнем окне (окне ключа). Следующий код возвращает основные окна:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

Он может вызываться в любой класс или метод, которому требуется доступ к окну текущего, ключ. Если окно не открыт, он вернет `null`.

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>Доступ к все приложения Windows

Возможны ситуации, когда необходимо получить доступ ко всем windows, что в приложении Xamarin.Mac в настоящее время есть открытым. Например ли файл, который пользователь хочет, чтобы открыть еще открыт в окне с существующей.

`NSApplication.SharedApplication` Поддерживает `Windows` свойство, которое содержит массив всех открытых окон в приложении. Можно перебрать этот массив для доступа ко всем windows текущего приложения. Пример:

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

В примере кода выполняется приведение каждого возвращаемого окна с пользовательскими `ViewController` класс в наше приложение и проверка значения пользовательского `Path` свойства и путь к файлу, пользователю необходимо открыть. Если файл уже открыт, теперь мы представляем этого окна на передний план.

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>Настройка размера окна в коде

Бывают случаи, когда приложению требуется изменения размера окна в коде. Чтобы изменить размер и положение окна, его настроить в `Frame` свойство. При изменении размера окна, обычно требуется также настроить его происхождения, чтобы оставить окно в том же расположении, из-за macOS в системе координат.

В отличие от iOS, где (0,0) представляет собой верхний левый угол macOS использует математические систему координат где представляет (0,0) в левом нижнем углу экрана. В iOS координаты увеличиваться по мере перемещения вниз к справа. В macOS координаты возрастает значение вверх справа. 

Следующий пример кода изменяет размеры окна:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> При изменении windows размер и расположение в коде, необходимо убедиться, что учитывают минимального и максимального размеров, которые были заданы в конструктор Interface Builder. Это не будут учитываться автоматически, и можно будет сделать окно большего или меньшего размера, чем разрешено этими ограничениями.

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>Наблюдение за изменениями размера окна

Возможны ситуации, когда необходимо отслеживать изменения размера окна в приложении Xamarin.Mac. Например, для повторной отрисовки поверхности содержимого в соответствии с новым размером.

Чтобы отслеживать изменения размера, сначала убедитесь, что назначенные пользовательский класс контроллера окно конструктора Interface Builder. Например `MasterWindowController` в следующем:

[![](window-images/resize01.png "Инспектор удостоверений")](window-images/resize01.png#lightbox)

Далее следует изменить пользовательский класс контроллера окна и монитор `DidResize` событий в окне контроллера для получения уведомлений об изменениях динамической размер. Пример:

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

При необходимости можно использовать `DidEndLiveResize` только уведомления о событии после завершения изменения размера окна. Пример.

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>Настройка окна, заголовок и представлено в файл

При работе с windows, которые представляют документы, `NSWindow` имеет `DocumentEdited` свойство, если значение `true` отображается небольшой точка в «закрыть», чтобы дать пользователю указывает на то что файл был изменен и должны быть сохранены перед закрывающей.

Изменим наш `ViewController.cs` файл и внесите следующие изменения:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

Мы также отслеживаем `WillClose` события в окне и проверка состояния `DocumentEdited` свойство. Если это `true` нам нужно предоставить пользователю возможность сохранить изменения в файл. Если запустить наше приложение и введите любой текст, будет отображаться точка:

[![](window-images/file01.png "Измененные окна")](window-images/file01.png#lightbox)

Если мы попытаемся закрыть окно, мы получаем предупреждение:

[![](window-images/file02.png "Отображение сохранения диалоговое окно")](window-images/file02.png#lightbox)

Если мы загружаем документа из файла можно задать заголовок окна к файлу имя, используя `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` метод (учитывая, что `path` является строка, представляющая открываемого файла). Кроме того, можно задать URL-адрес файла с помощью `window.RepresentedUrl = url;` метод.

Если URL-адрес указывает на файл типа, известного среде операционной системы, его значок будет отображаться в заголовке окна. Если пользователь щелкнет правой кнопкой мыши значок, отображается путь к файлу.

Изменим то `AppDelegate.cs` файл и добавьте следующий метод:

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

Теперь запустим наше приложение, выберите **открыть...**  из **файл** меню, выберите текстовый файл из **откройте** диалогового окна поле и открыть его:

[![](window-images/file03.png "Открытое окно")](window-images/file03.png#lightbox)

Файл будет отображаться и заголовок будет устанавливаться со значком, для файла:

[![](window-images/file04.png "Загрузить содержимое файла")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>Добавление в проект новое окно

Помимо основным окном документа в приложении Xamarin.Mac может потребоваться отображать другие типы окон для пользователя, например адаптируются к предпочтениям или панели инспектора.

Чтобы добавить новое окно, сделайте следующее:

1. В **обозревателе решений**, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования в Interface Builder в Xcode.
2. Перетащите новый **контроллер окна** из **библиотеки** и поместите его на **конструктора**:

    [![](window-images/new01.png "Выбрав новый контроллер окна в библиотеке")](window-images/new01.png#lightbox)
3. В **инспектор удостоверений**, введите `PreferencesWindow` для **идентификатора раскадровки**: 

    [![](window-images/new02.png "Указание идентификатора раскадровки")](window-images/new02.png#lightbox)
5. Спроектировать свой интерфейс: 

    [![](window-images/new03.png "Проектирование пользовательского интерфейса")](window-images/new03.png#lightbox)
6. Откройте меню приложений (`MacWindows`) выберите **предпочтения...** , Щелкните и перетащите в новое окно: 

    [![](window-images/new05.png "Создание объекта перехода")](window-images/new05.png#lightbox)
7. Выберите **Показать** во всплывающем меню.
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Если мы запустите код и выберите **предпочтения...**  из **меню приложения**, откроется окно:

[![](window-images/new04.png "Меню настройки образца")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>Работа с панелями

Как уже говорилось в начале этой статьи, панели поверх других окон, а также средства или элементы управления, которые пользователи могут работать с, пока открыты документы. 

Так же, как и любой другой тип окна, создания и работы с в приложении Xamarin.Mac процесс по существу одинакова:

1. Добавьте в проект нового окна определения.
2. Дважды щелкните `.xib` файл, чтобы открыть окно конструктора для редактирования в Interface Builder в Xcode.
2. Задайте свойства требуется окно **инспекторе атрибутов** и **инспектор размеров**.
4. Перетащите в элементах управления, необходимые для создания собственного интерфейса и настроить их в **инспекторе атрибутов**.
5. Используйте **инспектор размеров** обрабатывать изменения размера для элементов пользовательского интерфейса.
6. Предоставляет элементы пользовательского интерфейса окна в код C# с помощью **розеток** и **действия**.
7. Сохраните изменения и вернитесь в Visual Studio для Mac синхронизировать с Xcode.

В **инспекторе атрибутов**, у вас есть следующие параметры, характерные для панелей:

[![](window-images/panel03.png "Инспектор атрибутов")](window-images/panel03.png#lightbox)

- **Стиль** -можно настроить стиль панели: регулярных панели (выглядит как стандартное окно), программа панели (имеет заголовок меньшего размера), HUD панели (прозрачная и заголовок является частью фона).
- **Активация не** -определяет в панели выведении окна на ключа.
- **Модальное окно документа** -Если документ модальным, панель будет только плавающей выше приложения windows, в противном случае отображается над всеми.


Чтобы добавить новую область, сделайте следующее:

1. В **обозревателе решений**, правой кнопкой мыши проект и выберите **добавить** > **новый файл...** .
2. В диалоговом окне нового файла выберите **Xamarin.Mac** > **окно Cocoa с контроллером**:

    [![](window-images/panels00.png "Добавление нового окна контроллера")](window-images/panels00.png#lightbox)
3. Введите в поле **Имя** значение `DocumentPanel` и нажмите кнопку **Новый**.
4. Дважды щелкните `DocumentPanel.xib` файл, чтобы открыть его для редактирования в конструкторе Interface Builder: 

    [![](window-images/new02.png "Изменение порта")](window-images/new02.png#lightbox)
5. Удалите существующее окно и перетащите панель из **инспектор библиотеки** в **редактор интерфейса**: 

    [![](window-images/panels01.png "Удаление существующего окна")](window-images/panels01.png#lightbox)
6. Подключить панели до **владелец файла** - **окно** - **розетки**: 

    [![](window-images/panels02.png "Перетаскивание пишем панели")](window-images/panels02.png#lightbox)
7. Переключиться в режим **инспектор удостоверений** и присвоить класс панели `DocumentPanel`: 

    [![](window-images/panels03.png "Класс панели параметров")](window-images/panels03.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.
7. Изменить `DocumentPanel.cs` и измените определение класса на следующее: 

    `public partial class DocumentPanel : NSPanel`
8. Сохраните изменения в файле.

Изменить `AppDelegate.cs` и создайте `DidFinishLaunching` внешний метод следующим образом:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

Если запустить наше приложение, будет отображаться панели:

[![](window-images/panels04.png "Панели в выполняющемся приложении")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Панель Windows стали нерекомендуемыми в Apple и должна быть заменена **интерфейсы инспекторов**. Полный пример создания **инспектор** в приложении Xamarin.Mac, см. в разделе наших [MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) примера приложения.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с Windows и панели в приложении Xamarin.Mac. Мы видели различные типы и использует Windows и панелей, как создавать и поддерживать Windows и панелей конструктора Interface Builder и способы работы с Windows и панелей в коде C#.

## <a name="related-links"></a>Связанные ссылки

- [MacWindows (пример)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector (пример)](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Работа с меню](~/mac/user-interface/menu.md)
- [Рекомендации по работе с человеческим интерфейсом OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Введение в Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
