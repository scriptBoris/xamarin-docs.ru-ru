---
title: Повторно используемые EventToCommandBehavior
description: Поведения можно использовать для сопоставления команд с помощью элементов управления, которые не были предназначены для взаимодействия с помощью команд. В этой статье демонстрируется создание и использование поведения Xamarin.Forms для вызова команды в том случае, когда создается событие.
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 8bf8f86cf708806d1c17b3fe4eda0755f98fd646
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563190"
---
# <a name="reusable-eventtocommandbehavior"></a>Повторно используемые EventToCommandBehavior

_Поведения можно использовать для сопоставления команд с помощью элементов управления, которые не были предназначены для взаимодействия с помощью команд. В этой статье демонстрируется создание и использование поведения Xamarin.Forms для вызова команды в том случае, когда создается событие._

## <a name="overview"></a>Обзор

`EventToCommandBehavior` Класс является многократно используемых пользовательское поведение Xamarin.Forms и выполняет команды в ответ на *любой* события Click. По умолчанию аргументы события для события передаются в команду, а при необходимости может быть преобразован с [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) реализации.

Следующие свойства поведение должно быть задано в соответствии с поведением:

- **EventName** — имя события поведение прослушивает.
- **Команда** — `ICommand` для выполнения. Поведение ожидает найти `ICommand` экземпляра [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из присоединенного элемента управления, которое может быть унаследовано от родительского элемента.

Также можно задать следующие свойства необязательное поведение:

- **CommandParameter** — `object` , передаются в команду.
- **Преобразователь** — [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) реализация, которая изменит формат данных события, аргумент, передаваемый между *источника* и *целевой*обработчиком привязки.

> [!NOTE]
> `EventToCommandBehavior` — Это пользовательский класс, который может находиться в [пример поведения EventToCommand](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/), и не является частью Xamarin.Forms.

## <a name="creating-the-behavior"></a>Создание поведение

`EventToCommandBehavior` Класс является производным от `BehaviorBase<T>` класс, который в свою очередь является производным от [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) класса. Цель `BehaviorBase<T>` класса заключается в предоставлении базовый класс для любого реакции на событие Xamarin.Forms, требующих [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) поведения будет присвоено присоединенного элемента управления. Это гарантирует, что поведение можно привязать к и выполняться `ICommand` определяется `Command` свойства при использовании поведения.

`BehaviorBase<T>` Класс предоставляет переопределяемый [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) метод, который задает [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) поведение и переопределяемым [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))метод, который очищает `BindingContext`. Кроме того, класс хранит ссылку на присоединенного элемента управления в `AssociatedObject` свойство.

### <a name="implementing-bindable-properties"></a>Реализация привязываемые свойства

`EventToCommandBehavior` Класс определяет четыре [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) экземпляров, выполняемых пользователем определенные команды, когда создается событие. Эти свойства отображаются в следующем примере кода:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

При `EventToCommandBehavior` исчерпания класс `Command` свойство должно иметь значение данных, привязанных к `ICommand` для выполнения в ответ на события Click, который определен в `EventName` свойство. Поведение ожидают найти `ICommand` на [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) из присоединенного элемента управления.

По умолчанию аргументы события для события передаются в команду. Эти данные можно при необходимости преобразовать при передаче между *источника* и *целевой* обработчиком привязки, указав [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) реализацию как `Converter` значение свойства. Кроме того, параметр может передаваться в команду, указав `CommandParameter` значение свойства.

### <a name="implementing-the-overrides"></a>Реализация переопределения

`EventToCommandBehavior` Класса переопределения [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) и [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) методы `BehaviorBase<T>` класса, как показано в следующем примере кода:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) Метод выполняет программу установки с помощью вызова `RegisterEvent` метод, передавая значение `EventName` свойство в качестве параметра. [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) Метод выполняет очистку, вызвав `DeregisterEvent` метод, передавая значение `EventName` свойство в качестве параметра.

### <a name="implementing-the-behavior-functionality"></a>Реализация функциональных возможностей поведения

Поведение предназначено для выполнения команды, определяется `Command` свойства в ответ на события Click, который определяется `EventName` свойство. В следующем примере кода показано поведение основные функциональные возможности:

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

`RegisterEvent` Метод выполняется в ответ на `EventToCommandBehavior` присоединяемый к элементу управления и он получает значение `EventName` свойство в качестве параметра. Затем пытается найти события, определенного в методе `EventName` свойства, вложенного элемента управления. Условии, что событие может быть найден, `OnEvent` регистрации метода способом обработчик для события.

`OnEvent` Метод выполняется в ответ на события Click, который определен в `EventName` свойство. При условии, что `Command` свойство ссылается на допустимый `ICommand`, метод пытается получить параметр для передачи `ICommand` следующим образом:

- Если `CommandParameter` свойство определяет параметр, он извлекается.
- В противном случае, если `Converter` определяет свойство [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) реализации преобразователя, выполняется и преобразует данные аргумент события, передаваемый между *источника* и *целевой* обработчиком привязки.
- В противном случае аргументы события считаются параметра.

Данные, привязываемые `ICommand` выполняется, передавая их в команду, при условии, что в параметре [ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object)) возвращает метод `true`.

Несмотря на то, что не отображается, `EventToCommandBehavior` также включает в себя `DeregisterEvent` метод, который выполняется путем [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) метод. `DeregisterEvent` Метод используется для поиска и отменить регистрацию события, определенного в `EventName` свойство, для очистки памяти потенциальных утечек.

## <a name="consuming-the-behavior"></a>Использование поведения

`EventToCommandBehavior` Класса можно подключить к [ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) коллекцию элементов управления, как показано в следующем примере кода XAML:

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

Эквивалентный код на языке C# показан в следующем примере:

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

`Command` Свойство поведения является привязанным к данным `OutputAgeCommand` свойства связанного ViewModel, хотя `Converter` свойству `SelectedItemConverter` экземпляр, который возвращает [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)из [ `ListView` ](xref:Xamarin.Forms.ListView) из [ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs).

Во время выполнения поведение будет отвечать на действия с элементом управления. При выборе элемента в [ `ListView` ](xref:Xamarin.Forms.ListView), [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) событие будет срабатывать, которой будет выполнен `OutputAgeCommand` в ViewModel. В свою очередь, при этом обновляется ViewModel `SelectedItemText` свойство, [ `Label` ](xref:Xamarin.Forms.Label) привязывает to, как показано на следующем снимке экрана:

[![](event-to-command-behavior-images/screenshots-sml.png "Пример приложения с EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "пример приложения с EventToCommandBehavior")

Преимуществом использования это поведение для выполнения команды в том случае, когда создается событие, является, что команды могут быть связаны с элементами управления, которые не были предназначены для взаимодействия с помощью команд. Кроме того это удаляет стереотипного кода обработки событий из файлы с выделенным кодом.

## <a name="summary"></a>Сводка

В этой статье показано, с помощью Xamarin.Forms поведения для вызова команды в том случае, когда создается событие. Поведения можно использовать для сопоставления команд с помощью элементов управления, которые не были предназначены для взаимодействия с помощью команд.

## <a name="related-links"></a>Связанные ссылки

- [Поведение EventToCommand (пример)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [Поведение](xref:Xamarin.Forms.Behavior)
- [Поведение&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
