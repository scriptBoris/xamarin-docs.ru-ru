---
title: Создание приложения Xamarin.iOS с помощью API отражения
description: В этом документе описывается MonoTouch.Dialog основанное на атрибутах API отражения, который создает пользовательский Интерфейс на основе классов с атрибутами.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: bbc49e5f830a8711e5ead90fe02113e654d5da93
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107785"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Создание приложения Xamarin.iOS с помощью API отражения

Главный целевой сервер. API отражения D позволяет классам быть снабжен атрибутом атрибуты этого главного целевого сервера D использует автоматически создавать экраны. API-интерфейса отражения предоставляет привязку между этими классами и отображаемые на экране. Несмотря на то, что этот API не обеспечивает детального контроля, который выполняет элементы API, он уменьшает сложность, автоматически создав out элемент иерархию, основанную на классе оформления.

## <a name="setting-up-mtd"></a>Настройка главного целевого сервера D

ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D распространяется вместе с Xamarin.iOS. Чтобы использовать его, щелкните правой кнопкой мыши **ссылки** Xamarin.iOS узел проекта в Visual Studio 2017 или Visual Studio для Mac и добавьте ссылку на **MonoTouch.Dialog 1** сборки. Затем добавьте `using MonoTouch.Dialog` инструкций в источнике кода при необходимости.

## <a name="getting-started-with-the-reflection-api"></a>Приступая к работе с API отражения

С помощью API отражения осуществляется простым:

1.  Создание класса декорировать с помощью машинного перевода. Атрибуты D.
1.  Создание `BindingContext` экземпляра, передавая ему экземпляр приведенного выше класса. 
1.  Создание `DialogViewController` , передавая ему `BindingContext’s` `RootElement` . 


Давайте рассмотрим пример, чтобы продемонстрировать способы использования API отражения. В этом примере мы создадим отдельный экран ввода данных, как показано ниже:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "В этом примере мы создадим отдельный экран ввода данных следующим образом")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Создание класса с помощью машинного перевода. Атрибуты D

Первое, что нам нужно использовать API отражения — это класс, декорированные с помощью атрибутов. Эти атрибуты будут использоваться главного целевого сервера D внутренним образом для создания объектов из элементов API. Например рассмотрим следующее определение класса:

```csharp
public class Expense
{
        [Section("Expense Entry")]

        [Entry("Enter expense name")]
        public string Name;
        
        [Section("Expense Details")]
  
        [Caption("Description")]
        [Entry]
        public string Details;
        
        [Checkbox]
        public bool IsApproved = true;
}
```

`SectionAttribute` Приведет к части `UITableView` создается с строковый аргумент, используемый для заполнения раздела заголовка. После объявления раздела каждого поля, что следующий за ним будут включены в этом разделе, пока не объявлен другой раздел.
Тип элемента пользовательского интерфейса, созданные для поля будут зависеть от типа поля и главный целевой сервер. Атрибут типа D, дополнив его.

Например `Name` поле является `string` и он снабжен `EntryAttribute`. Это приводит к строке, добавляемого в таблицу с указанным заголовком и поле ввода текста. Аналогичным образом `IsApproved` поле является `bool` с `CheckboxAttribute`, полученный в строке таблицы с флажком в правом углу ячейки таблицы. ГЛАВНОГО ЦЕЛЕВОГО СЕРВЕРА D использует имя этого поля, автоматически добавит пробел, чтобы создать заголовок таким образом, так как он не указан в атрибуте.

## <a name="adding-the-bindingcontext"></a>Добавление контекста BindingContext

Чтобы использовать `Expense` класса, необходимо создать `BindingContext`. Объект `BindingContext` является классом, который будет привязывать данные из класса с атрибутом для создания иерархии элементов. Чтобы создать его, мы просто создать его экземпляр и передать конструктору экземпляра класса с атрибутом.

Например, чтобы добавить пользовательский Интерфейс, который объявлен с помощью атрибута в `Expense` класса, включите следующий код в `FinishedLaunching` метод `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Затем все что нужно сделать для создания пользовательского интерфейса — это добавить `BindingContext` для `DialogViewController` и задать его в качестве `RootViewController` окна, как показано ниже:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
   
        window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        var expense = new Expense ();
        var bctx = new BindingContext (null, expense, "Create a task");
        var dvc = new DialogViewController (bctx.Root);
            
        window.RootViewController = dvc;
        window.MakeKeyAndVisible ();
            
        return true;
}
```

Запуск приложения теперь приводит к экран, показанный выше отображение.

### <a name="adding-a-uinavigationcontroller"></a>Добавление UINavigationController

Тем не менее Обратите внимание, что заголовок «Создание задачи», мы передали `BindingContext` не отображается. Это обусловлено `DialogViewController` — не является частью `UINavigatonController`. Давайте изменим код для добавления `UINavigationController` как окна `RootViewController,` и добавьте `DialogViewController` как корень `UINavigationController` как показано ниже:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Теперь при запуске приложения, это название появится в `UINavigationController’s` панели навигации как снимке экрана ниже показано:

 [![](reflection-api-walkthrough-images/02-create-task.png "Теперь при запуске приложения, в заголовке отображается на панели навигации UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Включив `UINavigationController`, мы теперь можно использовать другие преимущества машинного перевода. D, для которого необходим навигации. Например, мы можем добавить перечисление для `Expense` класс определяет категорию затрат и главного целевого сервера D будет автоматически создавать экран выбора. Чтобы продемонстрировать, измените `Expense` класса для включения `ExpenseCategory` поля следующим образом:

```csharp
public enum Category
{
        Travel,
        Lodging,
        Books
}
        
public class Expense
{
        …

        [Caption("Category")]
        public Category ExpenseCategory;
}
```

Запуск приложения теперь приводит строку в таблице для категории, как показано:

 [![](reflection-api-walkthrough-images/03-set-details.png "Запуск приложения теперь приводит строку в таблице для категории, как показано")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Выбрав строку приводит приложения выполнен переход на новый экран со строками, соответствующими перечисления, как показано ниже:

 [![](reflection-api-walkthrough-images/04-set-category.png "Выбор строки приводит приложения выполнен переход на новый экран со строками, соответствующими перечисления")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Сводка

В этой статье представлено пошаговое руководство по API отражения. Мы показали, как добавить атрибуты к классу для управления тем, что отображается. Мы также рассказал пока о использовании `BindingContext` привязывать данные из класса в иерархии элемента, создается, а также способы использования машинного перевода. D с `UINavigationController`.


## <a name="related-links"></a>Связанные ссылки

- [MTDReflectionWalkthrough (пример)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Презентация - Мигель de Icaza создает на экран входа iOS с MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Презентация - легко создавать пользовательские интерфейсы iOS с помощью MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Общие сведения о диалоговом окне MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Пошаговое руководство по API элементов](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Пошаговое руководство элемент JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Диалоговое окно MonoTouch на Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation приложения](https://github.com/migueldeicaza/TweetStation)
- [Ссылки на класс UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Ссылки на класс UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
