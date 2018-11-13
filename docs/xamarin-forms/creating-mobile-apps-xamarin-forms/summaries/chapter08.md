---
title: Сводка Глава 8. Код и XAML в гармонии
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 8. Код и XAML в гармонии'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: cf757976be1a95fb7ed56ba6c014389fbe520408
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563580"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Сводка Глава 8. Код и XAML в гармонии

В этой главе рассматриваются XAML более глубоко, и особенно код и XAML взаимодействие.

## <a name="passing-arguments"></a>Передача аргументов

В общем случае класс, созданный в XAML должен иметь открытый конструктор без параметров; результирующий объект инициализируется с помощью свойства параметров. Тем не менее существует два других способа что объектов может быть создан и инициализирован.

Несмотря на то, что они являются методы общего назначения, они используются главным образом в MVVM Просмотр моделей.

### <a name="constructors-with-arguments"></a>Конструкторы с аргументами

[ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) образце показано, как использовать `x:Arguments` тег, чтобы задать аргументы конструктора. Эти аргументы должны быть заключены в теги элементов, указывающее тип аргумента. Для базовых типов данных .NET доступны следующие теги:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>Может вызывать методы из XAML

[ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) образце показано, как использовать `x:FactoryMethod` элемента, чтобы указать фабричный метод, который вызывается для создания объекта. Такой метод фабрики должен быть открытым и статическим, и его необходимо создать объект типа, в котором он определен. (Например [ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) определяет метод, так как он является открытым и статическим и возвращает значение типа `Color`.) Аргументы в метод фабрики, заданных в `x:Arguments` теги.

## <a name="the-xname-attribute"></a>Атрибут x: Name

`x:Name` Атрибут позволяет создавать экземпляр в XAML, чтобы присвоить имя объекта. Правила для этих имен не отличаются от имен переменных в C#. После возврата `InitializeComponent` вызовите в конструкторе, файл с выделенным кодом могут ссылаться на эти имена для доступа к соответствующему элементу XAML. Имена, фактически преобразуются средством синтаксического анализа XAML в закрытых полей в созданного разделяемого класса.

[ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) в нем демонстрируется использование `x:Name` файла кода для сохранения два `Label` элементы, определенные в XAML, обновлен с текущей датой и временем.

Тем же именем не может использоваться для нескольких элементов на одной странице. Это проблема при использовании `OnPlatform` создания параллельных именованные объекты для каждой платформы. [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) в нем демонстрируется более эффективный способ сделать нечто подобное.

## <a name="custom-xaml-based-views"></a>Пользовательские представления на основе XAML

Существует несколько способов избежать повтора разметки в XAML. Один из распространенных способов — создать новый класс на основе XAML, который является производным [ `ContentView` ](xref:Xamarin.Forms.ContentView). Этот подход продемонстрирован в [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) образца. `ColorView` Класс является производным от `ContentView` для отображения определенного цвета и его имя, тогда как `ColorViewListPage` класс является производным от `ContentPage` как обычно и явным образом создает 17 экземпляров `ColorView`.

Доступ к `ColorView` класса в XAML требуется другое объявление пространства имен XML, обычно называются `local` для классов в той же сборке.

## <a name="events-and-handlers"></a>События и обработчики

События могут быть назначены обработчиков событий в XAML, но сам обработчик событий должен быть реализован в файле кода. [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) демонстрирует способы создания пользовательского интерфейса клавиатуры в XAML и способ реализации `Clicked` обработчики в файле кода.

## <a name="tap-gestures"></a>Жесты касания

Любой `View` объекта можно получить сенсорный ввод и создавать события из этого входа. `View` Класс определяет [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) свойство коллекции, который может содержать один или несколько экземпляров классов, производных от [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer).

[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) Приводит к возникновению ошибки [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) события. [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) программы показано, как подключить `TapGestureRecognizer` объектов до четырех `BoxView` элементы должны быть созданы игру имитация:

[![Тройной снимок monkey tap](images/ch08fg07-small.png "игры имитации")](images/ch08fg07-large.png#lightbox "имитации игры")

Но **MonkeyTap** программа должна звук. (См. в разделе [следующей главе](chapter09.md).)

## <a name="related-links"></a>Связанные ссылки

- [Полный текст главе 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Примеры в главе 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Глава 8 F# образца](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Передача аргументов в XAML](~/xamarin-forms/xaml/passing-arguments.md)
