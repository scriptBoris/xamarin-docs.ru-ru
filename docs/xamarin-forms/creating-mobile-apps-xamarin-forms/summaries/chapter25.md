---
title: Сводка главе 25. Создание страницы
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка главе 25. Создание страницы'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 008c1b5eb30c3bae05d0d88b37fdef54be4066c5
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563008"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Сводка главе 25. Создание страницы

Теперь вы знаете двух классов, производных от `Page`: `ContentPage` и `NavigationPage`. Эта глава представляет два других:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) Управляет две страницы, главной и сведений
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) Управляет несколько дочерних страниц через вкладки

Эти типы страницы обеспечивают более сложные параметры навигации, чем `NavagationPage` подробно [Глава 24. Странице навигации](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Основные и подробные

[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) Определяет два свойства типа `Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) и [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail). Обычно каждое из этих свойств, чтобы задать `ContentPage`. `MasterDetailPage` Отображает и переключается между этим двум страницам.

Существуют два основных способа для переключения между этими двумя страницами:

- *разделить* где основные и подробные находятся рядом друг с другом
- *контекстном* где на страницу сведений о охватывает или частично рассматриваются главной странице

Существует несколько вариантов *контекстном* подход (*слайд*, *перекрываются*, и *замены*), но они обычно платформы зависимые. Можно задать [ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) свойство `MasterDetailPage` члену [ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior) перечисления:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

Тем не менее это свойство не влияет на телефонах. Телефоны всегда имеют контекстном поведение. Только для планшетных и настольных систем windows может иметь поведение разбиения.

### <a name="exploring-the-behaviors"></a>Изучая поведение

[ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) пример позволяет экспериментировать с поведением по умолчанию на разных устройствах. Программа содержит два отдельных `ContentPage` производные для основные и подробные (с `Title` задать для свойства), а другой класс, производный от `MasterDetailPage` , объединяет их. На страницу сведений о заключается в `NavigationPage` так, как программа UWP не будут работать без него.

На платформах Windows 8.1 и Windows Phone 8.1 требуют, что точечный рисунок присвоить `Icon` свойство главной страницы.

### <a name="back-to-school"></a>Снова в школу

[ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) пример занимает немного другой подход к построению программы для отображения учащихся из [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) библиотеки.

`Master` И `Detail` свойства определяются с помощью визуальных деревьев в [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) файл, который является производным от `MasterDetailPage`. Такой подход позволяет задать между основными и подробными страницами привязки данных.

Также устанавливает файл XAML [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) свойство `MasterDetailPage` для `True`. Это приводит к главной страницы для отображения при запуске; по умолчанию отображается страница сведений. [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) файле задает `IsPresented` для `false` при выборе элемента из `ListView` на главной странице. Затем открывается страница сведений:

[![Тройной снимок деталей и School](images/ch25fg09-small.png "страницу сведений из MasterDetailPage")](images/ch25fg09-large.png#lightbox "страницу сведений из MasterDetailPage")

### <a name="your-own-user-interface"></a>Собственного пользовательского интерфейса

Несмотря на то, что Xamarin.Forms предоставляет пользовательский интерфейс для переключения между представлениями основные и подробные, вы можете предоставить свои собственные. Для этого сделайте следующее:

- Задайте [ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) свойства `false` отключение проведение пальцем по экрану
- Переопределить [ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) метода и верните `false` для скрытия кнопки панели инструментов в Windows 8.1 и Windows Phone 8.1.

Затем необходимо предоставить средства для переключения между основными и подробными страницами, таким образом, как показано [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) образца.

[ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) образце показано использование другой подход `TapGestureRecognizer` на страницах основными и подробными.

## <a name="tabbedpage"></a>TabbedPage

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) — Это коллекция страниц, которые можно переключать с помощью вкладок. Он является производным от `MultiPage<Page>` и определяет не открытые свойства или методы свои собственные. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), тем не менее, определить свойство:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) свойство типа `IList<T>`

Заполните эту `Children` коллекции с объектами страницы.

Другой подход позволяет определять `TabbedPage` дочерние элементы во многом аналогично `ListView` с помощью этих двух свойств, которые автоматически создают страницам с вкладками:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) типа `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) типа `DataTemplate`

Тем не менее этот подход не работает на iOS, если коллекция содержит несколько элементов.

`MultiPage<T>` Определяет два дополнительные свойства, которые позволяют хранить список какая страница просматривается в настоящее время:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) типа `T`, ссылающегося на страницу
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) типа `Object`, ссылающегося на объект в `ItemsSource` коллекции

`MultiPage<T>` также определяет два события:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Когда `ItemsSource` изменения коллекции
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) При изменении просматриваемой страницы

### <a name="discrete-tab-pages"></a>Дискретные вкладок

[ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) Образец состоит из трех страниц с вкладками, которые отображают цвета тремя разными способами. Каждая вкладка предназначена `ContentPage` производный от него и затем `TabbedPage` производных [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) объединяет три страницы.

Для каждой страницы, отображаемой во `TabbedPage`, `Title` свойство необходимо указать текст на вкладке и Apple Store требует также использования значок поэтому `Icon` задано для iOS:

[![Тройной снимок дискретных цветов с вкладками](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

[ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) пример содержит домашнюю страницу, которая перечисляет всех учащихся. Учащийся шифрованию, вы перейдете в `TabbedPage` производный от него [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), который включает в себя три `ContentPage` объектов в визуальном дереве, один из которых позволяет ввести несколько заметок для этого учащегося.

### <a name="using-an-itemtemplate"></a>С помощью ItemTemplate

[ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) примере используется [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки. [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) файле задает `DataTemplate` свойство `TabbedPage` в визуальном дереве, начиная с `ContentPage` , содержащая привязки к свойствам `NamedColor` (включая привязку к `Title` свойство).

Однако это является проблемой для операций ввода-вывода. Можно отобразить только некоторые элементы, и нет нет хорошего способа предоставить им значки.



## <a name="related-links"></a>Связанные ссылки

- [Полный текст главе 25 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Примеры в главе 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Страница «основной-подробности»](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Страница с вкладками](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
