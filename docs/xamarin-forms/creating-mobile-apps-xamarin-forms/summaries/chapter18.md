---
title: Сводка Глава 18. MVVM
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 30fa06364e49294a71d37c29d4b9f1e95deac12f
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156929"
---
# <a name="summary-of-chapter-18-mvvm"></a>Сводка Глава 18. MVVM

Одним из лучших способов проектирования приложения является отделение пользовательского интерфейса от базового кода, который иногда называют *бизнес-логики*. Существует несколько методик, но тот, который предназначен для сред на основе XAML называется Model-View-ViewModel или MVVM.

## <a name="mvvm-interrelationships"></a>Взаимосвязи MVVM

Приложения на основе MVVM имеется три уровня:

- Эта модель обеспечивает базовые данные, иногда посредством файлов или обращается к web
- Это представление является пользователь интерфейс или с уровня представления, обычно реализуется в XAML
- ViewModel подключается модели и представления

Модель не представляет, ViewModel и ViewModel неведении представления. Эти три слоя обычно подключиться друг к другу с помощью указанных ниже:

![Представление, ViewModel и View](images/ch18fg03.png "MVVM")

В многих небольших программ (и даже более крупные) часто модели отсутствует или его функциональность интегрирована в ViewModel.

## <a name="viewmodels-and-data-binding"></a>Привязка данных и ViewModels

Чтобы принять участие в работе привязки данных, ViewModel должно поддерживать уведомления представления, при изменении свойства ViewModel. ViewModel делает это путем реализации [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) интерфейс `System.ComponentModel` пространства имен. Это является частью .NET, а не Xamarin.Forms. (Обычно ViewModels предпринимает попытки сохранить независимость от платформы.)

`INotifyPropertyChanged` Интерфейс объявляет одно событие с именем [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) указывает свойство, которое было изменено.

### <a name="a-viewmodel-clock"></a>Часы ViewModel

[ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) В [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) библиотека определяет свойство типа `DateTime` что изменения на основе таймера. Этот класс реализует `INotifyPropertyChanged` и запускает `PropertyChanged` событие каждый раз, когда `DateTime` изменения свойств.

[ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) пример создает экземпляр этого ViewModel и привязки данных для ViewModel отображает дату обновления и сведения о времени.

### <a name="interactive-properties-in-a-viewmodel"></a>Интерактивных свойств в ViewModel

Свойства в модели представления может быть более интерактивных и, как показано [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) класс, который является частью из [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) образца. Привязки данных укажите множитель и множитель значения из двух `Slider` элементы и отобразить продукт с `Label`. Тем не менее были внесены значительные изменения в этот пользовательский интерфейс в XAML без последующие изменения ViewModel или файл с выделенным кодом.

### <a name="a-color-viewmodel"></a>Цвет ViewModel

[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) В [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) библиотеки интегрирует модели цвета RGB и HSL. Рассматривается в [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) пример:

[![Тройной снимок TK](images/ch18fg08-small.png "модели цвета HSL")](images/ch18fg08-large.png#lightbox "модели цвета HSL")

### <a name="streamlining-the-viewmodel"></a>Упрощение ViewModel

Можно упростить код в ViewModels, определив `OnPropertyChanged` с помощью метода [ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) атрибут, который автоматически получает имя свойства вызывающего. [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) библиотеки для этого и предоставляет базовый класс для модели ViewModel.

## <a name="the-command-interface"></a>Интерфейс командной

MVVM работает с привязками данных, и привязок данных работать со свойствами, поэтому MVVM, по-видимому, будет объемов, когда дело доходит до обработки `Clicked` событие `Button` или `Tapped` событие `TapGestureRecognizer`. Чтобы разрешить ViewModel, чтобы обрабатывать такие события, Xamarin.Forms поддерживает *интерфейс командной*.

Интерфейс командной проявляется в `Button` с два открытых свойства:

- [`Command`](xref:Xamarin.Forms.Button.Command) типа [ `ICommand` ](xref:System.Windows.Input.ICommand) (определенные в `System.Windows.Input` пространства имен)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) типа `Object`

Для поддержки интерфейса команды, ViewModel необходимо определить свойство типа `ICommand` т.е. Затем данные, привязанные к `Command` свойство `Button`. `ICommand` Интерфейс объявляет два метода и одно событие:

- [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object)) Метода с аргументом типа `object`
- Объект [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) метода с аргументом типа `object` , возвращающий `bool`
- Объект [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged) событий

На внутреннем уровне ViewModel задает каждое свойство типа `ICommand` с экземпляром класса, реализующего `ICommand` интерфейс. Через привязку данных, `Button` первоначально вызываемые `CanExecute` метод и отключается, если метод возвращает `false`. Он также задает обработчик для `CanExecuteChanged` и вызывает метод `CanExecute` при каждом возникновении этого события. Если `Button` будет включен, он вызывает `Execute` метод всякий раз, когда `Button` нажатии.

Возможно, некоторые классы ViewModel, предшествуют Xamarin.Forms, и они уже поддерживают интерфейс командной. Для новой модели ViewModel предназначен для использования только с помощью Xamarin.Forms, Xamarin.Forms предоставляет [ `Command` ](xref:Xamarin.Forms.Command) класс и [ `Command<T>` ](xref:Xamarin.Forms.Command`1) класса, реализующие `ICommand` интерфейс. Универсальный тип является типом аргумента `Execute` и `CanExecute` методы.

### <a name="simple-method-executions"></a>Простой способ выполнения

[ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) образце показано, как использовать интерфейс командной в ViewModel. [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) Класс определяет два свойства типа `ICommand` и также определяет два частных свойств, которые он передает самый [ `Command` конструктор](xref:Xamarin.Forms.Command.%23ctor(System.Action)). Программа содержит привязки данных из этого ViewModel для `Command` свойства двух `Button` элементов.

`Button` Элементов можно легко заменить `TapGestureRecognizer` объектов в XAML без изменения кода.

### <a name="a-calculator-almost"></a>Калькулятор, почти

[ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) пример делает использование обеих `Execute` и `CanExecute` методы `ICommand`. Она использует [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) библиотеки. ViewModel содержит шесть свойств типа `ICommand`. Они инициализируются из [ `Command` конструктор](xref:Xamarin.Forms.Command.%23ctor(System.Action)) и [ `Command` конструктор](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) из `Command` и [ `Command<T>` конструктор](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/) из `Command<T>`. Числовые ключи арифмометр привязываются к свойству, которое инициализируется с помощью `Command<T>`и `string` аргумент `Execute` и `CanExecute` идентифицирует конкретный ключ.

## <a name="viewmodels-and-the-application-lifecycle"></a>Модели представлений и жизненного цикла приложения

`AdderViewModel` Используется в **AddingMachine** образец также определяет два метода с именем `SaveState` и `RestoreState`. Эти методы вызываются из приложения, когда он переходит в спящий режим, и при его запуске.



## <a name="related-links"></a>Связанные ссылки

- [Глава 18 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Глава 18 выборок](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Шаблоны корпоративного приложения с помощью Xamarin.Forms электронную книгу](~/xamarin-forms/enterprise-application-patterns/index.md)
