---
title: Привязка данных и ключ значение написание кода в Xamarin.Mac
description: В этой статье рассматривается, используя ключ значение кодирования и наблюдения, чтобы разрешить для привязки данных к элементам пользовательского интерфейса в конструктора Interface Builder ключ значение.
ms.prod: xamarin
ms.assetid: 72594395-0737-4894-8819-3E1802864BE7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 4a391160f2102fd1f069a45eb7c16aec91dfd7e0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110385"
---
# <a name="data-binding-and-key-value-coding-in-xamarinmac"></a>Привязка данных и ключ значение написание кода в Xamarin.Mac

_В этой статье рассматривается, используя ключ значение кодирования и наблюдения, чтобы разрешить для привязки данных к элементам пользовательского интерфейса в конструктора Interface Builder ключ значение._

## <a name="overview"></a>Обзор

При работе с C# и .NET в приложении Xamarin.Mac, у вас есть доступ к тем же кодирования ключ значение и методы привязки данных, работающий в *Objective-C* и *Xcode* does. Поскольку Xamarin.Mac напрямую интегрируется с Xcode, можно использовать конструктор _Interface Builder_ для привязки данных с элементами пользовательского интерфейса, вместо написания кода.

Используя ключ значение кодирования и привязка данных методов в приложении Xamarin.Mac, можно значительно сократить объем кода, который необходимо писать и поддерживать для заполнения и работать с элементами пользовательского интерфейса. Вы также можете использовать дальнейшего разделения данных резервного (_модель данных_) из вашего front end Interface пользователя (_Model-View-Controller_), что приведет к проще было обслуживать более гибкие приложения Структура.

[![Пример запущенного приложения](databinding-images/intro01.png "пример запущенного приложения")](databinding-images/intro01-large.png#lightbox)

В этой статье мы обсудим, что узнаете основы работы с кодирования ключ значение и привязка данных в приложении Xamarin.Mac. Настоятельно рекомендуется работать через [Привет, Mac](~/mac/get-started/hello-mac.md) статьи, во-первых, в частности [введение в Xcode и конструкторе Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) и [переменных экземпляров и действий](~/mac/get-started/hello-mac.md#outlets-and-actions) разделы, так как он рассматриваются основные понятия и методы, которые мы будем использовать в этой статье.

Может потребоваться взгляните на [предоставление C# классы / методы Objective-c](~/mac/internals/how-it-works.md) раздел [структуре Xamarin.Mac](~/mac/internals/how-it-works.md) документа, он объясняет `Register` и `Export` атрибуты используется для привязывания классов C# к объектам Objective-C и пользовательского интерфейса элементов.

<a name="What_is_Key-Value_Coding" />

## <a name="what-is-key-value-coding"></a>Что такое ключ значение кодирования

Ключ значение кодирования (KVC) — это механизм для доступа к свойствам объекта косвенно, с помощью ключей (специальный формат строки) для определения свойства вместо обращения к ним через переменные экземпляра или методы доступа (`get/set`). Путем реализации методов доступа кодирования совместимый ключ значение в приложении Xamarin.Mac, можно получить доступ к другим функциям macOS (прежнее название OS X), например наблюдения ключ значение (KVO), привязка данных, Core Data, Cocoa привязок и распространяя применимость сценариев.

Используя ключ значение кодирования и привязка данных методов в приложении Xamarin.Mac, можно значительно сократить объем кода, который необходимо писать и поддерживать для заполнения и работать с элементами пользовательского интерфейса. Вы также можете использовать дальнейшего разделения данных резервного (_модель данных_) из вашего front end Interface пользователя (_Model-View-Controller_), что приведет к проще было обслуживать более гибкие приложения Структура. 

Например рассмотрим следующее определение класса объекта KVC требованиям:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

Во-первых, `[Register("PersonModel")]` регистрирует класс атрибута и делает его уязвимым для Objective-C. Затем класс должен наследоваться от `NSObject` (или его подкласса, который наследует от `NSObject`), это добавляет несколько базовый метод, который позволяет классу быть KVC требованиям. Далее, `[Export("Name")]` атрибут предоставляет `Name` свойство и определяет значение ключа, которое будет использоваться позже для доступа к свойству KVC и KVO методами. 

И, наконец, чтобы иметь возможность быть наблюдаемая ключ-значение изменяется на значение свойства, метода доступа нужно упаковать внесение изменений в его значение `WillChangeValue` и `DidChangeValue` вызовы методов (указав один и тот же ключ как `Export` атрибут).  Пример:

```csharp
set {
    WillChangeValue ("Name");
    _name = value;
    DidChangeValue ("Name");
}
```

Этот шаг является _очень_ важно для связывания с данными в Xcode конструктора Interface Builder (как мы увидим далее в этой статье).

Дополнительные сведения см. в разделе Apple [ключ-значение кодирования руководство по программированию](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html).

### <a name="keys-and-key-paths"></a>Ключи и пути к ключам

Объект _ключ_ является строка, идентифицирующая конкретного свойства объекта. Как правило ключ соответствует имени метода доступа в кодирования совместимые объекта значения ключа. Ключи необходимо использовать кодировку ASCII, обычно начинаются с прописной буквы и не может содержать пробелы. Таким образом, заданному в примере выше, `Name` бы значение ключа для `Name` свойство `PersonModel` класса. Ключ и имя свойства, предоставляемые ими не имеют должны совпадать, однако в большинстве случаев они являются.

Объект _путь к ключу_ строка точки разделены ключи, используемые для указания иерархии для просмотра свойств объектов. Свойство первого ключа в последовательности относительно получателя, и каждый последующий ключ оценивается относительно значение предыдущего свойства. Таким же образом использовать точечную нотацию для обхода объекта и его свойства в классе C#.

Например, если вы развернули `PersonModel` класса и добавить `Child` свойство:

```csharp
using System;
using Foundation;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        private string _name = "";
        private PersonModel _child = new PersonModel();
        
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }
        
        [Export("Child")]
        public PersonModel Child {
            get { return _child; }
            set {
                WillChangeValue ("Child");
                _child = value;
                DidChangeValue ("Child");
            }
        }
        
        public PersonModel ()
        {
        }
    }
}
```

Путь к ключу имени ребенка будет `self.Child.Name` или просто `Child.Name` (с учетом как использовалось значение ключа).

### <a name="getting-values-using-key-value-coding"></a>Получение значений, с помощью кодировки ключ значение

`ValueForKey` Метод возвращает значение для указанного ключа (как `NSString`) по отношению к экземпляру класса KVC получения запроса. Например если `Person` является экземпляром класса `PersonModel` класс, определенный выше:

```csharp
// Read value 
var name = Person.ValueForKey (new NSString("Name"));
```

Это будет возвращать значение `Name` свойства для текущего экземпляра `PersonModel`. 

### <a name="setting-values-using-key-value-coding"></a>Значения параметров, с помощью кодировки ключ значение

Аналогичным образом `SetValueForKey` задайте значение для указанного ключа (как `NSString`) по отношению к экземпляру класса KVC получения запроса. И опять же, используя экземпляр `PersonModel` класса, как показано ниже:

```csharp
// Write value
Person.SetValueForKey(new NSString("Jane Doe"), new NSString("Name"));
```

Изменить значение `Name` свойства `Jane Doe`.

<a name="Observing_Value_Changes" />

### <a name="observing-value-changes"></a>Отслеживая изменения значения

Используя ключ значение наблюдения (KVO), можно прикрепить наблюдателя к определенного ключа соответствует класса KVC и получать уведомления при каждом изменении значения для этого ключа (с помощью методов KVC или прямой доступ к заданного свойства в коде C#). Пример:

```csharp
// Watch for the name value changing
Person.AddObserver ("Name", NSKeyValueObservingOptions.New, (sender) => {
    // Inform caller of selection change
    Console.WriteLine("New Name: {0}", Person.Name)
});
```

Теперь в любое время `Name` свойство `Person` экземпляр `PersonModel` класса изменяется, новое значение записывается в консоль. 

Дополнительные сведения см. в разделе Apple [введение ключ-значение наблюдения руководство по программированию](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html#//apple_ref/doc/uid/10000177i).

## <a name="data-binding"></a>привязка данных,

В следующих разделах будет показано, как использовать ключ значение кодирования и наблюдения совместимый класс ключ значение для привязки данных к элементам пользовательского интерфейса в конструктора Interface Builder, вместо чтения и записи значений, с помощью кода C#. Таким образом можно отделить вашей _модель данных_ из представлений, которые используются для их отображения, что делает приложение Xamarin.Mac, более гибкими и простыми для поддержания. Вы также значительно уменьшить объем кода, который должен быть написан.

<a name="Defining_your_Data_Model" />

### <a name="defining-your-data-model"></a>Определение модели данных

Прежде чем вы можете привязать элемент пользовательского интерфейса в конструктор Interface Builder, необходимо иметь совместимый класс KVC/KVO, заданных в приложении Xamarin.Mac в качестве _модель данных_ для привязки. Модель данных предоставляет все данные, которая будет отображаться в пользовательском интерфейсе, а также принимает данные, которые пользователь делает в пользовательском Интерфейсе во время выполнения приложения каких-либо изменений.

Например если вы создавали приложения, управляемые группы сотрудников, можно выполнить следующий класс для определения модели данных:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

Большинство возможностей этого класса охваченных [Какова кодирования ключ значение](#What_is_Key-Value_Coding) предыдущем разделе. Тем не менее, давайте рассмотрим несколько конкретных элементов и расширения, которые были сделаны данному классу действовать в качестве модели данных для **массива контроллеров устройств** и **контроллеров дерева** (который мы будем использовать позже для данных привязать **в древовидном представлении**, **представления структуры** и **представления коллекций**).

Во-первых, так как сотрудник может быть руководителем, мы использовали `NSArray` (в частности `NSMutableArray` , значения могут быть изменены) позволяет сотрудникам, которыми они должны быть присоединены к их:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
```

Необходимо отметить два момента:

1. Мы использовали `NSMutableArray` вместо стандартных C# массива или коллекции, так как это обязательное требование для привязки данных к элементам управления AppKit, такие как **представления таблиц**, **представления структуры** и **коллекций** .
2. Мы предоставляются массив сотрудников, приводя его к `NSArray` для данных привязки θ κΰζδϋι его C# имя в формате `People`, на такой, который ожидает привязки данных, `personModelArray` в форме **{class_name} массива**(Обратите внимание, что первый символ стала нижнего регистра).

Далее нам нужно добавить некоторые специально имя открытые методы для поддержки **массива контроллеров устройств** и **контроллеров дерева**:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Это позволяет на контроллерах, чтобы запрашивать и изменять данные, которые они отображаются. Например, предоставляемым `NSArray` выше, они имеют очень специфических соглашение об именовании (который отличается от обычной C# соглашения об именовании):

- `addObject:` — Добавляет объект в массив.
- `insertObject:in{class_name}ArrayAtIndex:` — Указывает Hive расположение `{class_name}` — это имя класса. Этот метод вставляет объект в массив по указанному индексу.
- `removeObjectFrom{class_name}ArrayAtIndex:` — Указывает Hive расположение `{class_name}` — это имя класса. Этот метод удаляет объект в массив по указанному индексу.
- `set{class_name}Array:` — Указывает Hive расположение `{class_name}` — это имя класса. Этот метод позволяет заменить существующие переноса на новый.

Внутри этих методов, мы заключил изменения в массив в `WillChangeValue` и `DidChangeValue` сообщений на соответствие KVO.

Наконец, с момента `Icon` свойство использует значение `isManager` изменения свойств, `isManager` свойство не может быть отражено в `Icon` для элементов пользовательского интерфейса привязки данных (во время KVO):

```csharp
[Export("Icon")]
public NSImage Icon {
    get {
        if (isManager) {
            return NSImage.ImageNamed ("group.png");
        } else {
            return NSImage.ImageNamed ("user.png");
        }
    }
}
``` 

Чтобы исправить это, мы используем следующий код:

```csharp
[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");
    }
}
```

Обратите внимание, что помимо свой собственный ключ `isManager` доступа также отправляет `WillChangeValue` и `DidChangeValue` сообщений для `Icon` ключа, он будет видеть изменения также.

Мы будем использовать `PersonModel` модели данных на протяжении оставшейся части этой статьи.

<a name="Simple_Data_Binding" />

### <a name="simple-data-binding"></a>Простая привязка данных

С нашей модели данных, определенной давайте рассмотрим простой пример привязки данных в Interface Builder в Xcode. Например, давайте добавим формы для нашего приложения Xamarin.Mac, который может использоваться для изменения `PersonModel` , определенным выше. Мы добавим несколько полей текста и типа "флажок" для отображения и изменения свойств нашей модели.

Во-первых давайте добавим новый **контроллер представления** для наших **Main.storyboard** в конструктор Interface Builder и назовите его класс `SimpleViewController`: 

[![Добавление нового контроллера представления](databinding-images/simple01.png "добавлении нового контроллера представления")](databinding-images/simple01-large.png#lightbox)

Затем вернитесь в Visual Studio для Mac, изменить **SimpleViewController.cs** файл (который был автоматически добавлен в наш проект) и предоставить экземпляр `PersonModel` что мы будем нести нашу форму для привязки данных. Добавьте следующий код:

```csharp
private PersonModel _person = new PersonModel();
...

[Export("Person")]
public PersonModel Person {
    get {return _person; }
    set {
        WillChangeValue ("Person");
        _person = value;
        DidChangeValue ("Person");
    }
}
```

Затем при загрузке представления, давайте создадим экземпляр нашей `PersonModel` и заполнить его следующим кодом:

```csharp
public override void ViewDidLoad ()
{
    base.AwakeFromNib ();

    // Set a default person
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    Person = Craig;

}
```

Теперь необходимо создать собственную форму, дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Макет форма будет выглядеть примерно следующим образом:

[![Изменение раскадровки в Xcode](databinding-images/simple02.png "редактирования раскадровки в Xcode")](databinding-images/simple02-large.png#lightbox)

Для привязки данных формы `PersonModel` , мы реализуются с помощью `Person` ключ, выполните следующие действия:

1. Выберите **имя сотрудника** текстовым полем и переключиться в режим **инспектор привязок**.
2. Проверьте **привязки к** выберите **простой контроллер представления** из раскрывающегося списка. Далее введите `self.Person.Name` для **путь к ключу**: 

    [![Ввод пути к ключу](databinding-images/simple03.png "ввода пути к ключу")](databinding-images/simple03-large.png#lightbox)
3. Выберите **род занятий** текстовое поле и проверьте **привязки к** выберите **простой контроллер представления** из раскрывающегося списка. Далее введите `self.Person.Occupation` для **путь к ключу**:  

    [![Ввод пути к ключу](databinding-images/simple04.png "ввода пути к ключу")](databinding-images/simple04-large.png#lightbox)
4. Выберите **сотрудник — менеджер** флажок и установите флажок **привязки к** выберите **простой контроллер представления** из раскрывающегося списка. Далее введите `self.Person.isManager` для **путь к ключу**:  

    [![Ввод пути к ключу](databinding-images/simple05.png "ввода пути к ключу")](databinding-images/simple05-large.png#lightbox)
5. Выберите **число сотрудников управляемых** текстовое поле и проверьте **привязки к** выберите **простой контроллер представления** из раскрывающегося списка. Далее введите `self.Person.NumberOfEmployees` для **путь к ключу**:  

    [![Ввод пути к ключу](databinding-images/simple06.png "ввода пути к ключу")](databinding-images/simple06-large.png#lightbox)
6. Если сотрудник не является диспетчером, нам нужно скрыть номер из сотрудников управляемых метку и текстовое поле.
7. Выберите **число сотрудников управляемых** метки, разверните **Hidden** стрелку вниз и установите флажок **привязки к** и выберите **простой контроллер представления** из раскрывающегося списка. Далее введите `self.Person.isManager` для **путь к ключу**:  

    [![Ввод пути к ключу](databinding-images/simple07.png "ввода пути к ключу")](databinding-images/simple07-large.png#lightbox)
8. Выберите `NSNegateBoolean` из **Transformer значение** раскрывающегося списка:  

    ![Выбор ключа преобразование NSNegateBoolean](databinding-images/simple08.png "выбрав преобразования ключа NSNegateBoolean")
9. Это означает, что привязка данных, метки будут скрыты, если значение `isManager` свойство `false`.
10. Повторите шаги 7 и 8 для **число сотрудников управляемых** текстовое поле.
11. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Если вы запустите приложение, значения из `Person` свойства будут заполнены автоматически нашу форму:

[![Отображение в форме автоматически заполняемая](databinding-images/simple09.png "отображение в форме автоматически заполняемая")](databinding-images/simple09-large.png#lightbox)

Любые изменения, сделанные пользователей к форме будут записаны обратно к `Person` свойства в контроллере представления. Например, отменив выбор **сотрудник — менеджер** обновления `Person` экземпляр наших `PersonModel` и **число сотрудников управляемых** метку и текстовое поле скрыты автоматически (с помощью Привязка данных):

[![Скрытие число сотрудников для от менеджеров](databinding-images/simple10.png "скрытие число сотрудников для менеджеров")](databinding-images/simple10-large.png#lightbox)

<a name="Table_View_Data_Binding" />

### <a name="table-view-data-binding"></a>Привязка данных в представлении таблицы

Теперь, когда у нас есть основные возможности привязки данных, давайте взглянем на более сложные задачи привязки данных с помощью _контроллер массива_ и привязка данных в табличное представление. Дополнительные сведения о работе с представлениями таблиц см. в разделе наших [представления таблиц](~/mac/user-interface/table-view.md) документации.

Во-первых давайте добавим новый **контроллер представления** для наших **Main.storyboard** в конструктор Interface Builder и назовите его класс `TableViewController`:

[![Добавление нового контроллера представления](databinding-images/table01.png "добавлении нового контроллера представления")](databinding-images/table01-large.png#lightbox)

Затем изменим **TableViewController.cs** файл (который был автоматически добавлен в наш проект) и предоставить массив (`NSArray`) из `PersonModel` классы, что мы будем нести нашу форму для привязки данных. Добавьте следующий код:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Как мы это делали на `PersonModel` класса выше в [определении модели данных](#Defining_your_Data_Model) разделе, мы предоставили четыре специально именованную открытые методы, чтобы контроллер массива и чтение и запись данных из нашей коллекции `PersonModels`.

Далее при загрузке представления, нам нужно заполнить нашего массива следующим кодом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Теперь нам нужно создать представление таблицы, дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Макет таблицы выглядит примерно следующим образом:

[![Размещение нового представления таблицы](databinding-images/table02.png "списочном новое представление таблицы")](databinding-images/table02-large.png#lightbox)

Нам нужно добавить **контроллер массива** для предоставления данных, привязанных к нашей таблице, сделайте следующее:

1. Перетащите **массива контроллера** из **инспектор библиотеки** на **редактор интерфейса**:  

    ![Выбор контроллера массива из библиотеки](databinding-images/table03.png "Выбор контроллера массива из библиотеки")
2. Выберите **контроллер массива** в **иерархия интерфейса** и переключиться в режим **инспекторе атрибутов**:  

    [![Выбрав инспекторе атрибутов](databinding-images/table04.png "выбрав инспекторе атрибутов")](databinding-images/table04-large.png#lightbox)
3. Введите `PersonModel` для **имя класса**, нажмите кнопку **, а также** и добавьте три ключа. Присвойте им названия `Name`, `Occupation` и `isManager`:  

    ![Добавление необходимых путей ключа](databinding-images/table05.png "Добавление необходимые пути к ключам")
4. Это означает, что контроллер массива что управляемых им массив, и какие свойства следует предоставлять доступ (через ключи).
5. Переключиться в режим **инспектор привязок** и в разделе **содержимого массива** выберите **привязки к** и **контроллер представления таблиц**. Введите **модели путь к ключу** из `self.personModelArray`:  

    ![Ввод пути к ключу](databinding-images/table06.png "ввода пути к ключу")
6. Этот способ привязывает контроллер массива к массиву `PersonModels` который мы предоставили в нашем контроллере представления.

Теперь необходимо привязать контроллер массива нашего представления таблицы, сделайте следующее:

1. Выберите вид таблицы и **привязки инспектор**:  

    [![Выбрав инспектор привязки](databinding-images/table07.png "выбрав инспектор привязки")](databinding-images/table07-large.png#lightbox)
2. В разделе **содержимое таблицы** стрелку вниз, выберите **привязки к** и **контроллер массива**. Введите `arrangedObjects` для **ключ контроллера** поля:  

    ![Определение ключа контроллера](databinding-images/table08.png "определение ключа контроллера")
3. Выберите **ячейка представления таблиц** под **сотрудника** столбца. В **инспектор привязок** под **значение** стрелку вниз, выберите **привязки к** и **представление ячейки таблицы**. Введите `objectValue.Name` для **модели путь к ключу**:  

    [![Задание пути к ключу модели](databinding-images/table09.png "задание пути к ключу модели")](databinding-images/table09-large.png#lightbox)
4. `objectValue` является текущим `PersonModel` в массиве, который управляется контроллером массива.
5. Выберите **ячейка представления таблиц** под **род занятий** столбца. В **инспектор привязок** под **значение** стрелку вниз, выберите **привязки к** и **представление ячейки таблицы**. Введите `objectValue.Occupation` для **модели путь к ключу**:  

    [![Задание пути к ключу модели](databinding-images/table10.png "задание пути к ключу модели")](databinding-images/table10-large.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Если запустить приложение, таблица будет заполнена с помощью наших массива `PersonModels`:

[![Запуск приложения](databinding-images/table11.png "запуск приложения")](databinding-images/table11-large.png#lightbox)

<a name="Outline_View_Data_Binding" />

### <a name="outline-view-data-binding"></a>Привязка данных в представлении структуры

привязки данных к структура очень похожа на привязку к представлению таблицы. Основное отличие заключается в том, что мы будем использовать **дерева контроллера** вместо **контроллер массива** для предоставления связанные данные для представления структуры. Дополнительные сведения о работе с представлениями структур см. в разделе наших [представления структуры](~/mac/user-interface/outline-view.md) документации.

Во-первых давайте добавим новый **контроллер представления** для наших **Main.storyboard** в конструктор Interface Builder и назовите его класс `OutlineViewController`: 

[![Добавление нового контроллера представления](databinding-images/outline01.png "добавлении нового контроллера представления")](databinding-images/outline01-large.png#lightbox)

Затем изменим **OutlineViewController.cs** файл (который был автоматически добавлен в наш проект) и предоставить массив (`NSArray`) из `PersonModel` классы, что мы будем нести нашу форму для привязки данных. Добавьте следующий код:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Как мы это делали на `PersonModel` класса выше в [определении модели данных](#Defining_your_Data_Model) раздел, мы предоставили четыре специально именованную открытые методы, чтобы контроллер дерева и чтение и запись данных из нашей коллекции `PersonModels`.

Далее при загрузке представления, нам нужно заполнить нашего массива следующим кодом:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    var Craig = new PersonModel ("Craig Dunn", "Documentation Manager");
    Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (Craig);

    var Larry = new PersonModel ("Larry O'Brien", "API Documentation Manager");
    Larry.AddPerson (new PersonModel ("Mike Norman", "API Documenter"));
    AddPerson (Larry);

}
```

Теперь нам нужно создать представление структуры, дважды щелкните **Main.storyboard** файл, чтобы открыть его для редактирования в конструкторе Interface Builder. Макет таблицы выглядит примерно следующим образом:

[![Создание представления структуры](databinding-images/outline02.png "Создание представления структуры")](databinding-images/outline02-large.png#lightbox)

Нам нужно добавить **дерева контроллера** чтобы обеспечить привязки данных для нашей структуры, выполните следующие действия:

1. Перетащите **дерева контроллера** из **инспектор библиотеки** на **редактор интерфейса**:  

    ![Выборе контроллера дерева из библиотеки](databinding-images/outline03.png "выборе контроллера дерева из библиотеки")
2. Выберите **дерева контроллера** в **иерархия интерфейса** и переключиться в режим **инспекторе атрибутов**:  

    [![Выбрав инспекторе атрибутов](databinding-images/outline04.png "выбрав инспекторе атрибутов")](databinding-images/outline04-large.png#lightbox)
3. Введите `PersonModel` для **имя класса**, нажмите кнопку **, а также** и добавьте три ключа. Присвойте им названия `Name`, `Occupation` и `isManager`:  

    ![Добавление необходимых путей ключа](databinding-images/outline05.png "Добавление необходимые пути к ключам")
4. Это означает, что контроллер дерева что управляемых им массив, и какие свойства следует предоставлять доступ (через ключи).
5. В разделе **дерева контроллера** введите `personModelArray` для **дочерние элементы**, введите `NumberOfEmployees` под **число** и введите `isEmployee` под  **Конечный**:  

    ![Задание путей ключа дерева контроллера](databinding-images/outline05.png "задание путей ключа дерева контроллера")
6. Это сообщает контроллеру дерева, где найти все дочерние узлы, сколько узлов дочерних и если текущий узел имеет дочерние узлы.
7. Переключиться в режим **инспектор привязок** и в разделе **содержимого массива** выберите **привязки к** и **владелец файла**. Введите **модели путь к ключу** из `self.personModelArray`:  

    ![Изменение пути к ключу](databinding-images/outline06.png "редактирования реестра")
8. Этот способ привязывает дерева контроллеру массив `PersonModels` который мы предоставили в нашем контроллере представления.

Теперь необходимо привязать нашего представления структуры дерева контроллер, сделайте следующее:

1. Выберите представление структуры и в **привязки инспектор** выберите:  

    [![Выбрав инспектор привязки](databinding-images/outline07.png "выбрав инспектор привязки")](databinding-images/outline07-large.png#lightbox)
2. В разделе **просмотреть содержимое структуры** стрелку вниз, выберите **привязки к** и **дерева контроллера**. Введите `arrangedObjects` для **ключ контроллера** поля:  

    ![Установка ключа контроллера](databinding-images/outline08.png "Настройка контроллера")
3. Выберите **ячейка представления таблиц** под **сотрудника** столбца. В **инспектор привязок** под **значение** стрелку вниз, выберите **привязки к** и **представление ячейки таблицы**. Введите `objectValue.Name` для **модели путь к ключу**:  

    [![Ввод пути к ключу модели](databinding-images/outline09.png "введя путь ключа модели")](databinding-images/outline09-large.png#lightbox)
4. `objectValue` является текущим `PersonModel` в массиве, который управляется контроллером дерева.
5. Выберите **ячейка представления таблиц** под **род занятий** столбца. В **инспектор привязок** под **значение** стрелку вниз, выберите **привязки к** и **представление ячейки таблицы**. Введите `objectValue.Occupation` для **модели путь к ключу**:  

    [![Ввод пути к ключу модели](databinding-images/outline10.png "введя путь ключа модели")](databinding-images/outline10-large.png#lightbox)
6. Сохранить изменения и вернуться в Visual Studio для Mac синхронизировать с Xcode.

Если запустить приложение, структура будет заполняться нашего массива из `PersonModels`:

[![Запуск приложения](databinding-images/outline11.png "запуск приложения")](databinding-images/outline11-large.png#lightbox)

### <a name="collection-view-data-binding"></a>Привязка данных для представления коллекции

Привязка данных с помощью представления коллекции очень сходно привязки представление таблицы, так как контроллера массива используется для предоставления данных для коллекции. Поскольку представление коллекции не имеет формат отображения предустановки, больше работы является обязательным для предоставления отзывов взаимодействия пользователя и для отслеживания выбора пользователя.

> [!IMPORTANT]
> Из-за проблемы в Xcode 7 и macOS 10.11 (и выше) представления коллекций не могут использоваться в файлах раскадровки (Storyboard). Таким образом необходимо будет продолжать использовать xib-файлов для определять свои представления коллекции для приложений Xamarin.Mac. См. в нашем [представления коллекций](~/mac/user-interface/collection-view.md) Дополнительные сведения см.

<!--KKM 012/16/2015 - Once Apple fixes the issue with Xcode and Collection Views in Storyboards, we can uncomment this section.

First, let's add a new **View Controller** to our **Main.storyboard** file in Interface Builder and name its class `CollectionViewController`: 

![](databinding-images/collection01.png)

Next, let's edit the **CollectionViewController.cs** file (that was automatically added to our project) and expose an array (`NSArray`) of `PersonModel` classes that we will be data binding our form to. Add the following code:

```csharp
private NSMutableArray _people = new NSMutableArray();
...

[Export("personModelArray")]
public NSArray People {
    get { return _people; }
}
...

[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    _people.Add (person);
    DidChangeValue ("personModelArray");
}

[Export("insertObject:inPersonModelArrayAtIndex:")]
public void InsertPerson(PersonModel person, nint index) {
    WillChangeValue ("personModelArray");
    _people.Insert (person, index);
    DidChangeValue ("personModelArray");
}

[Export("removeObjectFromPersonModelArrayAtIndex:")]
public void RemovePerson(nint index) {
    WillChangeValue ("personModelArray");
    _people.RemoveObject (index);
    DidChangeValue ("personModelArray");
}

[Export("setPersonModelArray:")]
public void SetPeople(NSMutableArray array) {
    WillChangeValue ("personModelArray");
    _people = array;
    DidChangeValue ("personModelArray");
}
```

Just like we did on the `PersonModel` class above in the [Defining your Data Model](#Defining_your_Data_Model) section, we've exposed four specially named public methods so that the Array Controller and read and write data from our collection of `PersonModels`.

Next when the View is loaded, we need to populate our array with this code:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Build list of employees
    AddPerson (new PersonModel ("Craig Dunn", "Documentation Manager", true));
    AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
    AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
    AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
    AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
    AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
    AddPerson (new PersonModel ("Larry O'Brien", "API Documentation Manager", true));
    AddPerson (new PersonModel ("Mike Norman", "API Documenter"));

}
```

Now we need to create our Collection View, double-click the **Main.storyboard** file to open it for editing in Interface Builder. Layout the Collection View to look something like the following:

![](databinding-images/collection02.png)

When you add a Collection View to a User Interface design, two extra elements are also added:

1.  **Collection View Item** -  That manages a single instance of an item in the collection.
2.  **View** - A custom view that provides the visual size and appearance of each item in the collection. This view is tied to and managed by the **Collection View Item**.

Select the view and make it look like the following using an Image View and two Text Fields:

![](databinding-images/collection03.png)

One thing to note here, a `NSBox` was added behind everything in the view with the following attributes:

![](databinding-images/collection04.png)

We'll be using this box to provide feedback to the user when an item is selected in the Collection View.

We need to add an **Array Controller** to provide bound data to our collection, do the following:

1. Drag an **Array Controller** from the **Library Inspector** onto the **Interface Editor**: <br/>![](databinding-images/table03.png)
2. Select **Array Controller** in the **Interface Hierarchy** and switch to the **Attribute Inspector**: <br/>![](databinding-images/collection05.png)
3. Enter `PersonModel` for the **Class Name**, click the **Plus** button and add four Keys. Name them `Icon`, `Name`, `Occupation` and `isManager`: <br/>![](databinding-images/table05.png)
4. This tells the Array Controller what it is managing an array of, and which properties it should expose (via Keys).
5. Switch to the **Bindings Inspector** and under **Content Array** select **Bind to** and **File's Owner**. Enter a **Model Key Path** of `self.personModelArray`: <br/>![](databinding-images/table06.png)
6. This ties the Array Controller to the array of `PersonModels` that we exposed on our View Controller.

Now we need to bind our Collection View to the Array Controller, do the following:

1. Select the Collection View and the **Binding Inspector**: <br/>![](databinding-images/collection06.png)
2. Under the **Contents** turndown, select **Bind to** and **Array Controller**. Enter `arrangedObjects` for the **Controller Key** field: <br/>![](databinding-images/collection07.png)
3. Under the **Selection Indexes** turndown, select **Bind to** and **Array Controller**. Enter `selectionIndexes` for the **Controller Key** field: <br/>![](databinding-images/collection08.png)
4. Select the **Image View**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Person** (the name of our Collection View Item). Enter `representedObject.Icon` for the **Model Key Path**: <br/>![](databinding-images/collection09.png)
5. `representedObject` is the current `PersonModel` in the array being managed by the Array Controller.
6. Select the first **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Name` for the **Model Key Path**: <br/>![](databinding-images/collection10.png)
7. Select the second **Label**. In the **Bindings Inspector** under the **Value** turndown, select **Bind to** and **Collection View Item**. Enter `representedObject.Occupation` for the **Model Key Path**: <br/>![](databinding-images/collection11.png)
8. Select the `NSBox`. In the **Bindings Inspector** under the **Hidden** turndown, select **Bind to** and **Collection View Item**. Enter `selected` for the **Model Key Path** and `NSNegateBoolean` for the **Value Transformer**: <br/>![](databinding-images/collection12.png)
9. Save your changes and return to Visual Studio for Mac to sync with Xcode.

If we run the application, the table will be populated with our array of `PersonModels`:

![](databinding-images/collection13.png)

For more information on working with Collection Views, please see our [Collection Views](~/mac/user-interface/collection-view.md) documentation.-->

## <a name="debugging-native-crashes"></a>Отладка сбой в машинном коде

Сделать ошибку в привязках данных может привести к _собственного аварийные_ в неуправляемый код и привести к неработоспособности с приложения Xamarin.Mac `SIGABRT` ошибка:

[![Пример диалогового окна сбоев в машинном коде](databinding-images/debug01.png "примером диалоговое окно сбоя в машинном коде")](databinding-images/debug01-large.png#lightbox)

Обычно существует четыре основных причины сбой в машинном коде во время привязки данных:

1. Модель данных не наследует от `NSObject` или подкласс `NSObject`.
2. Вы не предоставляет свойство с помощью Objective-C `[Export("key-name")]` атрибута.
3. Не удалось wrap внесение изменений в метод доступа значение `WillChangeValue` и `DidChangeValue` вызовы методов (указав один и тот же ключ как `Export` атрибут).
4. Имеется неправильный или неверный ключ **привязки инспектор** в конструктор Interface Builder.

### <a name="decoding-a-crash"></a>Декодирование сбоя

Давайте вызвать сбоя в машинном коде в нашей привязки данных, поэтому можно показано, как найти и устранить ее. В построителе интерфейса, давайте изменим наш привязки первой метки в пример представления коллекции из `Name` для `Title`:

[![Редактирование ключ привязки](databinding-images/debug02.png "редактирования ключ привязки")](databinding-images/debug02-large.png#lightbox)

Давайте сохранить изменение, вернитесь в Visual Studio для Mac для синхронизации с Xcode и запуска приложения. При отображении представления коллекции, приложение будет сразу же завершиться сбоем с ошибкой `SIGABRT` ошибки (как показано в **выходные данные приложения** в Visual Studio для Mac) с момента `PersonModel` не содержит свойство с ключом `Title`:

[![Пример ошибки привязки](databinding-images/debug03.png "пример ошибки привязки")](databinding-images/debug03-large.png#lightbox)

Если прокрутить для ошибки в самом верху **выходные данные приложения** мы видим, ключ к решению проблемы:

[![Поиск проблемы в журнале ошибок](databinding-images/debug04.png "поиск проблемы в журнале ошибок")](databinding-images/debug04-large.png#lightbox)

Эта строка сообщает, что ключ `Title` не существует в объекте, который выполняется привязка. Если изменить привязки обратно в `Name` в Interface Builder, сохранить, синхронизация, перестроить и запустить, приложение будет работать правильно без проблем.

## <a name="summary"></a>Сводка

В этой статье были предприняты никакие подробные принципы работы с привязкой данных и ключ значение написание кода в приложении Xamarin.Mac. Во-первых он рассмотрели предоставление класса C# для Objective-C, используя ключ значение кодирования (KVC) и ключ значение наблюдения (KVO). Затем он показал, как использовать класс соответствует KVO и его привязка данных к элементам пользовательского интерфейса в Interface Builder в Xcode. В заключение привязка сложных данных с помощью **массива контроллеров устройств** и **дерева контроллеров**.


## <a name="related-links"></a>Связанные ссылки

- [MacDatabinding раскадровки (пример)](https://developer.xamarin.com/samples/mac/MacDatabinding-Storyboard/)
- [Файлы Xib MacDatabinding (пример)](https://developer.xamarin.com/samples/mac/MacDatabinding-XIBs/)
- [Привет, Mac](~/mac/get-started/hello-mac.md)
- [Стандартные элементы управления](~/mac/user-interface/standard-controls.md)
- [Представления таблиц](~/mac/user-interface/table-view.md)
- [Представления структуры](~/mac/user-interface/outline-view.md)
- [Представления коллекций](~/mac/user-interface/collection-view.md)
- [Ключ значение для кодирования, руководство по программированию](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Введение в руководство по программированию на рассмотрение ключ значение](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)
- [Введение в разделы о программировании привязки Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Введение в Cocoa ссылки привязки](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Рекомендации по созданию пользовательских интерфейсов в macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
