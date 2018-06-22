---
title: Пользовательские элементы управления в конструкторе Xamarin для iOS
description: Конструктор Xamarin для операций ввода-вывода поддерживает отрисовки пользовательских элементов управления в проекте создаются или ссылки из внешних источников, таких как хранилище компонентов Xamarin.
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 113fab2fd0d1a055d566606885cefbafe3185529
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30782188"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Пользовательские элементы управления в конструкторе Xamarin для iOS

_Конструктор Xamarin для операций ввода-вывода поддерживает отрисовки пользовательских элементов управления в проекте создаются или ссылки из внешних источников, таких как хранилище компонентов Xamarin._

В конструкторе Xamarin iOS является мощным средством для визуализации пользовательского интерфейса приложения и предоставляет WYSIWYG поддержка для большинства операций ввода-вывода представлений и контроллеров представления редактирования. Приложение также может содержать пользовательские элементы управления, которые расширяют тех, которые встроены в iOS. Если эти элементы управления записываются с несколько рекомендаций, помните, они также может осуществляться по iOS конструктора, обеспечивая более широких возможностей редактирования. В этом документе рассматривается этим рекомендациям.

## <a name="requirements"></a>Требования

В области конструктора отображается элемент управления, который соответствует следующим требованиям:

1.  Она является подклассом прямых или косвенных [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) или [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller). Другие [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) подклассов будут отображаться в виде значка в области конструктора.
2.  Он имеет [RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) для представления этого цель-C.
3.  Он имеет [необходимый конструктор IntPtr](~/ios/internals/api-design/index.md).
4.  Либо реализует [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) интерфейс или имеет [DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/) присвоено значение True.

Элементы управления, определенные в коде, отвечающих требованиям выше будет отображаться в конструкторе при их содержащий проект компилируется для симулятора. По умолчанию все пользовательские элементы управления будут отображаться в **пользовательские компоненты** раздел **элементов**. Однако [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/) может применяться класс пользовательского элемента управления, чтобы указать другой раздел.

Конструктор не поддерживает загрузки Objective-C сторонних библиотек.

## <a name="custom-properties"></a>Настраиваемые свойства

Свойство, объявленное в пользовательский элемент управления будет отображаться на панели «свойства», если выполняются следующие условия:

1.  Свойство имеет общедоступного метода считывания и метод задания.
1.  Имеет свойство [ExportAttribute](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) , а также [BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/) присвоено значение True.
1.  Тип свойства является числовой тип, тип перечисления, string, bool, [SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/), [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/), или [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/). Этот список поддерживаемых типов может расширяться в будущем.


Свойство также может быть оформлен [DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/) для задания метки, отображаемый для его на панели «Свойства».

## <a name="initialization"></a>Инициализация

Для `UIViewController` подклассов, следует использовать [ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/) метод для кода, который зависит от представления, созданные в конструкторе.

Для `UIView` и других `NSObject` подклассов, [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/) метод является рекомендуемым местом для инициализации пользовательского элемента управления, после ее загрузки из файла разметки. Это, поскольку все пользовательские свойства, заданные на панели «свойства» не будет установлен при запуске конструктора элемента управления, но они будут устанавливаться до `AwakeFromNib` вызывается:


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

Если элемент управления предназначен также должен быть создан напрямую из кода, можно создать метод, который содержит общий код инициализации, следующим образом:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>Свойства инициализации и AwakeFromNib

На время и место для инициализации конструируемого свойств в пользовательский компонент, а не перезаписывать значения, которые были заданы в конструкторе iOS следует соблюдать осторожность. В качестве примера рассмотрим следующий код:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {
    
    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

`CustomView` Компонент предоставляет доступ к `Counter` свойство, которое можно задать разработчиком внутри конструктора iOS. Тем не менее, независимо от того, какое значение установлено в конструкторе значение `Counter` свойства всегда будет нуль (0). Далее описывается, почему это происходит:

-  Экземпляр `CustomControl` завышенными из файла раскадровки.
-  Устанавливаются свойства, измененные в конструкторе операций ввода-вывода (например, если значением `Counter` к двум (2), например).
-  `AwakeFromNib` Метод выполняется, и вызов компонента `Initialize` метод.
-  Внутри `Initialize` значение `Counter` свойства сброшено в ноль (0).


Для исправления ситуации выше, либо инициализировать `Counter` свойство в другом месте (например, в конструкторе компонента) или не переопределить `AwakeFromNib` метод и вызовите `Initialize` Если компонента требуется дополнительная инициализация не за пределами что в настоящее время обрабатываются его конструкторов.

## <a name="design-mode"></a>В режиме конструктора

В области конструктора пользовательского элемента управления необходимо соблюдать несколько ограничений:

-  Ресурсы пакета приложения недоступны в режиме конструктора. Доступны при загрузке через [UIImage методы](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM) .
-  Асинхронные операции, такие как веб-запросов не должен выполняться в режиме конструктора. Область конструктора не поддерживает анимацию или других асинхронных обновлений для элемента управления пользовательского интерфейса.


Можно реализовать пользовательский элемент управления [IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/) и использовать [DesignMode](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/) свойство, чтобы определить его в рабочей области конструирования. В этом примере будет отображена надпись «Режим разработки» в рабочей области конструктора «Runtime» во время выполнения:

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

Всегда следует проверять `Site` свойство `null` для получения доступа к любой из его элементов. Если `Site` — `null`, безопасно предположить, элемент управления не работает в конструкторе.
В режиме конструктора `Site` будет установлено после запуска конструктора элемента управления и перед `AwakeFromNib` вызывается.

## <a name="debugging"></a>Отладка

Элемент управления, который соответствует требованиям выше отображаются на панели элементов и отображаемых в рабочей области.
Если элемент управления не отображается, проверьте наличие ошибок в элемент управления или один из ее зависимостей.

Область конструктора часто может перехватывать исключения отдельных элементах управления продолжая для отображения других элементов управления. Красный заполнителя заменяется повреждения элемента управления и Трассировка исключения можно просмотреть, щелкнув значок с восклицательным знаком:

 ![](ios-designable-controls-overview-images/exception-box.png "Ошибочный элемент управления как заполнитель красный и сведения об исключении")

Если символы отладки доступны для элемента управления, трассировка будет иметь имена файлов и номеров строк. Дважды щелкнув линию в трассировке стека будет выполнен переход к соответствующей строке в исходном коде.

Если конструктор не удается изолировать повреждения элемента управления, в верхней части рабочей области конструктора появится предупреждающее сообщение:

 ![](ios-designable-controls-overview-images/info-bar.png "Предупреждающее сообщение в верхней части рабочей области конструктора")

Полный отрисовки будет возобновлена после повреждения элемента управления исправлена или удалена из рабочей области конструирования.

## <a name="summary"></a>Сводка

Создание и применение пользовательских элементов управления в конструкторе операций ввода-вывода, представленные в этой статье. Сначала он описываются требования, которым должны соответствовать элементы управления для отображения в рабочей области конструирования и предоставляют пользовательские свойства на панели «Свойства». Затем он рассматривали кода программной части - инициализации элемента управления и свойство DesignMode. Наконец он описано, что произойдет, если исключения и способы решения этой проблемы.