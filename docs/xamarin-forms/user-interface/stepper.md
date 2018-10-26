---
title: Несопоставимого Xamarin.Forms
description: Несопоставимого Xamarin.Forms позволяет пользователю выбрать числовое значение из диапазона значений. Он состоит из двух кнопок с названием «минус» и "плюс". Управление две кнопки изменяется постепенно выбранное значение.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 53485d0d948f31b69f74b6933d05c14f69fa64c0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111580"
---
# <a name="xamarinforms-stepper"></a>Несопоставимого Xamarin.Forms

_Используйте средство организации пошагового режима выбора числовое значение из диапазона значений._

Xamarin.Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper) состоит из двух кнопок с названием «минус» и "плюс". Эти кнопки могут управляться пользователю выбрать постепенно `double` значение из диапазона значений.

[ `Stepper` ](xref:Xamarin.Forms.Stepper) Определены четыре свойства типа `double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) — Это сумма, чтобы изменить выбранное значение by, со значением по умолчанию 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) — Это минимальная диапазон со значением по умолчанию 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) — Это максимальное значение диапазона, со значением по умолчанию 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) имеет значение несопоставимого, что допустимы значения от `Minimum` и `Maximum` и имеет значение по умолчанию 0.

Все эти свойства, обеспечиваются [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) объектов. [ `Value` ](xref:Xamarin.Forms.Stepper.Value) Свойство имеет режим привязки по умолчанию [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), что означает, что это подходит в качестве источника привязки в приложение, использующее [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) архитектуры.

> [!WARNING]
> На внутреннем уровне [ `Stepper` ](xref:Xamarin.Forms.Stepper) гарантирует, что [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) — меньше, чем [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Если `Minimum` или `Maximum` когда-либо заданы таким образом, чтобы `Minimum` — не меньше `Maximum`, возникает исключение. Дополнительные сведения о параметр `Minimum` и `Maximum` свойства, см. в разделе [меры предосторожности](#precautions) раздел.

[ `Stepper` ](xref:Xamarin.Forms.Stepper) Приводит [ `Value` ](xref:Xamarin.Forms.Stepper.Value) свойство, чтобы оно было между [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) и [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)включительно. Если `Minimum` свойству присваивается значение больше, чем `Value` свойство, `Stepper` задает `Value` свойства `Minimum`. Аналогично Если `Maximum` присвоено значение меньше, чем `Value`, затем `Stepper` задает `Value` свойства `Maximum`.

[`Stepper`](xref:Xamarin.Forms.Stepper) Определяет [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) событие, возникающее, когда [ `Value` ](xref:Xamarin.Forms.Stepper.Value) изменения, либо с помощью операции пользователя из `Stepper` или когда приложение задает `Value` свойство напрямую. Объект `ValueChanged` событие также инициируется, когда `Value` приводится как описано в предыдущем абзаце.

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) Объект, который прилагается к [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) событий имеет два свойства: тип `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) и [ `NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Во время события, значение `NewValue` совпадает со значением [ `Value` ](xref:Xamarin.Forms.Stepper.Value) свойство [ `Stepper` ](xref:Xamarin.Forms.Stepper) объекта.

## <a name="basic-stepper-code-and-markup"></a>Основные несопоставимого кода и разметки

[ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) образец содержит три страницы, которые функционально идентичны, но реализуются по-разному. На первой странице используется только C# код, вторая использует XAML с помощью обработчика событий в коде и третий избежать обработчик событий с помощью привязки данных в файле XAML.

### <a name="creating-a-stepper-in-code"></a>Создание несопоставимого в коде

**Базовый код несопоставимого** странице в [ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) примере показано, как создать [ `Stepper` ](xref:Xamarin.Forms.Stepper) и два [ `Label` ](xref:Xamarin.Forms.Label) объектов в коде:

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

[ `Stepper` ](xref:Xamarin.Forms.Stepper) Инициализируется иметь [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) свойство 360 и [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) свойство 30. Управление `Stepper` изменяет выбранное значение между постепенно [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) для `Maximum` на основе значения из `Increment` свойство. [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) Обработчик `Stepper` использует [ `Value` ](xref:Xamarin.Forms.Stepper.Value) свойство `stepper` объект для задания [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) Свойство первого [ `Label` ](xref:Xamarin.Forms.Label) и использует `string.Format` метод с `NewValue` свойство в аргументах события, чтобы задать [ `Text` ](xref:Xamarin.Forms.Label.Text) свойство второй `Label`. Эти два подхода, чтобы получить текущее значение `Stepper` являются взаимозаменяемыми.

Ниже показаны снимки экрана **базовый код несопоставимого** страницы:

[![Основные несопоставимого кода](stepper-images/basic-stepper-code.png "основные несопоставимого кода")](stepper-images/basic-stepper-code-large.png#lightbox)

Второй [ `Label` ](xref:Xamarin.Forms.Label) отображается текст «(неинициализированные)» до [ `Stepper` ](xref:Xamarin.Forms.Stepper) будут обрабатывать, который вызывает первый [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) событий Активируемый.

### <a name="creating-a-stepper-in-xaml"></a>Создание несопоставимого в XAML

**Базовые XAML несопоставимого** страница функционально является таким же, как **базовый код несопоставимого** но реализуется, главным образом в XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

Файл кода содержит обработчик [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) событий:

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

Можно также для обработчика событий для получения [ `Stepper` ](xref:Xamarin.Forms.Stepper) , он генерирует события с помощью `sender` аргумент. [ `Value` ](xref:Xamarin.Forms.Stepper.Value) Свойство содержит текущее значение:

```csharp
double value = ((Stepper)sender).Value;
```

Если [ `Stepper` ](xref:Xamarin.Forms.Stepper) объекта были присваивается имя в файле XAML с `x:Name` атрибут (например, «шаг»), а затем обработчик событий может ссылаться на этот объект напрямую:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Привязка несопоставимого данных

**Основных привязок несопоставимого** странице показано, как написать практически эквивалентные приложение, которое устраняет [ `Value` ](xref:Xamarin.Forms.Stepper.Value) обработчик событий с помощью [привязки данных](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) Свойства первого [ `Label` ](xref:Xamarin.Forms.Label) привязан к [ `Value` ](xref:Xamarin.Forms.Stepper.Value) свойство [ `Stepper` ](xref:Xamarin.Forms.Stepper), так как [ `Text` ](xref:Xamarin.Forms.Label.Text) свойство второго `Label` с `StringFormat` спецификации. **Основных привязок несопоставимого** странице функций немного по-разному на двух предыдущих страницах: при первом отображении страницы, второй `Label` отображает текстовую строку со значением. Это является преимуществом с использованием привязки данных. Для отображения текста без привязки данных, необходимо специально инициализировать `Text` свойство `Label` или имитировать срабатывание [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) событие путем вызова обработчика событий из конструктора класса .

## <a name="precautions"></a>Меры предосторожности

Значение [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) свойства всегда должно быть меньше, чем значение [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) свойство. В следующем примере кода причины фрагмент [ `Stepper` ](xref:Xamarin.Forms.Stepper) для вызова исключения:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C# Компилятор создает код, который задает эти два свойства в последовательности, и когда [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) свойство имеет значение 180, больше, чем значение по умолчанию [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) значение 100. В этом случае исключения можно избежать, задав `Maximum` свойства первого:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Установка [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) до 360 проблема не возникает потому, что больше, чем значение по умолчанию [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) значение 0. Когда `Minimum` не установлен, значение меньше, чем `Maximum` значение 360.

Та же проблема существует в XAML. Задайте свойства в порядке, который гарантирует, что [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) был всегда больше, чем `Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Можно задать [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) и [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) значения для отрицательных чисел, но только в порядке, где `Minimum` — всегда меньше, чем `Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[ `Value` ](xref:Xamarin.Forms.Stepper.Value) Свойство всегда имеет больше или равно [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) значение и меньше или равно [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Если `Value` задано значение за пределами этого диапазона, значения будут приведены к находиться в диапазоне, но исключения не было. Например, этот код будет *не* исключение:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Вместо этого [ `Value` ](xref:Xamarin.Forms.Stepper.Value) приводится к типу [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) значение 100.

Ниже приведен фрагмент кода, показанный выше.

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Когда [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) имеет значение 180, затем [ `Value` ](xref:Xamarin.Forms.Stepper.Value) задается значение 180.

Если [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) во время был присоединен обработчик событий, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) приводится отличное значение по умолчанию 0, то `ValueChanged` события. Ниже приведен фрагмент XAML.

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Когда [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) имеет значение 180, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) задается значение 180 и [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) события. Это может произойти, прежде чем остальной части страницы был построен, и обработчик может пытаться ссылаться на другие элементы на странице, которые еще не созданы. Может потребоваться добавить код для `ValueChanged` обработчик, который проверяет наличие `null` значений других элементов на странице. Или можно задать `ValueChanged` обработчик событий после [ `Stepper` ](xref:Xamarin.Forms.Stepper) были инициализированы значениями.

## <a name="related-links"></a>Связанные ссылки

- [Пример демонстрации несопоставимого](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)
- [Несопоставимого API](xref:Xamarin.Forms.Stepper)
