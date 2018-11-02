---
title: Триггеры Xamarin.Forms
description: В этой статье объясняется, как использовать триггеры Xamarin.Forms реагировать на изменения пользовательского интерфейса с помощью XAML. Триггеры позволяют указать действия декларативно в XAML, изменить внешний вид элементов управления на основе событий или изменения свойств.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675218"
---
# <a name="xamarinforms-triggers"></a>Триггеры Xamarin.Forms

Триггеры позволяют указать действия декларативно в XAML, изменить внешний вид элементов управления на основе событий или изменения свойств.

Можно назначить триггер непосредственно к элементу управления, или добавить его в словарь ресурсов страницам или уровня приложения для применения к нескольким элементам управления.

Существует четыре типа триггера:

* [Триггер свойства](#property) -происходит, когда свойство элемента управления присваивается определенное значение.

* [Триггер данных](#data) - использует данные привязки к триггеру, в соответствии со свойствами другого элемента управления.

* [Триггер события](#event) -происходит при возникновении события в элементе управления.

* [Триггер с несколькими](#multi) -позволяет задать перед выполнением действия сразу нескольких условий триггера.

<a name="property" />

## <a name="property-triggers"></a>Триггеры свойств

Простой триггер может быть выражено исключительно в XAML, добавление `Trigger` коллекции активирует элемент к элементу управления.
В этом примере показано триггер, который изменяет `Entry` цвет фона при получении фокуса:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Ниже приведены важные части объявлении триггера.

* **TargetType** -тип элемента управления, к которому применяется триггер.

* **Свойство** -свойства элемента управления, за которым ведется наблюдение.

* **Значение** -значение, при его возникновении для наблюдаемого свойства, возникает при активации триггера.

* **Метод задания** -коллекция `Setter` могут быть добавлены элементы, и при удовлетворении условия активации. Необходимо указать `Property` и `Value` для задания.

* **EnterActions и ExitActions** (не показан) записываются в коде и может использоваться в дополнение к (или instead of) `Setter` элементов. Они являются [описанных ниже](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Применение триггера с помощью стиля

Триггеры также могут добавляться к `Style` объявление элемента управления, на странице, или приложение `ResourceDictionary`. В этом примере объявляется неявный стиль (т. е. не `Key` имеет значение) то есть будет применена ко всем `Entry` элементов управления страницы.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Триггеры данных

Триггеры данных использовать привязку данных для отслеживания другого элемента управления, чтобы вызвать `Setter`s к вызову. Вместо `Property` атрибут в триггер свойства, установите `Binding` атрибут для отслеживания для указанного значения.

В следующем примере используется синтаксис привязки данных `{Binding Source={x:Reference entry}, Path=Text.Length}`
то есть, как мы будем называть свойства другого элемента управления. Если длина `entry` равно нулю, активации триггера. В этом примере триггер отключает кнопку, если ввод был пустым.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Совет: при оценке `Path=Text.Length` всегда предоставлять значение по умолчанию для целевого свойства (например) `Text=""`), так как в противном случае будет `null` и триггер не будет работать, как и ожидается.

Помимо указания `Setter`s, вы также можете предоставить [ `EnterActions` и `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Триггеры событий

`EventTrigger` Элемента требуется только `Event` свойства, такие как `"Clicked"` в следующем примере.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Обратите внимание, что не `Setter` элементы, но вместо этого ссылку на класс, определяемый `local:NumericValidationTriggerAction` требующий `xmlns:local` объявляться на странице в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

Сам класс реализует `TriggerAction` то есть должен предоставлять переопределение для `Invoke` метод, вызываемый, когда происходит событие триггера.

Это реализация действия триггера следует:

* Реализация универсального `TriggerAction<T>` класса, соответствующего типа элемента управления, триггер будет применяться для универсального параметра. Можно использовать порождает обычное поле ввода, такие как `VisualElement` для записи действий триггера, которые работают с различными элементами управления, или указать тип элемента управления, например `Entry`.

* Переопределить `Invoke` - это вызывается при каждом выполнении условий триггера.

* При необходимости предоставлять свойства, которые можно задать в XAML при объявлении триггера (например, `Anchor`, `Scale`, и `Length` в этом примере).

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

Свойства, предоставляемые действие триггера можно задать в объявлении XAML следующим образом:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Будьте внимательны при совместном использовании триггеров в `ResourceDictionary`, один экземпляр будут совместно использовать элементы управления, поэтому любое состояние, которое настраивается один раз будет применяться к их все.

Обратите внимание, что триггеры событий не поддерживают `EnterActions` и `ExitActions` [описанных ниже](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Несколько триггеров

Объект `MultiTrigger` будет выглядеть `Trigger` или `DataTrigger` за исключением того, может существовать несколько условий. Все условия должны иметь значение true, если перед `Setter`активируются s.

Ниже приведен пример триггера для кнопки, который привязывается к двух различных входных параметров (`email` и `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>

  <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

`Conditions` Коллекции может также содержать `PropertyCondition` элементы следующим образом:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Создание триггера с несколькими «требуются все»

Триггер с несколькими обновляет его элемента только в том случае, если выполняются все условия. Тестирование для «все значения длины поля ноль» (например, страница входа, где все входные данные должны быть завершены) непросто, поскольку требуется, чтобы условие «где Text.Length > 0", но это не могут быть выражены в XAML.

Это можно сделать с помощью `IValueConverter`. Приведенный ниже преобразований код преобразователя `Text.Length` привязки в `bool` , указывающее, является ли поле пустым, или нет:

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Чтобы использовать этот преобразователь в триггер с несколькими, сначала добавьте его в словаре ресурсов страницы (вместе с пользовательской `xmlns:local` определение пространства имен):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

Ниже приведен XAML. Обратите внимание на следующие отличия из первого примера триггера с несколькими:

* Кнопка имеет `IsEnabled="false"` значение по умолчанию.
* Условия запуска с несколькими использовать преобразователь для включения `Text.Length` значение в `boolean`.
* Если выполняются все условия `true`, метод задания делает кнопки `IsEnabled` свойство `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Эти снимки экрана показаны различия между в двух различных триггера примерах выше. В верхней части окна ввода текста, только в одной `Entry` достаточно, чтобы включить **Сохранить** кнопки.
В нижней части окна **входа** кнопка остается неактивной, пока оба поля содержат данные.

![](triggers-images/multi-requireall.png "Примеры multiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions и ExitActions

Другой способ реализации изменений при возникновении триггер, — путем добавления `EnterActions` и `ExitActions` коллекции и указав `TriggerAction<T>` реализаций.

Вы можете предоставить *оба* `EnterActions` и `ExitActions` производительны `Setter`s в триггере, но помните, `Setter`s вызываются сразу же (они не ожидают `EnterAction` или `ExitAction` для Завершите). Кроме того, можно выполнить все, что в коде и не используйте `Setter`s вообще.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
                        <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Как всегда, если ссылка на класс задается в XAML следует объявить пространство имен, например `xmlns:local` следующим образом:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

`FadeTriggerAction` Код показан ниже:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

Примечание: `EnterActions` и `ExitActions` игнорируются **триггеров событий**.



## <a name="related-links"></a>Связанные ссылки

- [Пример триггеров](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Документация по API Xamarin.Forms](xref:Xamarin.Forms.TriggerAction`1)
