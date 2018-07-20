---
title: Сводка Глава 23. Триггеры и поведения
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 23. Триггеры и поведения'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 83a445555f9f184f735c105370de20665ad704a3
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156760"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Сводка Глава 23. Триггеры и поведения

Триггеры и расширения функциональности похожи, в том, что оба они предназначены для использования в файлах XAML для упрощения взаимодействия элемент в дополнение к использование привязок данных и для расширения функциональности элементов XAML. С объектами пользовательского интерфейса visual почти всегда используются триггеры и поведения.

Для поддержки триггеры и поведений, оба `VisualElement` и `Style` поддерживают два свойства-коллекции:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) и [ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers) типа `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) и [ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors) типа `IList<Behavior>`

## <a name="triggers"></a>Триггеры

Триггер — это условие (изменение свойства или порождение события), приведет к появлению ответа (другой изменение свойства или выполнением кода). `Triggers` Свойство `VisualElement` и `Style` имеет тип `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) является абстрактным классом, порождающим четыре запечатанных классов:

- [`Trigger`](xref:Xamarin.Forms.Trigger) для ответов на основе изменений свойств
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) для ответов, в зависимости от обработки событий
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) для ответов, в зависимости от привязки данных
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) для ответов на основе нескольких триггеров

Триггер всегда имеет значение для элемента, свойство которого изменяется с помощью триггера.

### <a name="the-simplest-trigger"></a>Самый простой триггер

[ `Trigger` ](xref:Xamarin.Forms.Trigger) Класс используется для проверки изменения значения свойства и отвечает, устанавливая другому свойству этого же элемента.

`Trigger` определяет три свойства:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) типа `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) типа `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) типа `IList<SetterBase>`, свойство содержимого `Trigger`

Кроме того `Trigger` требует, что следующее свойство наследуется от `TriggerBase` задать:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) для указания типа элемента, на котором `Trigger` подключен

`Property` И `Value` составляют условие и `Setters` коллекции представляет собой ответ. Если указанный `Property` имеет значением, полученным путем `Value`, то `Setter` объекты в `Setters` коллекции применяются. Когда `Property` имеет другое значение, методы задания будут удалены. `Setter` Определяет два свойства, так же, как первые два свойства `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) типа `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) типа `Object`

[ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) образце показано, как `Trigger` применяется к `Entry` можно увеличить размер `Entry` через `Scale` свойство при `IsFocused`свойство `Entry` является `true`.

Несмотря на то, что он не является типичным, `Trigger` можно задать в коде, как [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) в нем демонстрируется.

[ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) образце показано, как `Trigger` можно задать в `Style` для применения к нескольким `Entry` элементов.

### <a name="trigger-actions-and-animations"></a>Действия триггера и анимации

Это также можно выполнить немного кода, в зависимости от триггера. Этот код может быть анимацию, которая обращается свойству. Один из способов является использование [ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger), который определяет два свойства:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) типа `string`, имя события
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) типа `IList<TriggerAction>`, список действий, которое запускается в ответ.

Для этого необходимо написать класс, производный от [ `TriggerAction<T>` ](xref:Xamarin.Forms.TriggerAction`1), обычно `TriggerAction<VisualElement>`. В этом классе можно определить свойства. Ниже приведены обычные свойства CLR, а не свойства связывания, так как `TriggerAction` не является производным от `BindableObject`. Необходимо переопределить [ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*) метод, вызываемый при вызове действия. Аргумент является целевой элемент.

[ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки является примером. Он вызывает `ScaleTo` анимируемое свойство `Scale` свойства элемента. Так как одна из его свойств типа `Easing`, [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) класс позволяет использовать стандартные `Easing` статических полей в XAML.

[ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) образец демонстрирует способ вызова `ScaleAction` из `EventTrigger` объектов, которое ведет наблюдение за `Focused` и `Unfocused` события.

[ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) примере показано, как определить пользовательскую функцию плавности для `ScaleAction` в файл с выделенным кодом.

Вы также можете вызвать действия с помощью `Trigger` (distinguished из `EventTrigger`). Это требует, что вам известно, `TriggerBase` определяет две коллекции:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) типа `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) типа `IList<TriggerAction>`

[ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) образце показано, как использовать эти коллекции.

### <a name="more-event-triggers"></a>Другие триггеры событий

[ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) В класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) вызовы библиотеки `ScaleTo` дважды, чтобы увеличивать и уменьшать масштаб. [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) образец использует этот элемент в `EventTrigger` для предоставления визуальной обратной связи при `Button` нажата. Эта анимация типа double можно также с помощью двух действий в коллекции с типом [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) В класс **Xamarin.FormsBook.Toolkit** библиотека определяет настраиваемые shiver действие. [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) в нем демонстрируется его.

[ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) В класс **Xamarin.FormsBook.Toolkit** библиотеки будет ограничен `Entry` элементы и наборы `TextColor` свойство красным, если `Text` свойство не является `double`. [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) в нем демонстрируется его.

### <a name="data-triggers"></a>Триггеры данных

[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) Аналогичен `Trigger` за исключением того, что вместо мониторинг свойство для изменения значения, которые она отслеживает привязку данных. Это позволит свойства в один элемент для воздействия на свойство в другом элементе.

`DataTrigger` определяет три свойства:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) типа `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) типа `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) типа `IList<SetterBase>`

[ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) образец требует [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) библиотеки и наборы цветов имена учащихся на синий или на основе розовый `Sex` свойство:

[![Тройной снимок цвета Пол](images/ch23fg04-small.png "цвета Пол")](images/ch23fg04-large.png#lightbox "Пол цвета")

[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) пример наборов `IsEnabled` свойство `Entry` для `False` Если `Length` свойство `Text` свойство `Entry`равен 0. Обратите внимание, что `Text` свойство инициализируется пустой строкой; по умолчанию это `null`и `DataTrigger` не будут работать правильно.

### <a name="combining-conditions-in-the-multitrigger"></a>Объединение условий в MultiTrigger

[ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger) — Это набор условий. Когда они находятся все `true`, а затем применяются методы задания. Класс определяет два свойства:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) типа `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) типа `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) является абстрактным и имеет два дочерних класса:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), который имеет [ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property) и [ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value) свойства, такие как `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), который имеет [ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding) и [ `Value` ](xref:Xamarin.Forms.BindingCondition.Value) свойства, такие как `DataTrigger`

В [ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) образец, `BoxView` окрашивается только в том случае, если четыре `Switch` элементы включены.

[ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) образце показано, как сделать `BoxView` цвет при *любой* из четырех `Switch` элементы включены. Для этого приложения De Morgan законодательства и обращение всю логику.

Объединение и и или логику, не так просто и обычно требует невидимым `Switch` элементы для промежуточных результатов. [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) образце показано, как `Button` можно включить в том случае, если один из двух `Entry` элементы имеют некоторые текста, введенного в, но не в том случае, если оба они имеют некоторые при вводе текста на.

## <a name="behaviors"></a>поведения

Что-то можно сделать с помощью триггера, также можно сделать с поведением, но поведения всегда требуется класс, производный от [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) и переопределяет следующие два метода:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

Аргумент является элементом, который применяется поведение. Как правило `OnAttachedTo` метод прикрепляет некоторые обработчики событий и `OnDetachingFrom` отсоединяет их. Так как такой класс обычно сохраняет некоторые состояния, он обычно не может использоваться в `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) пример аналогичен **TriggerEntryValidation** за исключением того, что она использует поведение &mdash; [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) в класс [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки.

### <a name="behaviors-with-properties"></a>Поведение с помощью свойства

`Behavior<T>` является производным от `Behavior`, который является производным от `BindableObject`, поэтому привязываемые свойства могут быть определены для поведения. Эти свойства могут быть активны в привязки данных.

Это показано в [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) программа, которая делает использование [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) в класс [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки. `ValidEmailBehavior` имеет только для чтения свойство, используемое и служит в качестве источника привязки.

[ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) примере используется такое же поведение для отображения другого типа индикатора, чтобы сообщить, что адрес электронной почты является допустимым.

[ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) пример представлен вариант предыдущего примера. Использует ButtonGlide `DataTrigger` в сочетании с этого поведения.

### <a name="toggles-and-check-boxes"></a>Переключатели и флажки

Можно инкапсулировать поведение кнопку-переключатель в классе, такие как [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) в [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки, а затем определите все визуальные элементы для переключателя полностью в XAML.

[ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) примере используется `ToggleBehavior` с `DataTrigger` использовать `Label` с две текстовые строки для переключателя.

[ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) образец расширяет эту концепцию, переключение между двумя `FormattedString` объектов.

[ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) В класс **Xamarin.FormsBook.Toolkit** библиотеки является производным от `ContentView`, определяет `IsToggled` свойство и включает в себя `ToggleBehavior` переключателя логика. Это упрощает определение кнопки-переключателя в XAML, как показано [ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) образца.

[ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) включает в себя [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) класс, производный от `ToggleBase` и использует [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)для формирования выключатель, который похож на Xamarin.Forms `Switch`.

Объект [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) в **Xamarin.FormsBook.Toolkit** предоставляет анимации используется для создания анимированных бегунка в [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)образец.

### <a name="responding-to-taps"></a>Реагирование на касания

Одним из недостатков `EventTrigger` является, что вы не можете подключить его к `TapGestureRecognizer` реагировать на касания. Обойти эту проблему заключается цель [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) в [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) примере используется `TapBehavior` для использования более ранней `ShiverAction` для касание `BoxView` элементов.

[ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) примере показано, как начать работу в разметке, инкапсулируя `ShiverView` класса.

### <a name="radio-buttons"></a>Переключатели

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотека также имеет [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) класс для создания переключателей, по которому группируются `string` имя группы.

[ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) программа использует текстовые строки для его переключателя. [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) примере используется `Style` разницу во внешнем виде между checked и unchecked кнопками. [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) образец использует упакованный изображения для его переключателей:

[![Тройной снимок изображения переключателя](images/ch23fg17-small.png "изображения кнопки Radio")](images/ch23fg17-large.png#lightbox "Radio изображения кнопки")

[ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) образец рисует традиционных отображения объектов переключателей с точкой внутри окружности.

### <a name="fades-and-orientation"></a>Затухание и ориентации

Последнему примеру и [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) позволяет переключаться между представлениями разных выделения цветом, с помощью переключателей. Эффект затухания трех представлений в нее с помощью [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) в [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) библиотеки.

Программа также реагирует на изменения в ориентации между книжной и альбомной ориентации, используя [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) в **Xamarin.FormsBook.Toolkit** библиотеки.



## <a name="related-links"></a>Связанные ссылки

- [Глава 23 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Глава 23-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Работа с триггерами](~/xamarin-forms/app-fundamentals/triggers.md)
- [Реакции на событие Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
