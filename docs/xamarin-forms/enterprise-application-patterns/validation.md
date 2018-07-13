---
title: Проверка в корпоративных приложениях
description: В данной главе объясняется, как мобильное приложение eShopOnContainers выполняет проверку вводимых пользователем данных. Это включает в себя указания правил проверки, активируя проверку и отображение ошибок проверки.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 2b4be17e3c96ee223433b435a7b1011eafa8e9db
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995831"
---
# <a name="validation-in-enterprise-apps"></a>Проверка в корпоративных приложениях

Любое приложение, которое принимает входные данные от пользователей, следует убедиться, что ввод был допустимым. Приложения например, проверьте наличие входных данных, который содержит только символы определенного диапазона, имеет определенную длину или совпадает с определенного формата. Без проверки пользователь может передать данные, которые вызывают его сбой. Проверка применяет бизнес-правила и не позволяет злоумышленнику вставку вредоносных данных.

В контексте объекта модели ViewModel (MVVM) шаблона, модель представления или модели часто нужно будет выполнить проверку данных и сигнализировать об ошибок проверки в представление, таким образом, пользователь может исправить их. В мобильном приложении eShopOnContainers выполняет синхронные клиентскую проверку свойства модели представления и уведомляет пользователя об ошибках проверки, выделив элемент управления, который содержит недопустимые данные и отображения сообщений об ошибках, информирующие пользователя из почему данные недопустимы. Рис. 6-1 показаны классы, включенные при выполнении проверки в мобильном приложении eShopOnContainers.

[![](validation-images/validation.png "Классы проверки в мобильном приложении eShopOnContainers")](validation-images/validation-large.png#lightbox "классы проверки в мобильном приложении eShopOnContainers")

**Рис. 6-1**: классы проверки в мобильном приложении eShopOnContainers

Просмотр свойств модели, требующих проверки имеют тип `ValidatableObject<T>`и каждый `ValidatableObject<T>` экземпляр имеет правила проверки, добавленные его `Validations` свойство. Проверка вызывается из модели представления путем вызова `Validate` метод `ValidatableObject<T>` экземпляр, который извлекает проверки правил и выполняет их от `ValidatableObject<T>` `Value` свойство. Ошибки проверки, помещаются в `Errors` свойство `ValidatableObject<T>` экземпляр и `IsValid` свойство `ValidatableObject<T>` экземпляр обновляется, чтобы указать, успешно ли выполнена проверка.

Уведомления об изменении свойства обеспечивается `ExtendedBindableObject` класса и поэтому [ `Entry` ](xref:Xamarin.Forms.Entry) управления можно привязать к `IsValid` свойство `ValidatableObject<T>` экземпляра в класс модели представления, чтобы получать уведомления о том, следует ли введенные данные является допустимым.

## <a name="specifying-validation-rules"></a>Указание правил проверки

Правила проверки настраиваются путем создания класса, производного от `IValidationRule<T>` интерфейс, который показан в следующем примере кода:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Этот интерфейс определяет, что класс правило проверки должен предоставить `boolean` `Check` метод, который используется для выполнения проверки, и `ValidationMessage` свойства, значение которого представляет собой сообщение об ошибке проверки, которое будет отображаться, если Проверка не пройдена.

В следующем коде показано в примере `IsNotNullOrEmptyRule<T>` правило проверки, который используется для проверки имени пользователя и пароль, введенный пользователем `LoginView` при использовании макеты служб в мобильном приложении eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check` Возвращает `boolean` , указывающее, является ли аргумент значение `null`, пуст или состоит только из пробельных символов.

Несмотря на то, что не используется в мобильном приложении eShopOnContainers, в следующем примере кода показано правило проверки для проверки адреса электронной почты:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check` Возвращает метод `boolean` , указывающее, является ли аргумент значение допустимый адрес электронной почты. Это достигается путем поиска значение аргумента для первого экземпляра шаблон регулярного выражения, указанного в `Regex` конструктор. Ли находится шаблон регулярного выражения во входной строке можно определить с помощью проверки значения `Match` объекта `Success` свойство.

> [!NOTE]
> Проверка свойства иногда может включать в себя зависимые свойства. Пример зависимые свойства — когда набор допустимых значений для типа свойства зависит от определенное значение, которое было задано в свойстве б. Чтобы проверить, что значение свойства типа является одним из допустимых значений будет включать в себя извлечение значения свойства б. Кроме того при изменении значения свойства B, свойства типа необходимо быть проверено повторно.

## <a name="adding-validation-rules-to-a-property"></a>Добавление правил проверки к свойству

В мобильном приложении eShopOnContainers, объявляются свойства модели представления, требующих проверки типа `ValidatableObject<T>`, где `T` — это тип данных для проверки. В следующем примере кода показан пример двух свойств:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Чтобы выполнялась проверка, необходимо добавить правила проверки `Validations` коллекцию каждого `ValidatableObject<T>` экземпляра, как показано в следующем примере кода:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Этот метод добавляет `IsNotNullOrEmptyRule<T>` для правила проверки `Validations` коллекцию каждого `ValidatableObject<T>` экземпляр, указав значения для правила проверки `ValidationMessage` свойство, которое указывает сообщение об ошибке проверки, которое будет отображаться, если Проверка не пройдена.

## <a name="triggering-validation"></a>Активируя проверку

Способ проверки в мобильном приложении eShopOnContainers можно вручную запустить проверку свойства и автоматически проверки триггера при изменении свойства.

### <a name="triggering-validation-manually"></a>Активации проверки вручную

Проверка может инициироваться вручную для свойства модели представления. Например, это происходит в мобильном приложении eShopOnContainers, когда пользователь касается **входа** кнопку `LoginView`, при использовании службы имитации. Она вызывает делегат `MockSignInAsync` метод в `LoginViewModel`, который вызывает проверку, выполнив `Validate` метод, который показан в следующем примере кода:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

`Validate` Метод выполняет проверку имени пользователя и пароль, введенный пользователем `LoginView`, путем вызова метода Validate в каждом `ValidatableObject<T>` экземпляра. В следующем примере кода показан метод Validate из `ValidatableObject<T>` класса:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Этот метод очищает `Errors` коллекции, а затем извлекает какой-либо проверки правил, которые были добавлены в объект `Validations` коллекции. `Check` Метод для каждого полученного условие выполняется и `ValidationMessage` добавляется значение свойства для любого правила проверки, который не удается проверить данные `Errors` коллекцию `ValidatableObject<T>` экземпляра. Наконец `IsValid` свойство установлено, а его значение возвращается в вызывающий метод, указывающее, успешно ли выполнена проверка.

### <a name="triggering-validation-when-properties-change"></a>Активируя проверку при изменении свойств

Проверка также может запускаться при каждом изменении связанного свойства. Например, когда двухстороннюю привязку в `LoginView` задает `UserName` или `Password` активируется свойства проверки. В следующем примере кода показано, как это происходит:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

[ `Entry` ](xref:Xamarin.Forms.Entry) Привязан элемент управления `UserName.Value` свойство `ValidatableObject<T>` экземпляра и элемента управления `Behaviors` коллекция имеет `EventToCommandBehavior` экземпляра, добавить к ним. Это поведение выполняет `ValidateUserNameCommand` в ответ на [`TextChanged`] запуск события на `Entry`, которое вызывается, когда текст в `Entry` изменения. В свою очередь `ValidateUserNameCommand` выполняет делегат `ValidateUserName` метод, который выполняет `Validate` метод `ValidatableObject<T>` экземпляра. Таким образом, каждый раз пользователь вводит символ в `Entry` выполняется элемента управления для имени пользователя, проверка введенных данных.

Дополнительные сведения о поведениях см. в разделе [реализации поведения](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Отображение ошибок проверки

В мобильном приложении eShopOnContainers уведомляет пользователя об ошибках проверки выделив элемент управления, который содержит недопустимые данные с красной линией, и, отображая сообщение об ошибке, сообщающее, почему данные недопустимы ниже управления, содержащего недопустимые данные. При исправляется недопустимые данные, строки меняется на черный, и удаляется сообщение об ошибке. Рис. 6-2 показано LoginView в мобильном приложении eShopOnContainers, при наличии ошибок проверки.

![](validation-images/validation-login.png "Отображение ошибок проверки во время входа")

**Рис. 6-2:** отображение ошибок проверки во время входа

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Выделите элемент управления, который содержит недопустимые данные.

`LineColorBehavior` Вложенное поведение, используемым для выделения [ `Entry` ](xref:Xamarin.Forms.Entry) элементов управления, где произошли ошибки проверки. В следующем примере кода показано как `LineColorBehavior` прикреплено поведение присоединенного `Entry` управления:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[ `Entry` ](xref:Xamarin.Forms.Entry) Управления использует явный стиль, который показан в следующем примере кода:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Этот стиль устанавливает `ApplyLineColor` и `LineColor` вложенные свойства `LineColorBehavior` подключить поведение на [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления. Дополнительные сведения о стилях см. в разделе [стили](~/xamarin-forms/user-interface/styles/index.md).

Если значение `ApplyLineColor` вложенное свойство является набор или изменения, `LineColorBehavior` выполняет вложенное поведение `OnApplyLineColorChanged` метод, который показан в следующем примере кода:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Параметры для этого метода укажите экземпляр элемента управления, к которой подключен поведение и старые и новые значения `ApplyLineColor` вложенного свойства зависимостей. `EntryLineColorEffect` Класс добавляется к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции Если `ApplyLineColor` присоединенное свойство `true`, в противном случае он удаляется из элемента управления `Effects` коллекции. Дополнительные сведения о поведениях см. в разделе [реализации поведения](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

`EntryLineColorEffect` Подклассы [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) класса и показано в следующем примере кода:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) Класс представляет эффект независимо от платформы, который создает оболочку для внутреннего эффект, зависит от платформы. Это упрощает процесс удаления эффекта, так как нет сведений о типе доступа во время компиляции для создания эффекта от платформы. `EntryLineColorEffect` Вызывает конструктор базового класса, передав в качестве параметра, состоящий из объединение имени группы разрешения и уникальный идентификатор, указанный для каждого класса эффект от платформы.

В следующем коде показано в примере `eShopOnContainers.EntryLineColorEffect` реализацию для операций ввода-вывода:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

`OnAttached` Метод получает собственный элемент управления для Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) управления и обновляет цвет линии, вызвав `UpdateLineColor` метод. `OnElementPropertyChanged` Переопределение реагирует на изменения может быть привязано на `Entry` управления, обновив цвет линии, если вложенный `LineColor` изменения свойств или [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) свойство `Entry`изменения. Дополнительные сведения об эффектах см. в разделе [эффекты](~/xamarin-forms/app-fundamentals/effects/index.md).

При вводе допустимые данные в [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления, он будет применяться черная линия к нижней части элемента управления, чтобы указать, что отсутствуют ошибки проверки. Рис. 6-3 показан пример этого.

![](validation-images/validation-blackline.png "Указывающее, ошибки проверки не черная линия")

**Рис. 6-3**: черная линия, указывающее, ошибки проверки не

[ `Entry` ](xref:Xamarin.Forms.Entry) Элемент управления также имеет [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) добавляемый его [ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers) коллекции. В следующем коде показано в примере `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Это [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) мониторы `UserName.IsValid` свойство и если это значение становится `false`, он выполняет [ `Setter` ](xref:Xamarin.Forms.Setter), какие изменения `LineColor` подключен Свойство `LineColorBehavior` подключить поведение к красному. Рис. 6-4 показан пример этого.

![](validation-images/validation-redline.png "Красная линия, указывающих на ошибку проверки")

**Рис. 6-4**: красная линия, указывающих на ошибку проверки

В строке [ `Entry` ](xref:Xamarin.Forms.Entry) элемент управления останется красным, а введенные данные недопустимы, в противном случае он изменится на черный, чтобы указать, что введенные данные является допустимым.

Дополнительные сведения о триггерах см. в разделе [триггеры](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Отображение сообщения об ошибках

Пользовательский Интерфейс отображает сообщения об ошибках проверки в элементы управления Label ниже каждый элемент управления, данные которых не прошло проверку. В следующем коде показано в примере [ `Label` ](xref:Xamarin.Forms.Label) , отображающий сообщение об ошибке проверки, если пользователь не ввел допустимое имя пользователя:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Каждый [ `Label` ](xref:Xamarin.Forms.Label) привязывается к `Errors` свойство объекта модели представления, который проверяется. `Errors` Предоставляется свойство `ValidatableObject<T>` класса и имеет тип `List<string>`. Так как `Errors` свойство может содержать несколько ошибок проверки, `FirstValidationErrorConverter` экземпляр используется для извлечения из коллекции для отображения первой ошибки.

## <a name="summary"></a>Сводка

В мобильном приложении eShopOnContainers выполняет синхронные клиентскую проверку свойства модели представления и уведомляет пользователя об ошибках проверки, выделив элемент управления, который содержит недопустимые данные и отображения сообщений об ошибках, информирующие пользователя Почему данные недопустимы.

Просмотр свойств модели, требующих проверки имеют тип `ValidatableObject<T>`и каждый `ValidatableObject<T>` экземпляр имеет правила проверки, добавленные его `Validations` свойство. Проверка вызывается из модели представления путем вызова `Validate` метод `ValidatableObject<T>` экземпляр, который извлекает проверки правил и выполняет их от `ValidatableObject<T>` `Value` свойство. Ошибки проверки, помещаются в `Errors` свойство `ValidatableObject<T>`экземпляр и `IsValid` свойство `ValidatableObject<T>` экземпляр обновляется, чтобы указать, успешно ли выполнена проверка.


## <a name="related-links"></a>Связанные ссылки

- [Скачайте электронную книгу (2 МБ в формате PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (пример)](https://github.com/dotnet-architecture/eShopOnContainers)
