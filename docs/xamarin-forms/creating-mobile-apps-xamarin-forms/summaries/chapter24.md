---
title: Сводка Глава 24. Переход по страницам
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 24. Переход по страницам'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: d90bef4da589215247f412450905a5db541b0444
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563164"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Сводка Глава 24. Переход по страницам

Многие приложения состоят из нескольких страниц, между которыми переходит пользователь. Приложение всегда имело *основной* страницы или *домашней* страницы, и из него пользователь переходит на другие страницы, которые поддерживаются в стек для навигации назад. Рассматриваются дополнительные возможности навигации [ **главе 25. Странице видов**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Модальные страницы и страницы немодального

`VisualElement` Определяет [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) свойство типа [ `INavigation` ](xref:Xamarin.Forms.INavigation), который включает в себя следующие два метода для перехода на новую страницу:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Оба метода принимают `Page` экземпляр в качестве аргументов и возвращаемых `Task` объекта. Следующие два метода перейдите обратно к предыдущей странице:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Если пользовательский интерфейс имеет свой собственный **обратно** кнопку (как и телефоны Android и Windows), то он не требуются для приложения для вызова этих методов.

Несмотря на то, что эти методы доступны из любого `VisualElement`, обычно они вызываются из `Navigation` текущего элемента `Page` экземпляра.

Приложения обычно используют модальные страницы, когда пользователь должен предоставить некоторые сведения на странице, прежде чем возвращать на предыдущую страницу. Страницы, которые не являются модальными иногда называются немодальные или *иерархические*. Ничего не на самой странице отличает его как модальное или немодальное; он управляется вместо этого метод, используемый для перехода к нему. Для работы на всех платформах, модальные страницы необходимо предоставить собственный пользовательский интерфейс для перехода к предыдущей странице.

[ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) пример позволяет увидеть разницу между страницы немодального и модальным. Любое приложение, использующее Навигация по страницам необходимо передать его домашней страницы для [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) конструктор, как правило, в ее `App` класса. Один премии: больше не нужно задать `Padding` страницы для iOS.

Можно узнать, что для немодального страницы, страницы [ `Title` ](xref:Xamarin.Forms.Page.Title) свойство отображается. IOS, Android и платформы планшетных и рабочий стол Windows укажите элемент пользовательского интерфейса для обратного перехода на предыдущую страницу. Курс, Android и Windows phone устройства имеют стандартный **обратно** кнопку, чтобы вернуться назад.

Модальные страницы, страницы `Title` не отображается, и чтобы вернуться к предыдущей странице предоставляется не элемент пользовательского интерфейса. Несмотря на то, что можно использовать стандартный Android и Windows phone **обратно** кнопку, чтобы вернуться на предыдущую страницу, модальные страницы на других платформах необходимо указать собственный механизм, чтобы вернуться назад.

### <a name="animated-page-transitions"></a>Анимированные переходы

Входящие в состав второго логический аргумент, который задается для других версий различные методы навигации `true` Если вы хотите включить анимацию перехода страницы:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

Тем не менее стандартный Навигация по страницам перечислены методы анимации по умолчанию, поэтому они полезны только для перехода к определенной страницы при запуске (как обсуждалось в конце данной главы) или при предоставлении собственных анимацию входа (как описано в [ **Chapter22. Анимация**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Визуальные и функциональные вариантов

`NavigationPage` включает в себя два свойства, которые можно задать при создании экземпляра класса в вашей `App` метод:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` также включает четыре вложенных свойства привязки, которые влияют на определенной страницы, на котором они заданы:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) и [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) и [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) и [ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) работают только на устройствах iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) и [ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) работа над только iOS и Android

### <a name="exploring-the-mechanics"></a>Изучение механизм

Методы навигации страницы асинхронны и должен использоваться с `await`. Завершение не указывает, что Навигация по страницам завершен, но только безопасность для просмотра стека навигации по страницам.

Когда одна страница переходит на другой, первой страницы обычно получает вызов, чтобы его [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) метод, а вторая страница получает вызов, чтобы его [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) метод. Аналогичным образом, при возврате одной страницы в другую первая страница получает вызов, чтобы его `OnDisappearing` метод, а вторая страница обычно получает вызов, чтобы его `OnAppearing` метод. Порядок этих вызовов (и завершения асинхронных методов, который вызывает переход) — зависит от платформы. Слово «обычно» в две предыдущие инструкции используется из-за Android модальное окно навигации, в котором не возникают эти вызовы методов.

Кроме того, вызовы `OnAppearing` и `OnDisappearing` методы не обязательно указывают Навигация по страницам.

`INavigation` Интерфейс включает в себя два свойства-коллекции, которые позволяют проверить стек навигации:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) типа `IReadOnlyList<Page>` для немодального стека
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) типа `IReadOnlyList<Page>` для модального стека

Лучше всего получить доступ к этих стеков из `Navigation` свойство `NavigationPage` (которое должно быть `App` класса [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) свойство). Безопасным является только для просмотра этих стеков после выполнения методов асинхронной Навигация по страницам. [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage) Свойство `NavigationPage` не указывает текущей страницы, если текущая страница является модальной страницы, но указывает вместо последней страницы немодального.

[ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) примера можно рассмотреть Навигация по страницам и стеки и допустимые типы средств переходов по страницам:

- Страницы немодального можно перейти к другой немодальные или модальные страницы
- Модальная страница поддерживает переход только к другой модальные страницы

### <a name="enforcing-modality"></a>Применение модальность

Приложение использует модальной страницы, когда это необходимо для получения некоторых сведений от пользователя. Пользователь должен быть запрещен от возврата на предыдущую страницу, пока не будет предоставлена эту информацию. В iOS, очень легко предоставить **обратно** кнопку и включить его только в том случае, когда пользователь закончил со страницей. Но для устройств Android и Windows phone, приложения должны переопределять [ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) метода и верните `true` Если программы должен быть обработан **обратно** кнопку, как показано в [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) образца.

[ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) образце показано, как это работает в сценарии MVVM.

## <a name="navigation-variations"></a>Варианты навигации

Если это был переход определенной модальные страницы может быть несколько раз, он должен сохранить сведения, таким образом, чтобы пользователь мог изменить данные, а не вводить его на еще раз. Это можно обрабатывать за счет сохранения определенным экземпляром модальные страницы, но сохраняет данные в модель представления более эффективный подход (особенно в iOS).

### <a name="making-a-navigation-menu"></a>Создание меню навигации

[ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) образце показано использование `TableView` к пунктам меню в списке. Каждый элемент связан с `Type` объекта для определенной страницы. При выборе этого элемента, программа создает экземпляр страницы и переходу на нее.

[![Тройной снимок экрана: тип представления коллекции](images/ch24fg21-small.png "пункты меню со списком TableView")](images/ch24fg21-large.png#lightbox "TableView листинг команды меню")

[ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) пример выглядит немного иначе, в том, что в меню содержатся экземпляры каждой страницы, а не типы. Это позволяет сохранять сведения о каждой странице, но все страницы должен быть создан при запуске программы.

### <a name="manipulating-the-navigation-stack"></a>Управление стек навигации

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) демонстрирует несколько функций, определяемых `INavigation` , которые позволяют манипулировать стек навигации в виде структурированных:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) и [ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) с необязательным анимации

### <a name="dynamic-page-generation"></a>Создание динамической страницы

[ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) образец демонстрирует, создав страницу во время выполнения на основе ввода пользователя.

## <a name="patterns-of-data-transfer"></a>Шаблоны передачи данных

Часто бывает необходим для передачи данных между страницами &mdash; для передачи данных на страницу перехода, а также для страницы, чтобы вернуться на страницу, которая его вызвала данных. Существует несколько методов для этого.

### <a name="constructor-arguments"></a>Аргументы конструктора

При переходе на новую страницу, можно создать экземпляр класса page с аргументом конструктора, позволяющий страницы, чтобы инициализировать самого себя. [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) в нем демонстрируется это. Можно также для перехода страницу, чтобы иметь его `BindingContext` задается на странице, который осуществляет переход к нему.

### <a name="properties-and-method-calls"></a>Свойства и вызовы методов

В остальных примерах передачи данных исследовать проблемы передачи сведений о страниц, когда одна страница переходит на другую страницу и обратно. В этих обсуждениях *домашней* страница переходит к *info* странице и необходимо передать инициализированный сведения для *info* страницы. *Info* страница получает Дополнительные сведения от пользователя и передает данные *домашней* страницы.

*Домашней* страницы можно легко получить доступ к открытые методы и свойства в *info* странице, как только он создает экземпляр этой страницы. *Info* страницы можно также получить доступ к открытые методы и свойства в *домашней* страницы, но выбрав подходящий момент для этого может быть непростой задачей. [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) пример делает это в его `OnDisappearing` переопределить. Один недостаток заключается в том, *info* страницы должен знать тип *домашней* страницы.

### <a name="messagingcenter"></a>Помощью MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) класс предоставляет еще один способ для двух страницах для взаимодействия друг с другом. Сообщения идентифицируются по текстовой строки и могут быть дополнены любого объекта.

Это программа, которая будет получать сообщений от определенного типа необходимо подписаться на них с помощью [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) и указать функцию обратного вызова. Позже его подписку можно отменить, вызвав [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). Функция обратного вызова получает все сообщения, отправленные из указанного типа с указанным именем, проходящие через [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) метод.

[ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) показано, как передавать данные с использованием центра обмена сообщениями, но еще раз, это требует *info* страницы известен тип *домашней* страницы.

### <a name="events"></a>События

Событие проверенные временем подход для одного класса отправлять сведения с другим классом, не зная тип этого класса. В [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) пример *info* класс определяет событие, которое он срабатывает, когда информация готова. Тем не менее, есть не удобное место для *домашней* страницу, чтобы отделить обработчик событий.

### <a name="the-app-class-intermediary"></a>Класс посредника приложения

[ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) примере показано, как получить доступ к свойствам, определенным в `App` классом оба *домашней* страницы и *info*страницы. Это является хорошим решением, но в следующем разделе описывается, что-нибудь получше.

### <a name="switching-to-a-viewmodel"></a>Переключение на модель представления

Использование ViewModel для сведения позволяют *домашней* страницы и *info* страницы для совместного использования экземпляра класса сведения. Это показано в [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) образца.

### <a name="saving-and-restoring-page-state"></a>Сохранение и восстановление состояния страницы

`App` Класс-посредник или ViewModel подход идеально подходит для приложения необходимо сохранить сведения, когда программа переходит в спящий режим при *info* страница активна. [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) в нем демонстрируется это.

## <a name="saving-and-restoring-the-navigation-stack"></a>Сохранение и восстановление стек навигации

В общем случае многостраничных программу, которая переходит в спящий режим должен перейти к той же странице, при восстановлении. Это означает, что такой программе следует сохранить содержимое стека навигации. В этом разделе показано, как автоматизировать этот процесс в классе специально для этой цели. Этот класс также вызывает отдельных страниц, чтобы они могли сохранять и восстанавливать их состояния страницы.

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека определяет интерфейс с именем [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) что классы могут реализовывать сохранять и восстанавливать элементы в `Properties`словаря.

[ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) В класс **Xamarin.FormsBook.Toolkit** библиотеки является производным от `Application`. Затем можно создавать производные вашей `App` класса из `MultiPageRestorableApp` и выполнять действия по обслуживанию.

[ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) демонстрируется использование `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Что-то наподобие приложения реальной жизни

[ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) образец также используется `MultiPageRestorableApp` и позволяет вводить и изменять заметок, которые сохраняются в `Properties` словаря.



## <a name="related-links"></a>Связанные ссылки

- [Глава 24 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Глава 24-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Иерархическая навигация](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Модальные страницы](~/xamarin-forms/app-fundamentals/navigation/modal.md)
